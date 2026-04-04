# Rename Images by Title

Rename images in `gallery-original/` and `gallery-web/` to match the slugified titles defined in `artworks.json`. Only renames images that don't already match — safe to run multiple times.

## Steps

Run this script from the repo root:

```bash
python3 << 'EOF'
import json, re, unicodedata, os

def slugify(title):
    nfkd = unicodedata.normalize('NFKD', title)
    ascii_str = nfkd.encode('ascii', 'ignore').decode('ascii')
    return re.sub(r'[^a-z0-9]+', '-', ascii_str.lower()).strip('-')

base = '.'

with open(f'{base}/artworks.json') as f:
    artworks = json.load(f)

renamed = 0
for a in artworks:
    current = a['imagePath'].split('/')[-1]
    stem = current.rsplit('.', 1)[0]
    slug = slugify(a['title'])
    if stem == slug:
        continue

    for ext, folder in [('jpg', 'gallery-original'), ('webp', 'gallery-web')]:
        src = f'{base}/{folder}/{stem}.{ext}'
        dst = f'{base}/{folder}/{slug}.{ext}'
        if os.path.exists(src):
            os.rename(src, dst)
            print(f'{folder}: {stem}.{ext} → {slug}.{ext}')

    a['imagePath'] = a['imagePath'].replace(
        f'gallery-web/{stem}.webp',
        f'gallery-web/{slug}.webp'
    )
    renamed += 1

with open(f'{base}/artworks.json', 'w') as f:
    json.dump(artworks, f, ensure_ascii=False, indent=2)

if renamed:
    print(f'\nRenamed {renamed} artwork(s). artworks.json updated.')
else:
    print('All images already match their titles — nothing to do.')
EOF
```

## Slugification rules
- Swedish characters: å/ä → a, ö → o, é → e (via Unicode NFKD normalization)
- Spaces and non-alphanumeric characters → hyphens
- Lowercase
- Example: `"Vårens färger"` → `varens-farger`
