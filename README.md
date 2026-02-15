# Jellyfin Media Bar Enhanced Plugin

Media Bar Enhanced is a plugin for Jellyfin that introduces a customizable and interactive media bar to your dashboard view on Jellyfin web.

This plugin is a fork and enhancement of the original [Media Bar by MakD](https://github.com/MakD/Jellyfin-Media-Bar) and my previous work on [Jellyfin-Featured-Content-Bar](https://github.com/CodeDevMLH/Jellyfin-Featured-Content-Bar), but can be installed as plugin for easier installation and management/configuration.

![logo](https://raw.githubusercontent.com/CodeDevMLH/jellyfin-plugin-media-bar-enhanced/main/logo.png)

---

## Table of Contents
- [Jellyfin Media Bar Enhanced Plugin](#jellyfin-media-bar-enhanced-plugin)
  - [Table of Contents](#table-of-contents)
  - [Overview](#overview)
  - [Features](#features)
    - [New Features \& Enhancements](#new-features--enhancements)
    - [Core Features](#core-features)
  - [Installation](#installation)
  - [Client Compatibility](#client-compatibility)
  - [Configuration](#configuration)
    - [General Settings](#general-settings)
    - [Custom Content](#custom-content)
    - [Content Sorting](#content-sorting)
    - [Content Limits](#content-limits)
    - [Advanced Settings](#advanced-settings)
  - [Build The Plugin By Yourself](#build-the-plugin-by-yourself)
  - [Troubleshooting](#troubleshooting)
    - [Effects Not Showing](#effects-not-showing)
    - [Docker Permission Issues](#docker-permission-issues)
  - [Credits](#credits)
  - [Contributing](#contributing)

---

## Overview
![demo](https://github.com/user-attachments/assets/3a01b886-1a96-4dd1-abf6-e9c3b054bfde)

Expand to get more impressions:

<details>
<summary>Desktop Layout</summary> 

<img width="1920" height="1080" alt="trailer button" src="https://github.com/user-attachments/assets/5dce8eb1-8f2f-4583-a6d5-16f27ced8608" />
Normal mode like the original with additional trailer button
<br><br><br>

<img width="1920" height="993" alt="modal_desktop" src="https://github.com/user-attachments/assets/9087f43d-cd9d-4581-a7e0-404b75bc8e02" />
Trailer modal
<br><br><br>

<img width="1920" height="994" alt="config" src="https://github.com/user-attachments/assets/5492c384-a5c4-47ee-9428-3d9de2748e63" />
Excerpt from the config: E.g. here you can simply add your items that should be displayed
<br><br>
</details>


<details>
<summary>Mobile Layout</summary> 

![demo_mobile](https://github.com/user-attachments/assets/d11a7ed0-ceb7-43c3-9b22-09510251e0aa)
<br>If trailer on mobile is eenabled...
<br><br><br>

<img width="1080" height="2199" alt="mobile" src="https://github.com/user-attachments/assets/f0a0cc0d-f019-45f5-96c8-a5de14bf92ba" />
Normal mode like the original with additional trailer button
<br><br><br>

<img width="1080" height="2199" alt="trailer_modal_mobile" src="https://github.com/user-attachments/assets/944f9b82-9c9b-411f-883b-877b65ed933f" />
Trailer modal in portrait mode
<br><br>
</details>


## Features

This plugin builds upon the original Media Bar with new capabilities and improvements:

### New Features & Enhancements
*   **Video Backdrop Support**: Play trailer as background video directly in the slideshow
*   **SponsorBlock Integration**: Automatically skip intro/outro segments in YouTube trailers
*   **Enhanced Controls**:
    *   Keyboard shortcuts (Arrow keys to navigate, Space to pause, M to mute)
    *   Option to always show navigation arrows
    *   Standalone "Trailer" button (opens in a modal) if video backdrops are disabled
*   **Smarter Playback**:
    *   Option to wait for the trailer to end before advancing the slide.
    *   Mute/Unmute controls
*   **Override Trailers**: Manually specify a custom trailer URL for any item via the Custom Media IDs list
*   **Customization**:
    *   **Custom Media IDs**: Manually specify which items (Movies, Series, Collections/Boxsets) to display. Easily configurable via the plugin settings
    *   **Seasonal Content Mode**: Define date-based lists for holidays and seasons (e.g., Halloween, Christmas)
    *   Pagination dots turn into a counter (e.g., 1/20) if the limit is exceeded
        <details>
        <summary>Have a look:</summary>
        <img width="167" height="142" alt="PagDots_Number" src="https://github.com/user-attachments/assets/6a0a5040-cf13-4d9c-ae96-f50ec249c3f1" />
        </details>
    *   Option to disable the loading screen
    *   Client Settings: Optionally allow users to set selected media bar settings from their client.
        <details>
        <summary>Have a look:</summary>
        <img width="513" height="575" alt="Client-Settings" src="https://github.com/user-attachments/assets/3e29a84f-f8ea-4b7b-b561-80493cb1535b" />
        </details>
    *   **Local Trailers Preference**: Option to prefer local trailers (from the media item) over online sources.
    *   **Theme Video Support**: Option to prefer local theme videos (backdrops) over trailers.
    *   **Randomization**: Options to randomize theme videos and local trailers if multiple versions exist.
    *   **Include Watched Content**: Option to include watched items in the random slideshow.
    *   **Content Sorting Options**: Sort content by various criteria such as PremiereDate, ProductionYear, Random, or Original order.
    *   **Client-Side Settings**: Allow users to override settings locally on their device.

### Core Features
*   **Immersive Slideshow**: Rotates through your media library
*   **Metadata Display**: Shows title, rating, year, and plot summary
*   **Direct Play**: Click "Play" to start watching immediately
*   **Details View**: Click "Info" to jump to the item's detail page
*   **Add To Favorites**: Click the heart to add the item to your favorites
*   **Customize**: Change the plugins behavior through the Jellyfin admin panel

## Installation

This plugin is based on Jellyfin Version `10.11.x`

1.  Open your **Jellyfin Dashboard**.
2.  Navigate to **Plugins** > **Repositories**.
3.  Click the **+** button to add a new repository.
4.  Enter a name for the repo and paste the following URL:
    ```
    https://raw.githubusercontent.com/CodeDevMLH/jellyfin-plugin-manifest/refs/heads/main/manifest.json
    ```
5.  Click **Save**.
6.  Go to the **Catalog** tab.
7.  Find **Media Bar Enhanced** (Under **General**) and install it.
8.  **Restart your Jellyfin server.**
9.  **Refresh your browser** (Ctrl+F5) to load the new interface elements.

## Client Compatibility

Because this plugin relies on injecting JavaScript and CSS into the web interface, it works best on clients that use the web wrapper.

| Client Platform | Status | Notes |
| :--- | :---: | :--- |
| **Web Browsers** (Firefox, Chrome etc.) | ✅ | Direct JS injection |
| **Jellyfin Media Player** (Windows/Linux/macOS) | ✅ | Uses jellyfin web |
| **Android App** | ✅ | Uses a web wrapper |
| **iOS App** | ✅ | Uses a web wrapper |
| **Android TV / Fire TV** | ❌ | **Not supported.** Uses a native Java/Kotlin UI. |
| **Roku** | ❌ | **Not supported.** Uses a native UI. |
| **Swiftfin** (iOS/tvOS) | ❌ | **Not supported.** Uses a native Swift UI. |
| **Kodi** (via Jellyfin Addon) | ❌ | **Not supported.** Uses Kodi's native skinning engine. |

## Configuration

Configure the plugin via **Dashboard** > **Plugins** > **Media Bar Enhanced**.

> [!NOTE]
> You must refresh your browser window (F5 or Ctrl+R) after saving changes for them to take effect.

### General Settings
*   **Enable Media Bar Enhanced Plugin**: Master switch to toggle the plugin.
*   **Enable Video Backdrops**: Dynamically plays trailers in the background.
*   **Wait For Trailer To End**: Prevents slide transition until the video finishes.
*   **Enable Mobile Video**: specific setting to allow video playback on mobile devices (disabled by default to save data/battery).
*   **Show Trailer Button**: Adds a button to open the trailer in a popup modal if video backdrops are disabled (e.g. on mobile if trailers are disabled there)
*   **Prefer Local Trailers**: If enabled, local trailers will be preferred over remote (YouTube) trailers.
*   **Prefer Local Backdrops / Theme Videos**: If enabled, local backdrop videos (Theme Videos) will be preferred over trailers.

### Custom Content
Define exactly what shows up in your bar.

*   **Enable Custom Media IDs**: Restrict the slideshow to a specific list of IDs.
    *   **Manual Trailer Override**: Add `[YouTube_URL]` or `[Jellyfin_ID]` after an ID to force a specific trailer/video.
    *   Example ID: `a1b2c3d4e5... [https://www.youtube.com/watch?v=VIDEO_ID]`
    *   Example ID: `z1b2c3d4e5... [Jellyfin_ID]`
    *   **Example Mixed List**:
        ```
        a1b2c3d4e5f6...                         <-- Plays local item video
        6bdu812812hd... [https://youtu.be/...]  <-- Item metadata + Custom YouTube Trailer
        12h44h124sf7... [hdc78127z4ff...]       <-- Item metadata + Custom Jellyfin Trailer/Video etc.
        ```
    *   Example Collection Name: `Halloween Collection [https://...] | My Description` (Note: Use `|` to separate description from name if using a name instead of an ID)
*   **Apply Limits to Custom IDs**: If enabled, the "Content Limits" (see below) will also apply to your Custom Media IDs list. By default, custom lists show all listed items regardless of limits.
*   **Enable Seasonal Content Mode**: Advanced date-based scheduling.
    *   **GUI Configuration**: You can easily add "Seasons" via the **Add Season** button.
    *   **Active Period**: Select the Start and End Day/Month for each season.
    *   **Media IDs**: Enter the Comma-separated list of IDs (Movies, Series, Collections) for that season.
    *   **Priority**: If the current date matches a defined season, those IDs are used. If multiple seasons overlap, the first matching one is used. If no season matches, it falls back to the Default Custom Media IDs.

**How to get IDs:**
Check the URL of an item in the web interface:
`.../web/#/details?id=YOUR_ITEM_ID_HERE&...`

### Content Sorting
Customize the order of slides in the Media Bar.

*   **Sort By**: Choose criteria like *Random*, *Premiere Date*, *Production Year*, *Critic Rating*, *Community Rating*, *Name*, or *Runtime*.
*   **Sort Order**: Ascending or Descending.
*   **Note**: Sorting applies to both server-fetched content AND Custom Media IDs. Select **Original** to preserve the exact order of your Custom Media IDs list.

### Content Limits
Fine-tune performance by limiting the number of items fetched from the server.

*   **Total Max Items**: Maximum total items to fetch (combined).
*   **Include Watched Content**: If enabled, the random slideshow will also include items that you have already watched.
*   **Max Movies**: Maximum movies to include (for random selection).
*   **Max Tv Shows**: Maximum TV shows to include (for random selection).
*   **Preload Count**: Number of slides to preload for smooth transitions.
    *   *Intelligent Preloading*: The plugin uses a safe preloading strategy that respects this count but handles small lists gracefully to avoid playback issues.
*   **Max Pagination Dots**: Maximum number of dots to show. If exceeded, it switches to a counter (e.g., 1/20).

### Advanced Settings
*   **Slide Animations**: Enable/disable the "Zoom In" effect.
*   **Use SponsorBlock**: Skips non-content segments in YouTube trailers (if the data exists).
*   **Preferred YouTube Quality**: Select your preferred resolution (*Auto*, *Maximum*, *1080p*, *720p*).
*   **Start Muted**: Videos start without sound (user can unmute).
*   **Full Width Video**: Stretches video to cover the entire width (good for desktop, crop on mobile).
*   **Enable Loading Screen**: Enable/disable the loading indicator while the bar initializes.
*   **Always Show Arrows**: Keeps navigation arrows visible instead of hiding them on mouse leave.
*   **Randomize Backdrop Video**: If enabled, a random video from the backdrops/theme videos will be selected instead of the first one.
*   **Randomize Local Trailer**: If enabled, a random local trailer will be selected instead of the first one.
*   **Enable Keyboard Controls**:
    *   `Left`/`Right`: Change slide
    *   `Space`: Pause/Play slideshow
    *   `M`: Mute/Unmute video
*   **Content Limits**: Fine-tune performance by limiting the number of items (Movies, TV Shows) fetched.

## Build The Plugin By Yourself

If you want to build the plugin yourself:

1.  Clone the repository.
2.  Ensure you have the .NET SDK installed (NET 8 or 9 depending on your Jellyfin version).
3.  Run the build command:
    ```powershell
    dotnet build Jellyfin.Plugin.MediaBarEnhanced/Jellyfin.Plugin.MediaBarEnhanced.csproj --configuration Release --output bin/Publish
    ```
4.  The compiled DLL and resources will be in bin/Publish.

## Troubleshooting

### Effects Not Showing
1. **Verify plugin installation**:
   - Check that the plugin appears in the jellyfin admin panel
   - Ensure that the plugin is enabled and active

2. **Clear browser cache**:
   - Force refresh browser (Ctrl+F5)
   - Clear jellyfin web client cache (--> mostly you have to clear the whole browser cache)

### Docker Permission Issues
If you encounter the message `Access was denied when attempting to inject script into index.html. Automatic direct injection failed. Automatic direct insertion failed. The system will now attempt to use the File Transformation plugin.` in the log or similar permission errors in Docker:

**Option 1: Use File Transformation Plugin (Recommended)**

Media Bar Enhanced now automatically detects and uses the [File Transformation](https://github.com/IAmParadox27/jellyfin-plugin-file-transformation) plugin (v2.5.0.0+) if it's installed. This eliminates permission issues by transforming content at runtime without modifying files on disk.

**Installation Steps:**
1. Install the File Transformation plugin from the Jellyfin plugin catalog
2. Restart Jellyfin
3. Media Bar Enhanced will automatically detect and use it (no configuration needed)
4. Check logs to confirm: Look for "Successfully registered transformation with File Transformation plugin"

**Benefits:**
- No file permission issues in Docker environments
- Works with read-only web directories
- Survives Jellyfin updates without re-injection
- No manual file modifications required

**Option 2: Fix File Permissions**
```bash
# Find the actual index.html location
docker exec -it jellyfin find / -name index.html

# Fix ownership (replace 'jellyfin' with your container name and adjust user:group if needed)
docker exec -it --user root jellyfin chown jellyfin:jellyfin /jellyfin/jellyfin-web/index.html

# Restart container
docker restart jellyfin
```

**Option 3: Manual Volume Mapping**
```bash
# Extract index.html from container
docker cp jellyfin:/jellyfin/jellyfin-web/index.html /path/to/jellyfin/config/index.html

# Add to docker-compose.yml volumes section:
volumes:
  - /path/to/jellyfin/config/index.html:/jellyfin/jellyfin-web/index.html
```

## Credits

This project is based on the original [Jellyfin Media Bar by MakD](https://github.com/MakD/Jellyfin-Media-Bar) and incorporates concepts from [IAmParadox27's plugin fork](https://github.com/IAmParadox27/jellyfin-plugin-media-bar). Thanks for their work!

Also, special thanks to IAmParadox27 for the [File Transformation plugin](https://github.com/IAmParadox27/jellyfin-plugin-file-transformation) which this plugin can optionally use for improved Docker compatibility.

## Contributing

Feel free to contribute to this project by creating pull requests or reporting issues.
