# Websnap 

![GitHub License](https://img.shields.io/github/license/danielpos178/websnap)




<p align="center">
    <img src="docs/images/ss_15_tps_80.gif" alt="Websnap Demo">
</p>

<p align="center">
    <b>A GitHub Action to automatically generate animated GIFs or WebP images from web pages.</b>
</p>

## Overview

Websnap captures website scrolling animations or static snapshots directly within your GitHub Actions pipeline. This is a fork of [PabloLec/website-to-gif](https://github.com/PabloLec/website-to-gif) that fixes the `no_scroll` bug and adds a few new features.

### Added Features
- **Dark Mode:** Inject CSS filters to capture sites in dark mode automatically (`dark_mode: true`).
- **Hide Elements:** Remove sticky navbars, cookie banners, or ads before capturing using CSS selectors.
- **Fixed `no_scroll`:** The original repo had a typo that broke this feature. It works here now.
- **WebP Output:** Websnap supports outputting to `WebP`. WebP rendering takes longer than GIF generation, but it results in a smaller file size without losing quality.

---

## Usage

Create a workflow file (e.g., `.github/workflows/generate-preview.yml`) in your repository:

```yaml
name: Generate Website Preview

on: 
  workflow_dispatch:

jobs:
  capture:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Repository
        uses: actions/checkout@v4
      
      - name: Capture Website
        uses: danielpos178/websnap@main
        with:
          url: "https://docs.github.com/en/get-started"
          file_name: "demo"
          dark_mode: true
          hide_elements: ".cookie-banner"

      - name: Commit and Push
        run: |
          git config --global user.name "websnap[bot]"
          git config --global user.email "github-actions[bot]@users.noreply.github.com"
          git add .
          git commit -m "Update website preview" || echo "No changes to commit"
          git push
```

---

## Inputs

| Parameter | Description | Default | Example |
|---|---|---|---|
| `url` | Web page URL to be captured. **(Required)** | - | `"https://docs.github.com"` |
| `save_path` | Absolute path for saving the file. Directory must exist. | `/` (repo root) | `"/docs/images/"` |
| `file_format` | Output file format (`GIF` or `WebP`). | `GIF` | `"WebP"` |
| `file_name` | Output file name without extension or path. | `demo` | `"ss_25_tps_100"` |
| `window_width` | Browser window width in pixels. | `1920` | `1366` |
| `window_height` | Browser window height in pixels. | `1080` | `768` |
| `start_y` | Vertical position to start capture in pixels. | `0` | `0` |
| `stop_y` | Vertical position to stop capture. | Bottom of page | `800` |
| `final_width` | Width of the final output image. | `640` | `1024` |
| `final_height` | Height of the final output image. | `360` | `576` |
| `scroll_step` | Pixels to scroll per frame. | `25` | `50` |
| `time_per_frame`| Duration of each frame in milliseconds. | `100` | `200` |
| `start_delay` | Delay before capturing begins in milliseconds. | `0` | `100` |
| `no_scroll` | Disable scrolling to capture a static view. | `false` | `true` |
| `time_between_frames`| Interval between frame captures when `no_scroll` is true. | `100` | `200` |
| `number_of_frames`| Number of frames to capture when `no_scroll` is true. | `20` | `50` |
| `resizing_filter`| Filter used to resize frames (see Pillow docs). | `LANCZOS` | `"LANCZOS"` |
| `dark_mode` | Capture page in dark mode using CSS invert filter. | `false` | `true` |
| `hide_elements` | Comma-separated CSS selectors to hide before capture. | - | `".cookie-banner, #navbar"` |

---

## Examples

How `scroll_step` and `time_per_frame` affect the output pacing and file size:

### Smooth & Slow
**Parameters:** `scroll_step: 15`, `time_per_frame: 80`  
![Smooth and slow scroll](docs/images/ss_15_tps_80.gif)

### Balanced (Recommended)
**Parameters:** `scroll_step: 25`, `time_per_frame: 100`  
![Balanced scroll](docs/images/ss_25_tps_100.gif)

### Fast Scroll
**Parameters:** `scroll_step: 50`, `time_per_frame: 50`  
![Fast scroll](docs/images/ss_50_tps_50.gif)

### Fast Step, Normal Pacing
**Parameters:** `scroll_step: 50`, `time_per_frame: 100`  
![Fast step normal pacing](docs/images/ss_50_tps_100.gif)

### Staggered Scroll
**Parameters:** `scroll_step: 50`, `time_per_frame: 200`  
![Staggered scroll](docs/images/ss_50_tps_200.gif)

### Capturing Without Scrolling

You can disable page scrolling entirely to create animated snapshots of a single view. This is useful for capturing in-page animations or loaders.

```yaml
- name: Capture Static View
  uses: danielpos178/websnap@main
  with:
    url: "https://yoursite.com"
    no_scroll: true
    time_per_frame: 100
    time_between_frames: 100
    number_of_frames: 50
```

![Animated no scroll](docs/images/animated_no_scroll.gif)

---

## Contributing

Feel free to open an [issue](https://github.com/danielpos178/websnap/issues) or submit a pull request if you find a bug or have a feature request.

---

## Credits

Original action by [PabloLec](https://github.com/PabloLec/website-to-gif).
