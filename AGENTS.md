# AGENTS.md: Media Bar Enhanced (Altffour fork)

Guide for agents working in this repo. Last checked: 2026-10-02.

Repo: `EclipseKnight/jellyfin-plugin-media-bar-enhanced` (the owner's fork). The main Altffour guide is `/var/www/altffour-jellyfin-frontend/AGENTS.md`.

## What it is

- A Jellyfin server plugin named **Media Bar Enhanced** (GUID `d7e11d57-819b-4bdd-a88d-53c5f5560225`).
- It adds a slideshow banner (the "media bar") to the top of the Jellyfin **web** home page. Each slide shows the backdrop and logo, title, ratings, year and plot, plus Play, Info and Favorite buttons.
- Trailers can play as the slide's background video. Sources are local trailers, theme videos, or YouTube (via youtube-nocookie, with SponsorBlock skipping intros and outros).
- Other features: custom item lists, seasonal lists, sorting and limits, keyboard controls, and optional per-device settings for users.
- It only works in clients that run jellyfin-web: browsers, Jellyfin Media Player, and the Android/iOS apps. Native TV apps (Android TV, Roku, Swiftfin, Kodi) don't show it.

### Fork history

- MakD/Jellyfin-Media-Bar (the original script), then the M0RPH3US slideshow v4.0.1 (see the JS file header), then **CodeDevMLH/jellyfin-plugin-media-bar-enhanced** (git remote `upstream`), then this fork (git remote `origin`).
- The fork split from upstream after 1.7.0.0 (`b511221`, 2026-03-06).
- Fork-only changes:
  - 1.7.1.0: fixes the Continue Watching row background on the home page.
  - `9cc5127`: sets the slides container height to 100%. Committed but not released yet.
- Upstream has moved on to 3.9.0.0 (2026-10-03, still targetAbi 10.11). The local `upstream/main` ref is stale, so run `git fetch upstream` before comparing.

## Layout

```
Jellyfin.Plugin.MediaBarEnhanced/
  MediaBarEnhancedPlugin.cs        plugin entry; injects or removes the web tags on startup and when IsEnabled changes
  ScriptInjector.cs                edits jellyfin-web index.html; falls back to the File Transformation plugin
  Helpers/TransformationPatches.cs File Transformation callback (adds the same tags at runtime)
  Api/MediaBarEnhancedController.cs  GET /MediaBarEnhanced/Config and /MediaBarEnhanced/Resources/{path}
  Configuration/PluginConfiguration.cs  settings and their defaults
  Configuration/configPage.html    dashboard settings page (embedded)
  Web/mediaBarEnhanced.js          the slideshow itself (~3,900 lines, most of the logic)
  Web/mediaBarEnhanced.css
  Web/assets/*.svg                 logos for the client-settings button
  Jellyfin.Plugin.MediaBarEnhanced.csproj
manifest.json                      Jellyfin plugin repository manifest (published from main)
.github/workflows/                 upstream CI (see "Publish" below; it does not run on this fork)
logo.png                           catalog image (also copied into the build output)
```

### How it works

1. On startup the plugin adds `<script src="../MediaBarEnhanced/Resources/mediaBarEnhanced.js" defer>` before `</body>` and a CSS `<link>` before `</head>` in jellyfin-web's `index.html`.
2. If it can't write that file (in production it's owned by root), it registers a runtime transformation with the **File Transformation** plugin through reflection. Without that plugin, nothing is injected.
3. Everything under `Web/` is embedded in the DLL and served by `GET /MediaBarEnhanced/Resources/{path}`.
4. The JS loads `GET /MediaBarEnhanced/Config`, waits for login and `ApiClient`, and runs on the home route.
   - It calls Jellyfin's `/Items` APIs, the YouTube iframe API and `sponsor.ajay.app`.
   - Per-device settings are kept in `localStorage` under keys named `mediaBarEnhanced-*`.
   - Its console logs start with `🎬 Media Bar:`.

### Adding a setting

Add it in three places:

1. A property on `PluginConfiguration.cs` (PascalCase).
2. A field in `configPage.html`, in both the load and the save code.
3. A key in the JS `CONFIG` object using the camelCase name. `fetchPluginConfig` only copies server keys that already exist in `CONFIG`.

## Build, check, test

- .NET SDK 10.0.112 is installed at `/usr/bin/dotnet`. It builds both the net9.0 and net10.0 targets.
- Default build (Jellyfin 10.11 / net9.0). Checked on 2026-10-02: it builds with 2 warnings and 0 errors.
  ```bash
  dotnet build Jellyfin.Plugin.MediaBarEnhanced/Jellyfin.Plugin.MediaBarEnhanced.csproj \
    --configuration Release --output bin/Publish
  ```
- The csproj picks the Jellyfin packages from `<JellyfinVersion>` (default `10.11.0`). The target framework depends on it: `10.11*` builds net9.0 and anything else builds net8.0.
- **A Jellyfin 12.1 build does not work from this repo yet.** `-p:JellyfinVersion=12.1.0` fails with NU1202, because 12.1 needs net10.0 and this csproj picks net8.0. See "Staging copy" for the port.
- JS syntax check: `node --check Jellyfin.Plugin.MediaBarEnhanced/Web/mediaBarEnhanced.js`
- **There are no automated tests.** Test by hand in the staging Jellyfin:
  1. Build and install the plugin there.
  2. Restart Jellyfin, then hard-refresh the browser (Ctrl+F5).
  3. Check the home page and the browser console.
- JS and CSS are compiled into the DLL. Every web change needs a rebuild, a reinstall and a Jellyfin restart.

## Package

Same steps as upstream CI:

```bash
cd bin/Publish && zip -r Jellyfin.Plugin.MediaBarEnhanced.zip * && cd ../..
md5sum bin/Publish/Jellyfin.Plugin.MediaBarEnhanced.zip   # this is the manifest checksum
```

- The zip holds the files at its root: the DLL, `.deps.json`, `.pdb` and `logo.png`. There is no folder inside it.
- Newtonsoft.Json is not shipped in the zip. The plugin uses the copy that comes with Jellyfin (12.1 has it in `/usr/lib/jellyfin/bin`).
- Package from a clean output folder. `zip` adds to an existing zip, so old files can end up in it.
- The 1.7.1.0 zip in `bin/Publish/` matches its manifest checksum (`f63f8dff…`).

## Manifest and how Jellyfin installs from it

`manifest.json` is a JSON array with one plugin object: `guid`, `name`, `description`, `overview`, `owner`, `category`, `imageUrl`, and `versions` (newest first). Each version has these fields:

| field | value |
|---|---|
| `version` | 4-part, e.g. `1.7.1.0` |
| `changelog` | plain text, `- ` lines separated by `\n` |
| `targetAbi` | lowest Jellyfin version the build supports, e.g. `10.11.0.0` |
| `sourceUrl` | `https://github.com/EclipseKnight/jellyfin-plugin-media-bar-enhanced/releases/download/v<version>/Jellyfin.Plugin.MediaBarEnhanced.zip` |
| `checksum` | MD5 of that zip (lowercase hex) |
| `timestamp` | UTC, `YYYY-MM-DDTHH:MM:SSZ` |

- Jellyfin reads it from `https://raw.githubusercontent.com/EclipseKnight/jellyfin-plugin-media-bar-enhanced/refs/heads/main/manifest.json`. Pushing to `main` publishes it. raw.githubusercontent can take a few minutes to show the new file.
- Production already has this repository entry, named "Media Bar Enhanced".
- How an install works:
  1. Dashboard > Plugins > Catalog picks the newest version whose `targetAbi` the server supports.
  2. Jellyfin downloads `sourceUrl` and checks the MD5 against `checksum`. A mismatch fails the install.
  3. It unzips into `plugins/<name>_<version>/` (for example `Media Bar Enhanced_1.7.1.0/`) and writes a `meta.json`.
  4. It needs a restart.
- The older version entries point at upstream's (CodeDevMLH) release assets. Leave them as they are.
- 1.7.0.0 was removed from this manifest in `8834c1c`, and 1.5.1.1 was never in it. *Unsure* if that was on purpose.
- The `owner` and description still name CodeDevMLH, the upstream author. The `imageUrl` points at this fork.
- The README's install section points at **upstream's** central manifest, not this fork's. Don't use it for Altffour.

## Versioning, changelog, tags

- Versions have 4 parts (Jellyfin style).
- Every release does all of these:
  - bumps `<Version>` in the csproj
  - adds a new entry at the **top** of `manifest.json` `versions`
  - writes a changelog in that entry
  - gets the git tag `v<version>`
- **Never replace a published version.** If one is broken, fix it in the next version.
- The changelog is the manifest entry's `changelog` field (also used as the GitHub release notes). There is no CHANGELOG.md.
- Changes that aren't released yet stay in commit messages. Sum them up in the next release's changelog.
- Tags `v1.0.0.0` to `v1.7.0.0` came from upstream. `v1.7.1.0` is the first fork release (lightweight tag on `8834c1c`).
- Don't reuse `1.8.0.0` for different code without asking the owner. That number belongs to the unpublished 12.1 build in staging.

## Publish (manual; this is how 1.7.1.0 was done)

1. Bump the csproj `<Version>`.
2. Build into a clean `bin/Publish`, zip it, and take the MD5 (see Package).
3. Add the manifest entry: version, changelog, targetAbi, sourceUrl, checksum, timestamp.
4. Commit the csproj and `manifest.json`, then tag and push:
   ```bash
   git tag v<version>
   git push origin main v<version>
   ```
5. Create the release with the zip:
   ```bash
   gh release create v<version> bin/Publish/Jellyfin.Plugin.MediaBarEnhanced.zip --verify-tag --title v<version> --notes "<changelog>"
   ```
6. Check it worked:
   - Download the release asset and confirm its MD5 matches the manifest.
   - Fetch the raw manifest URL and confirm the new entry is there.

Steps 4 and 5 make the release public. Only do them when the owner asks.

### About the GitHub workflows (upstream's)

- `release_automation.yml` runs on push to `main`. It builds, rewrites the top manifest entry's checksum, timestamp and URL, auto-commits that, and creates the release.
- Then it tries to push to a central manifest repo, `<repo owner>/jellyfin-plugin-manifest`, using the secret `JELLYFIN_PLUGIN_MANIFEST_UPDATER_PAT`. Neither the repo nor the secret exists on this fork, so that step would fail.
- It skips everything if release `v<version>` already exists.
- `build.yaml` only runs on pushes to `dev`, and there is no `dev` branch here.
- **As of 2026-10-02 the fork has zero workflow runs**, even though both workflows show as "active". *Unsure* why.
- If they ever start running, a push to `main` with a new version could release by itself and push a manifest commit. Run `gh run list` after pushing and `git pull` before your next commit.
- CI builds only the default (10.11) target.

## Production and staging

- **Production Jellyfin is 12.1 and frozen** (apt-held). This plugin is **not installed** in production right now.
  - It was removed on 2026-09-25 because it showed a blank banner on Jellyfin 12's default "Modern" web layout. *Unsure* of the root cause.
  - Its old settings are still in `/var/lib/jellyfin/plugins/configurations/Jellyfin.Plugin.MediaBarEnhanced.xml` (from 2026-03-10). A reinstall would pick them up.
  - File Transformation 3.0.1.0 is installed in production. The plugin needs it there, because `/usr/share/jellyfin/web/index.html` is owned by root.
- **The published manifest only has 10.11 builds**, and 10.11 plugins don't load on Jellyfin 12. *Unsure* whether the 12.1 catalog still offers 1.7.1.0. If it does, installing it would give a plugin that doesn't work.
- **Staging copy: `/srv/altffour-staging/src/media-bar`.** It's a clone of this repo (its `origin` is `/var/www/jellyfin-plugin-media-bar-enhanced`).
  - It has one commit not in this repo: `14480ff` "Media Bar Enhanced 1.8.0.0: Jellyfin 12.1 / net10". That commit adds a net10.0 target for `12.*` and bumps the version to 1.8.0.0.
  - It was used to build `/srv/altffour-staging/packages/Media Bar Enhanced_1.8.0.0/` (DLL plus a hand-made `meta.json` with targetAbi `12.1.0.0`) for the Jellyfin 12.1 upgrade rehearsal.
  - That build used Jellyfin.Controller 12.1.0, so it was probably built with `-p:JellyfinVersion=12.1.0` (*unsure* of the exact command).
  - 1.8.0.0 was never published, tagged or pushed.
- **Staging Jellyfin**: Docker container `jellyfin-staging` (`jellyfin/jellyfin:12.1`, `127.0.0.1:8098`).
  - It's normally stopped. Start it with `docker start jellyfin-staging`.
  - Its plugins are in `/srv/altffour-staging/jf/var-lib-jellyfin/plugins/`, and 1.8.0.0 is installed there.
  - Its API key is in `/root/.jellyfin-staging-api-key`. Never print it.
- Background on the 12.1 upgrade: `/var/www/ALTFFOUR_DESIGN_PLAN.md` §2.11–2.14.

### Never install or upgrade in production yourself

- Only the owner decides when to install, upgrade or remove this plugin in production.
- An install needs a Jellyfin restart. Before any restart, check that nobody is watching. This command doesn't print the key:
  ```bash
  curl -s -H "Authorization: MediaBrowser Token=\"$(cat /root/.jellyfin-api-key)\"" \
    "http://127.0.0.1:8096/Sessions?ActiveWithinSeconds=900" \
    | jq '[.[] | select(.NowPlayingItem)] | length'
  ```
  Legacy `api_key` and `X-Emby-Token` auth is off on 12.1. Use the `Authorization` header.
- Keep `autoUpdate` off. Production has it off for every plugin.
- Look but don't touch: `/var/lib/jellyfin/plugins` is read-only for agents.

## Related repos

- **Altffour theme**: `/var/www/altffour-theme-repo`.
  - `Theme/assets/add-ons/media-bar-plugin-support-{nightly,latest-min}.css` styles the media bar. It targets `#homeTab #slides-container`, `.backdrop*`, `.play-button`, `.detail-button`, `.favorite-button`, `.slide-loading-indicator` and the Continue Watching row.
  - If you rename IDs or classes in the JS or CSS, update that theme CSS too.
- **Altffour app monorepo**: `/var/www/altffour-jellyfin-frontend`. It has the main `AGENTS.md`. The custom Altffour app does not use this plugin; it's for jellyfin-web.

## Safety rules

- Don't print secrets (API keys, tokens, `/root/.jellyfin-api-key`, gh credentials).
- Ask before deleting data (configs, plugin folders, releases, tags, branches).
- Don't use `pkill` or `killall`. Stop a specific PID or use `systemctl` or `docker` for the named service, and only with the owner's go-ahead for production.
- `GET /MediaBarEnhanced/Config` needs no login, so the plugin settings are public. Never put anything secret in them.

## Commit rules

- Run `git status` first. The tree may have the owner's uncommitted work.
  - On 2026-10-02 there's an uncommitted home-route fix in `Web/mediaBarEnhanced.js`. It makes the slideshow start only on `#/home`.
  - Don't stage, stash, revert or change someone else's changes. Add only your own files by name.
- Use plain, short commit messages. Existing style: `fix(css): …`, `fix(home): …`, `chore(release): publish v1.7.1.0 from fork`.
- **No AI attribution**: no `Co-Authored-By` trailer and no "Generated with Claude Code" line, in commits or PRs.
- Don't force-push `main`. `main` is not branch-protected, so be careful.
- Write a changelog with each change: the manifest `changelog` for releases, and clear commit messages until then.

## Gotchas

- `.gitignore` ignores `*.md` except `README.md`. New Markdown files need `git add -f <file>`. Once tracked, they behave normally.
- After any web change, browsers keep the old JS and CSS. Hard-refresh (Ctrl+F5).
- `/api-docs/openapi.json` returns 500 on both 10.11 and 12.1. Two plugins share the `PluginConfiguration` schema id (this one and another). It's cosmetic. A fix would be to rename the class.
- The remote branch `altffour/maintain-media-bar-enhanced` holds an older copy of the 1.7.1.0 fix (`5d23573`). `main` already has it. *Unsure* if the branch is still used.
- `isHomePageRoute()` in the uncommitted fix doesn't match `#/home.html?…` (from `/var/www/REVIEW_FINDINGS.md`).
- When you disable the plugin, it removes its tags from `index.html`. If you delete the plugin folder while the tags were written directly into the file, they may stay behind. Production currently has none.
