# Gem Progress Bars (dota++)

Two head-to-head gem level bars under the top bar, one per team, styled like a fighting game's health bars (Street Fighter, Tekken). Radiant is green and fills left → right; Dire is reddish-orange and fills right → left.

Reference: `reference.webp` (Felipe's draft). Prototype: `index.html`. Asset list: [assets.html](assets.html).

## Placement

- Centered horizontally, `--bars-top` (50px) from the top: just under the vanilla top bar.
- Layout: `[Radiant panel][center plate][Dire panel]`; the center column is wide enough for the plate plus the overhanging logos, each panel `--side-w` × `--side-h` (520×48), the center plate `--center-w` × `--center-h` (114×48).

## Anatomy

- **Panels:** styled after the vanilla top bar, with a light top edge and a dark bottom edge (`--topbar-edge`). The outer ends are slanted (`--side-skew`), like the top bar's hero slots. All CSS. The background is **semitransparent** (`--topbar-glass`), like the score boxes beside the clock, so the game shows through. The bar track stays solid dark, so the fill keeps its contrast. (A solid background like the hero slots was tested and dropped.)
- **Center plate:** semitransparent like the panels (`--topbar-glass`, with the same light top edge), in the trapezoid shape of the vanilla clock plate (bottom corners pulled in by `--center-taper`). It holds a gold "VS" and the gem shard. (The textured `topbar_center_bg.png` was tried first and replaced so the whole element reads as one semitransparent strip.)
- **Inside each panel, from the outside in:**
  1. **"LVL N"**, the team's gem level (`--fs-level`).
  2. **The bar** (`--bar-h` 22px), slanted to match the panel. A dark inset track, the team-coloured fill (`--radiant-fill` / `--dire-fill`) with a glossy top band (`--bar-gloss`).
  3. **Team logo** (`--logo` 56px), centered `--logo-offset` (6px) past the panel's inner end, so the circle covers the panel's end, including its slanted top corner. No panel edge shows around the logo.
- **Points on hover:** hovering a panel shows "points / cost" centered in the bar (`--fs-points`).

## States

The bars have two states. **Compact** is triggered by ALT held **or** a notification on screen; either one is enough, so holding ALT during a notification changes nothing.

| State | Trigger | What happens |
|---|---|---|
| **Full** | Neither | Full bars at `--bars-top`. |
| **Compact** | Player holds ALT (intentional input), **or** a big top-of-screen notification appears (first blood, kill streaks, etc.) | Bars shrink to thin strips and move down to `--compact-top` over `--t-move` / `--t-morph`. They return to full when ALT is released **and** no notification is up. |

**Compact version:**
- The two fills, `--thin-h` (10px) tall, with a thin border (`--thin-border`: a dark outline plus a faint light rim) so they separate from busy backgrounds.
- **Kept, shrunk:** the team logos (`--thin-logo` 24px, overhanging the strips' inner ends like the full ones) and the gem shard in the middle (`--thin-shard` 22px). They keep the "us vs them" and "this is about gems" reading without the full layout.
- **Dropped:** the "LVL N" labels, the "VS", the center plate and the panel backgrounds. The element is about 24px tall in total.
- Strip top at `--compact-top` (150px); the mini logos reach 7px above it. That's below a typical two-line notification (a rampage or first blood ends at y ~131–140), and the lowest icon that ALT reveals, the round TP scroll timer under the hero portrait (y 87–122). That also clears "UNTIL HORN" and the respawn timers.
- The two strips sit close together at the screen's center, `--thin-gap` (72px) apart: just room for the logos and the shard.
- The point is that team progress stays glanceable while the vanilla UI has the stage. (Hiding the bars entirely was considered and dropped in favour of this.)

### Gaining points

1. The **gain segment** (`--radiant-gain` / `--dire-gain`, a pale tint) jumps to the new value at once.
2. After `--t-gain-hold` (300ms), the **fill** catches up over `--t-fill`.

This is the fighting-game "recent damage" trail, reversed for gains: you see how much was just added.

### Level up

When the points reach the level cost:
1. The fill reaches the end.
2. **Flash** (medium, `--t-levelup` 700ms): one white pulse over the bar, a glow of the team colour around it, a ring burst on the logo, and the level number pops.
3. The bar resets to empty with level +1. Any overflow points are then added as a normal gain.

Gains are queued per team, so a big gain that crosses a level plays in order.

## Prototype-only layers

- The stage background swaps per state, to show what each state makes room for:
  - `context-normal.webp`: default.
  - `context-alt.webp`: ALT held.
  - `context-notify-rampage.webp`: a rampage notification, a typical two-line announcement. `context-notify-firstblood.webp` and `context-notify-triplekill.webp` are kept for reference.
- "Prototype controls" box: point gains for each team, ALT toggle, simulated rampage (4s), reset, asset list. The real ALT key works too.
- Deep links to states: `?alt=on`, `?notify=on`, or both.

## Assumptions / open questions

- **Level cost** is a flat 500 points per level (`DATA.levelCost`). The real curve probably grows per level.
- **Three-line notifications (accepted overlap):** first blood immediately followed by a multi-kill stacks a third line, down to y ~165, which the strips cross. It's rare enough that the position is tuned for the common two-line case instead.
- **What counts as a "notification":** the prototype only has an on/off flag. The game needs the list of events that trigger it (first blood, streaks, Roshan, Aegis, …) and a duration for each, or a hook on the vanilla notification panel's visibility.
- Should a level-up that happens in the compact state (ALT or notification) wait and flash once the bars are back to full size? Right now it flashes on the thin strip.
- The "VS" and shard in the center plate come from the draft. The plate's original contents (clock, day/night) are not part of this element.

## Notes for a Panorama implementation

- Panorama scales from the 1080p reference, so the token sizes can be used directly.
- The slanted ends use `skewX` in the prototype. If Panorama's transform support doesn't cover skew, use slanted end-cap images for the panel and bar instead.
- Bar fill: a child panel with `width` set to the percentage; Dire's anchors right (`horizontal-align: right`).
- Compact state: one class on the root panel, set while ALT is held (Panorama can read the ALT key state like the vanilla HUD does) or while a notification is up. The slide is a `transition` on `y` or `margin-top`, the shrink a `transition` on `height`.
- The center plate's trapezoid uses `clip-path` in the prototype. If Panorama can't clip, use a semitransparent trapezoid image instead (e.g. `topbar_center_bg.png` re-exported at the panels' opacity).
- No CSS filters are used; glows are `box-shadow` animations, which Panorama supports.
