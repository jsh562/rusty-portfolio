# rusty-portfolio

Source for the [Rusty portfolio landing page](https://jsh562.github.io/rusty-portfolio).

Live site renders from [`docs/index.md`](docs/index.md) via GitHub Pages (Jekyll, `jekyll-theme-minimal`). To update the site, edit `docs/index.md`, push to `main`, and GitHub rebuilds in ~30 seconds.

## Adding a new port

When a new port ships:

1. Edit [`docs/index.md`](docs/index.md) and add a new section under **Published ports** following the existing template:
   - Crate name as a heading linked to the GitHub repo
   - One-line tagline (what it ports + headline value)
   - Usage code block (3–5 representative invocations)
   - Short paragraph on key features / divergences
   - The four canonical links: `cargo install`, crates.io, docs.rs, source
2. Push. Done.

## Why a separate repo?

The SDDP planning workspace (`jsh562/rusty`) is private and contains spec-driven-dev artifacts that are noise to portfolio visitors. This repo exists solely to host the public landing page via GitHub Pages on a free personal account (free Pages requires a public repo).
