---
name: png-optimize
description: Optimize and minimize PNG files for smaller file sizes using oxipng and pngquant. Use this skill whenever the user mentions optimizing PNGs, compressing images, reducing image file size, minimizing assets, preparing images for web, or running oxipng/pngquant/optipng. Also trigger when the user says "compress", "shrink", "minimize", or "optimize" near "png", "image", or "icon", or when committing PNG resources (per CLAUDE.md convention).
---

# PNG Optimization

Optimize PNG files to reduce file size while preserving visual quality. Uses a two-pass approach: lossy quantization (pngquant) followed by lossless compression (oxipng) for maximum savings.

## Tool Detection and Installation

Check which tools are available before starting:

```bash
which oxipng pngquant 2>/dev/null
```

- **oxipng** — lossless PNG optimizer (Rust rewrite of optipng, faster). Primary tool.
- **pngquant** — lossy PNG quantizer. Reduces colors intelligently for major size drops on photos/icons with many colors. Safe for flat icons and UI assets.

If neither is installed, install via Homebrew:

```bash
brew install oxipng pngquant
```

If Homebrew isn't available, `oxipng` can be installed via cargo (`cargo install oxipng`) and `pngquant` from https://pngquant.org.

## Optimization Strategy

Use both tools in sequence for best results. The order matters — lossy first, then lossless:

### Step 1: Lossy pass with pngquant (if available)

```bash
pngquant --quality=80-95 --force --ext .png <files...>
```

- `--quality=80-95` — target quality range (80 minimum, 95 ceiling). Imperceptible for icons and UI assets.
- `--force` — overwrite originals.
- `--ext .png` — keep the same filename (no `-fs8` suffix).
- Skip this step for screenshots or images where exact pixel fidelity matters. When in doubt, ask the user.

### Step 2: Lossless pass with oxipng

```bash
oxipng -o max --strip all <files...>
```

- `-o max` — maximum compression effort (slowest but smallest). Use `-o 4` for faster runs on many files.
- `--strip all` — remove metadata chunks (tEXt, iTXt, iCCp, etc.) that add unnecessary bytes.

### Single-tool fallback

If only oxipng is available, just run the lossless pass — it still provides meaningful savings (10-30% typical).

If only pngquant is available, run it alone — lossy quantization is where the biggest savings come from anyway.

## Reporting Results

Always report before and after sizes so the user can see the impact:

```bash
# Before optimization, capture sizes
ls -la *.png | awk '{print $5, $9}'

# After optimization, show comparison
ls -la *.png | awk '{print $5, $9}'
```

Summarize total savings as both bytes and percentage.

## Common Patterns

**Optimize all PNGs in a directory:**
```bash
pngquant --quality=80-95 --force --ext .png *.png
oxipng -o max --strip all *.png
```

**Optimize a single file conservatively (lossless only):**
```bash
oxipng -o max --strip all image.png
```

**Optimize recursively:**
```bash
find . -name "*.png" -exec pngquant --quality=80-95 --force --ext .png {} +
find . -name "*.png" -exec oxipng -o max --strip all {} +
```

**High-quality mode for photos (less aggressive lossy):**
```bash
pngquant --quality=90-99 --force --ext .png photo.png
oxipng -o max --strip all photo.png
```

## When NOT to Use Lossy Compression

- Screenshots for documentation where pixel accuracy matters
- Images that will be diff-compared (CI visual regression tests)
- Already-indexed/palettized PNGs (pngquant may increase size)
- The user explicitly asks for lossless-only

In these cases, skip pngquant and only run the oxipng lossless pass.
