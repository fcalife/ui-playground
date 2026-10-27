# UI Playground

Umbrella folder for UI mockups. Each mockup turns a static concept (screenshot, sketch, generated image) into a functional prototype built from real assets and real geometry.

## Goals

1. **Experiment cheaply.** Moving, resizing, swapping or adding UI pieces should mean editing a value or a data entry, not redrawing anything.
2. **Hand off.** Once a layout is decided, the prototype plus its SPEC.md is the reference the developer implements from, in their own engine. Fidelity of behavior and structure matters more than pixel-perfection.

## Structure

```
<game>/<screen>/
  index.html     prototype; opens directly in a browser, no build step
  assets/        real art (icons, textures); placeholders are marked as such
  reference.*    the original concept image, kept for comparison
  SPEC.md        states, interactions, tokens, open questions — for the developer
```

Folder names are path- and URL-safe (e.g. `dotapp` for "dota++").

## Publishing (GitHub Pages)

The repo root is the site. `index.html` is a hand-kept list: one `<section>` per project and one `<li>` per mockup, linking to `<game>/<screen>/` and its spec. The spec link is `SPEC.html` (GitHub Pages' default Jekyll build renders each `SPEC.md` as `SPEC.html`), unless Felipe keeps that mockup's spec in a Google Doc. Gem Slot links to its Doc. When adding a mockup, add its entry to `index.html`. Keep every path relative (the site lives under `/ui-playground/`), and list large unused files under `exclude` in `_config.yml` so they stay unpublished.

## Conventions

- Plain HTML/CSS/JS in one file. No frameworks, bundlers or package installs unless a mockup truly needs one.
- Never use diffusion-generated images of the UI itself. Frames, panels, bevels and buttons are CSS geometry; art is real asset files.
- Content lives in one data object at the top of the script; markup is rendered from it.
- Every size, color and timing is a CSS custom property in one `:root` token block — that block doubles as the measurements spec.
- Interactions are explicit states (e.g. idle → hover → selected → resolved), toggled via a class or `data-state` on the root, and each state is described in SPEC.md.
- Behavior that was assumed rather than specified is listed under "Assumptions" in SPEC.md.
- Each project has a single user-facing UI language (usually English); code is always English.
