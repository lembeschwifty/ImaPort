# ImaPort - Portable Image File Converter

ImaPort is a portable, cross-platform image converter that lives in a **single HTML file**. No installation, no dependencies, no internet connection, no account. Download `index.html`, open it in any modern browser, and convert images entirely on your own device.

## Why ImaPort?

- **Truly portable**: one self-contained file. Carry it on a USB stick, email it to yourself, keep it on your phone. It works on Windows, macOS, Linux, Android, and iOS, in any modern browser.
- **Fully offline**: no network access is needed for any built-in format (the only exception is HEIC, see below).
- **Private by design**: images are decoded and encoded locally using the browser's built-in Canvas APIs. Nothing is uploaded anywhere - there is no server.
- **Zero dependencies**: no frameworks, no CDN scripts bundled in, no build step.

## Features

- Convert to **PNG**, **JPG**, or **WEBP**
- Input: anything your browser can decode - PNG, JPG, WEBP, GIF, BMP, SVG, AVIF (where supported), and **HEIC/HEIF** (iPhone photos) via an optional decoder
- **Optional resize before conversion**: presets (256 / 1280 / 1080 / 1440 px), a percentage slider (10-400%), or exact pixel dimensions with aspect-ratio fit
- **Batch conversion**: add many files, convert them all in one pass
- **Quality slider** for lossy formats (JPG, WEBP), 10-100%
- **Background color picker** for JPG output (JPG has no alpha channel, so transparent areas are filled)
- Per-file **Download** buttons plus **Download all**
- Shows original vs. converted size and dimensions, and flags whether the output is smaller
- Drag-and-drop or tap-to-browse; touch friendly
- Dark, responsive UI that works on phones and desktops

## Quick Start

1. Download `index.html` from this repository (Code > Download ZIP, or the raw file).
2. Open it in any modern browser (double-click it, or open it from your phone's file manager).
3. Drag images in or tap the drop zone, choose your output format, and press **Convert**.
4. Press **Download** (single file) or **Download all** (batch).

Optional: host `index.html` on any static host (GitHub Pages, Netlify, a USB web server, anything) - it behaves exactly the same.

## HEIC / HEIF Support (iPhone photos)

No browser can decode HEIC natively, so ImaPort handles it with an optional decoder:

- When you convert a HEIC/HEIF file, ImaPort lazily downloads the unmodified [heic2any](https://github.com/alexcorvi/heic2any) library (v0.0.4) from jsDelivr, once per session.
- The decoder runs entirely in your browser; the photo is still never uploaded anywhere.
- All built-in formats work fully offline. Only HEIC needs internet, and only for the one-time script download.

## Resizing

Four modes, applied to every file in the batch:

- **Original**: no resizing, dimensions are preserved.
- **Presets**: one tap fits the image within 256, 1280, 1080, or 1440 px (aspect ratio preserved).
- **Scale %**: percentage slider from 10% to 400% of the original dimensions.
- **Custom px**: enter width and/or height in pixels. With **Keep aspect ratio** on, both values act as a maximum bounding box (the image fits inside while preserving proportions); if you enter only one value, the other is computed. With it off, the exact dimensions you enter are used.

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
| HEIC/HEIF (via optional decoder) | yes | yes* | yes |

* JPG has no transparency; transparent areas are filled with your chosen background color (default white).

## Known Limitations

Be aware of these before using ImaPort for critical work:

- **Output formats are limited to PNG, JPG, and WEBP.** Browsers can only encode those natively. Formats like TIFF, ICO, or animated GIF cannot be written without external encoders (a deliberate trade-off to keep the app dependency-free).
- **HEIC decoding needs internet for a one-time script download** and depends on the heic2any library's compatibility with your file; live HEIC photos from iPhone cameras are supported, but some HEIF variants may fail.
- **Animated GIF/WebP input uses only the first frame.** Animation is lost; ImaPort outputs static images.
- **SVG input is rasterized** at up to 2048 px on its longest side. Converting to another vector format is not supported.
- **AVIF support depends on the browser** (Chrome and recent Firefox can decode it; Safari support varies).
- **Upscaling beyond 400%** is not offered in Scale mode (use Custom px for more), and upscaling never adds detail - the result is interpolated.
- **Color profile and EXIF metadata are not preserved.** The conversion pipeline uses the Canvas API, which strips most metadata and does not apply ICC profile transforms.
- **JPEG quality is approximate.** The quality slider maps to the browser encoder's internal scale; results differ slightly between browsers.
- **Very large images**: canvas size limits vary per browser and device. ImaPort clamps both the decoded source and the resize target to 16.7 megapixels (4096x4096); larger requests are proportionally scaled down instead of failing or producing blank output.
- **iPhone / iOS Safari quirks**:
  - Older iOS versions do not support the dynamic viewport height unit; on those versions the page layout may look off after rotating to landscape (modern iOS is handled with `100dvh`).
  - iOS Safari enforces a canvas area limit (~16.7 megapixels on many devices). The safeguard above scales oversized conversions down proportionally, but this means very large images may come out smaller than requested.
  - iPhone photos in HEIC format can be decoded via the optional decoder above (needs internet once).
  - Downloads in Safari on iOS may save with the page's name instead of the converted filename; rename the file and add the `.png`, `.jpg`, or `.webp` extension.
- **ICO input** support depends on the browser; many browsers decode standard ICO files, but not all.

## Privacy & Security

- Images are processed entirely in your browser via the Canvas API and `URL.createObjectURL`.
- The app makes **no network requests** except the single, user-triggered HEIC decoder script download (only if you actually convert a HEIC file). You can verify this yourself in your browser's dev tools Network tab.
- No analytics, no cookies, no local storage, no tracking.
- No bundled supply chain: the HTML file itself has zero dependencies.

## Troubleshooting

- **"HEIC decoder could not load"**: the optional decoder needs internet on first use. Connect and retry; it caches for the session.
- **"Browser could not decode this file"**: the image format is not supported by your browser. Try a different browser or convert the file first.
- **"Encoding failed"**: the browser refused to encode (rare). Try a different output format.
- **Custom resize seems ignored**: if both W and H are empty, the original size is kept; with Keep aspect ratio on, your values are a maximum box, not exact output size.
- **Output dimensions smaller than requested**: a safeguard clamps very large outputs to 16.7 megapixels to stay within device canvas limits.
- **Downloaded file has no extension**: some mobile browsers save files by the page title; rename it and add `.png`, `.jpg`, or `.webp`.
- **Output is larger than the original**: normal when converting a well-compressed JPG to PNG (lossless), or when raising the quality slider. Lower the quality or pick WEBP.

## Technical Notes

- Single file: HTML + inline CSS + vanilla ES2017 JavaScript (~24 KB, no build step).
- Decode via `Image` + object URLs; encode via `canvas.toBlob(mime, quality)`.
- HEIC is detected by MIME type or extension and routed through a lazily loaded heic2any decoder (unmodified dist file from jsDelivr, cached per session).
- Resize (when enabled) runs as a high-quality image-smoothing pass on a second canvas before encoding.
- Both decode and resize are clamped to 16.7 megapixels to respect mobile canvas area limits.
- Layout uses `100dvh` (with `100vh` fallback) so it survives iOS Safari rotation and toolbar collapse.
- JPG output composites onto a background color first, since JPEG has no alpha channel.
- Works from `file://` - no server required.

## Third-Party Notices

- [heic2any](https://github.com/alexcorvi/heic2any) v0.0.4, Copyright (c) 2020 Alex Corvi, MIT License - loaded unmodified from jsDelivr only when a HEIC file is converted; it may be replaced or rebuilt per its license.
- heic2any bundles [libheif](https://github.com/strukturag/libheif) and [libde265](https://github.com/strukturag/libde265), both GNU LGPL-3.0. Their sources and license texts are available at those repositories.

## License

MIT - see [LICENSE](LICENSE).
