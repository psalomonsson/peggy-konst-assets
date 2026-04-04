# peggy-konst-assets

Asset repository for Peggy Granlund's art website, served via GitHub Pages at:
`https://psalomonsson.github.io/peggy-konst-assets/`

## Structure

```
gallery-original/   High-resolution original JPEGs from camera (~4–10MB each)
gallery-web/        Web-optimized WebP images (~300–900KB), used by the website
banners/            Banner images used on the website
locales/            Translation files (en.json, sv.json) for UI text
artworks.json       Artwork metadata — titles, descriptions, sizes, sold status
```

## artworks.json

Each entry has:
- `title` (required)
- `imagePath` — full GitHub Pages URL pointing to `gallery-web/*.webp`
- `sold` — boolean
- `description`, `size` — optional

When adding a new artwork, point `imagePath` to `gallery-web/<FILENAME>.webp`.

## Image workflow

Original images live in `gallery-original/` and are never modified.
Web-optimized images live in `gallery-web/` and are what the website uses.

**To add new images:**
1. Drop the original `.jpg` into `gallery-original/`
2. Run `/optimize-images` (see [.claude/commands/optimize-images.md](.claude/commands/optimize-images.md))
3. Add entry to `artworks.json` with `imagePath` pointing to `gallery-web/<FILENAME>.webp`

**Optimization settings:**
- Max 1920px on the longest side (smaller images are never upscaled)
- WebP format at 80% quality
- Tool: ImageMagick (`brew install imagemagick`)

## Locales

Two languages: `en.json` (English) and `sv.json` (Swedish).
Both files must be kept in sync when changing UI text.

## Notes

- `banners/` contains manually prepared banner images — no automated optimization pipeline for these yet
- `.DS_Store` is gitignored
