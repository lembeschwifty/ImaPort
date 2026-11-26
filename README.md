# ImaPort - Portable Image File Converter

ImaPort is a portable, cross-platform image converter that lives in a **single HTML file**. No installation, no dependencies, no internet connection, no account. Download `index.html`, open it in any modern browser, and convert images entirely on your own device.

## Why ImaPort?

- **Truly portable**: one self-contained file. Carry it on a USB stick, email it to yourself, keep it on your phone. It works on Windows, macOS, Linux, Android, and iOS, in any modern browser.
- **Fully offline**: once you have the file, no network access is ever needed or used.
- **Private by design**: images are decoded and encoded locally using the browser's built-in Canvas APIs. Nothing is uploaded anywhere - there is no server.
- **Zero dependencies**: no frameworks, no CDN scripts, no build step.

## Features

- Convert to **PNG**, **JPG**, or **WEBP**
- Input: anything your browser can decode - PNG, JPG, WEBP, GIF, BMP, SVG, AVIF (where supported)
- **Batch conversion**: add many files, convert them all in one pass
- **Quality slider** for lossy formats (JPG, WEBP), 10-100%
- **Background color picker** for JPG output (JPG has no alpha channel, so transparent areas are filled)
- Per-file **Download** buttons plus **Download all**
- Shows original vs. converted file size and flags whether the output is smaller
- Drag-and-drop or tap-to-browse; touch friendly
- Dark, responsive UI that works on phones and desktops

## Quick Start

1. Download `index.html` from this repository (Code > Download ZIP, or the raw file).
2. Open it in any modern browser (double-click it, or open it from your phone's file manager).
3. Drag images in or tap the drop zone, choose your output format, and press **Convert**.
4. Press **Download** (single file) or **Download all** (batch).

Optional: host `index.html` on any static host (GitHub Pages, Netlify, a USB web server, anything) - it behaves exactly the same.

## Supported Conversions

| Input | PNG out | JPG out | WEBP out |
| --- | --- | --- | --- |
| PNG  | yes | yes* | yes |
| JPG/JPEG | yes | yes | yes |
| WEBP | yes | yes* | yes |
| GIF (first frame) | yes | yes* | yes |
| BMP  | yes | yes* | yes |
| SVG (rasterized) | yes | yes* | yes |
| AVIF (where browser supports decode) | yes | yes* | yes |

* JPG has no transparency; transparent areas are filled with your chosen background color (default white).

## Known Limitations

Be aware of these before using ImaPort for critical work:

- **Output formats are limited to PNG, JPG, and WEBP.** Browsers can only encode those natively. Formats like TIFF, ICO, HEIC, or animated GIF cannot be written without external encoders (a deliberate trade-off to keep the app dependency-free and offline-capable).
- **Animated GIF/WebP input uses only the first frame.** Animation is lost; ImaPort outputs static images.
- **SVG input is rasterized** at up to 2048 px on its longest side. Converting to another vector format is not supported.
- **AVIF support depends on the browser** (Chrome and recent Firefox can decode it; Safari support varies).
- **Color profile and EXIF metadata are not preserved.** The conversion pipeline uses the Canvas API, which strips most metadata and does not apply ICC profile transforms.
- **JPEG quality is approximate.** The quality slider maps to the browser encoder's internal scale; results differ slightly between browsers.
- **Very large images** may fail on memory-constrained devices (e.g., big panoramas on old phones) because canvas size limits vary per browser.
- **HEIC input** generally does not work, since most browsers cannot decode HEIC.
- **ICO input** support depends on the browser; many browsers decode standard ICO files, but not all.

## Privacy & Security

- Images are processed entirely in your browser via the Canvas API and `URL.createObjectURL`.
- The app makes **no network requests** of any kind. You can verify this yourself in your browser's dev tools Network tab (works offline, e.g. with airplane mode).
- No analytics, no cookies, no local storage, no tracking.
- No supply chain: zero dependencies means nothing to audit or update.

## Troubleshooting

- **"Browser could not decode this file"**: the image format is not supported by your browser (common with HEIC). Try a different browser or convert the file to PNG/JPG first.
- **"Encoding failed"**: the browser refused to encode (rare). Try a different output format.
- **Downloaded file has no extension**: some mobile browsers save files by the page title; rename it and add `.png`, `.jpg`, or `.webp`.
- **Output is larger than the original**: normal when converting a well-compressed JPG to PNG (lossless), or when raising the quality slider. Lower the quality or pick WEBP.

## Technical Notes

- Single file: HTML + inline CSS + vanilla ES2017 JavaScript (~17 KB, no build step).
- Decode via `Image` + object URLs; encode via `canvas.toBlob(mime, quality)`.
- JPG output composites onto a background color first, since JPEG has no alpha channel.
- Works from `file://` - no server required.

## License

MIT - see [LICENSE](LICENSE).
