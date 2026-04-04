# Optimize Gallery Images

Optimize new images added to `gallery-original/` for web use.

## What this does
- Reads all `.jpg` files from `gallery-original/`
- Resizes to max 1920px on the longest side (preserving aspect ratio)
- Converts to WebP at 80% quality
- Saves output to `gallery-web/`
- Reports original vs optimized file sizes

## Requirements
- ImageMagick must be installed: `brew install imagemagick`

## Steps

1. Check which images in `gallery-original/` do NOT yet have a corresponding file in `gallery-web/`:

```bash
for img in gallery-original/*.jpg; do
  filename=$(basename "$img" .jpg)
  if [ ! -f "gallery-web/${filename}.webp" ]; then
    echo "Missing: $filename"
  fi
done
```

2. Convert any missing images:

```bash
for img in gallery-original/*.jpg; do
  filename=$(basename "$img" .jpg)
  output="gallery-web/${filename}.webp"
  if [ ! -f "$output" ]; then
    magick "$img" -resize "1920x1920>" -quality 80 "$output"
    original_size=$(du -sh "$img" | cut -f1)
    web_size=$(du -sh "$output" | cut -f1)
    echo "  $filename: $original_size → $web_size"
  fi
done
```

3. For each new image, add an entry to `artworks.json` with `imagePath` pointing to:
   `https://psalomonsson.github.io/peggy-konst-assets/gallery-web/<FILENAME>.webp`

## Parameters (adjust if needed)
| Parameter | Value | Notes |
|-----------|-------|-------|
| Max dimension | 1920px | `-resize "1920x1920>"` — `>` means only shrink, never enlarge |
| Quality | 80% | Good balance of size vs quality; increase to 90% for higher fidelity |
| Format | WebP | Supported by all modern browsers; ~30% smaller than JPEG at same quality |

## Re-optimizing all images
If you want to redo all images (e.g., after changing quality settings), remove the `if [ ! -f "$output" ]` check or delete `gallery-web/` first:

```bash
rm -rf gallery-web && mkdir gallery-web
for img in gallery-original/*.jpg; do
  filename=$(basename "$img" .jpg)
  magick "$img" -resize "1920x1920>" -quality 80 "gallery-web/${filename}.webp"
  echo "  $filename: $(du -sh "$img" | cut -f1) → $(du -sh "gallery-web/${filename}.webp" | cut -f1)"
done
```
