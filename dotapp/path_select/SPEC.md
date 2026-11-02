# Path Select (dota++)

A large modal shown at match start, right after the tutorial popup. The local player picks one of three **paths** for their hero, an auto-build that decides which 1–2 gem types each ability gets this match, or **manual mode** to pick every gem themselves. Then they confirm. No timer, no team visibility: this is local only.

Prototype: `index.html` (layout "Crossroads", chosen from three drafts; see the end). Asset list: [assets.html](assets.html).

## Look

**Surfaces:** Dota's alternation of solid slabs (`--slab`, `--slab-deep`) for structure, and a semitransparent gradient (`--glass`) for the panel body over the dimmed game (`--backdrop`). **Gold (`--c-gold`) means "selected", and only that.** Each path has an accent colour (`--accent-*`), used for its top edge, glow and stat pips.

**Panel:** `--panel-w` × `--panel-h` (1480×920), centered.
- **Header slab:** the hero's **minimap icon** at exactly 2× (`--minimap-icon` 64px; it's pixel art, so only whole scale factors, rendered pixelated), "CHOOSE YOUR PATH", and one line: hero name plus what a path does.
- **Body:** the three columns below.
- **Footer slab:** a status hint ("Choose a path to continue" / "Selected: …") and the **Confirm** button (Dota's green "go" button), right-aligned.

**A path shows:** name, one-line tagline, three ratings (Damage, Survival, Mobility, 1–3 pips), a crest of its two lead gems (epic-tier art), and for each ability (Q W E R), its 1–2 gem types.

**Manual mode** is deliberately quieter than the paths: no accent, no crest, the "unknown gem" icon.

## States

| State | Trigger | What happens |
|---|---|---|
| `choosing`, nothing selected | Panel opens | Confirm is disabled; hint says "Choose a path to continue". |
| Hover | Pointer (or keyboard focus) on a path | The slab slides right; the detail pane previews it. |
| `choosing`, selected | Click a path or manual mode | Gold frame on it; other options dim. Confirm enables: "Confirm path" or "Confirm manual mode". Clicking another option switches the selection. |
| `confirmed` | Confirm | Panel fades out over `--t-exit`. The prototype shows a "Path locked in: …" toast in its place. |

Selection works with keyboard too: every option is a button with `aria-pressed`.

## Layout

Three columns: the hero, the paths, the detail.
- **Hero column** (`--col-hero`): portrait, name and the four ability icons. (What a path does is explained once, in the header.)
- **Paths column** (`--col-forks`): three slanted slabs (`--fork-skew`) with the crest gems and the name; manual mode is a fourth, thinner, dimmer slab. Hover slides a slab `--fork-hover-shift` right; selected slides `--fork-sel-shift` with a gold frame, and the other slabs dim.
- **Detail pane:** shows the **hovered** path (keyboard focus too), else the selected one: big name, tagline, ratings, and each ability with its gems **named** under the icons. For manual mode, each ability shows the "?" gem labelled "Your pick". With nothing hovered or selected: "Hover a path to preview its build". It swaps instantly (a crossfade was considered and judged unnecessary).

## Experiments (toggles in the prototype controls, off by default)

- **Hero preview** (`?hero=on`): the hero column previews the hovered / selected path: its lead gem as a crest over the portrait's corner (`--hero-crest`), and each ability icon gets that path's gems underneath (`--hero-gem`). Doubt noted: the current hero column's empty space is part of the balance.
- **Slab ratings** (`?ratings=on`): each slab shows its Damage / Survival / Mobility pips, so paths compare without hovering. The paths column widens to `--col-forks-rated` and slab names shrink to fit; the detail title shrinks so long names still fit.

## Prototype-only layers

- `context-ingame.webp` behind the dimmed backdrop.
- Prototype controls: the two experiments, reset, asset list. Deep links: `?pick=stone&hero=on&ratings=on` (pick: `fury`, `stone`, `tricks`, `manual`).

## Assumptions / open questions

- **All path content is placeholder**: the names (Boundless Fury, Stone Hide, Seventy-Two Tricks), taglines, gem picks and ratings. Each path does follow the brief: 1–2 gem types per ability.
- **Portrait** is the in-game asset at its maximum size (256×144), shown slightly upscaled (~316px wide).
- **Ratings** (Damage / Survival / Mobility) are an assumption: a quick way to compare paths without reading. Are they useful, and are these the right three?
- Gem **tiers** in the crest use epic art for impact; ability rows use rare art. The tier carries no meaning here.
- Can the player change their path later in the match? Not covered here.
- After confirming, does the panel open anything (e.g. the first gem pick for manual mode), or just close?

## Notes for a Panorama implementation

- Panorama scales from the 1080p reference, so the token sizes can be used directly.
- Glows and frames are `box-shadow`. The crest gems use `filter: drop-shadow`, and the disabled Confirm uses `grayscale`: swap those for baked shadows / a disabled style if Panorama's filter support falls short.
- The slanted slabs use `skewX`; if unsupported, use slanted background images.
- Paths are data (`PATHS` in the prototype): one entry per path with name, tagline, crest, ratings and gems per ability key.

## Alternatives considered

Two other layouts were drafted and dropped in favour of this one:
- **Three Banners:** three tall hero-pick-style cards, each with crest, ratings and its full per-ability build. Strongest at a glance, but comparing one ability across paths meant scanning three cards.
- **Three Roads:** a table with abilities as rows and paths as columns, a whole column selectable. Best for comparison, least showy.
