# nuri-packs

Downloadable coloring-book packs for the **Nuri** toddler app. Read-only public
content served over HTTPS via GitHub Pages. The app fetches `manifest.json`,
lists packs in the parent dashboard, and downloads pages on demand —
**no app update needed to ship new pictures.**

## Layout

```
manifest.json          index of all packs + per-file sha256 (integrity check)
<packId>/
  <slug>.heic          display image (what the child colors)
  <slug>-wall.png      flood-fill wall mask (bold outlines → walls)
```

Each page's region seed points + names live inline in `manifest.json`.

## Adding / updating a pack

1. Make clean line-art PNGs (bold **closed** black outlines, flat white
   interiors, white background — see the app repo's
   `ios-native/Tools/coloring-regen-plan.md`). Put them in a folder, one PNG
   per page (`car.png`, `truck.png`, …).
2. From the app repo, build the pack (generates HEIC + wall mask + seeds +
   verifies fills + updates the manifest with fresh checksums):
   ```
   python3 ios-native/Tools/build-pack.py \
     --pack cars --title-en "Cars" --title-sv "Bilar" --title-ko "자동차" \
     --src /path/to/pngs --out /path/to/nuri-packs
   ```
3. Commit & push. The app picks up the new/updated pack on next Refresh.

## Security model

Read-only public content, no accounts, nothing uploaded. The app enforces:
HTTPS-only, per-file SHA-256 verification against the manifest, JSON schema
validation, size caps, and filename sanitization. Downloads are reachable only
behind the parent-dashboard gate.
