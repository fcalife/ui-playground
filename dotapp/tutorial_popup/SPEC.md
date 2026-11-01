# Tutorial Popup (dota++)

A modal shown once at match start that explains the gem loop in three steps: **do stuff → get gems → power up**. It's static: no animation or step-by-step reveal. The copy is deliberately short and casual. That's final copy, not a placeholder.

Reference: `reference.webp` (Felipe's sketch). Prototype: `index.html`. Asset list: [assets.html](assets.html).

## Placement

- Centered on the 1920×1080 reference stage, `--panel-w` wide (1168, the sketch at 1:1). Height follows the content (about 880).
- The game behind is dimmed by a full-screen backdrop (`--c-dim`), which also blocks input to the game while the popup is open.
- Panel background is `bg.webp`, covering the panel, with a thin border and a drop shadow onto the game.

## Anatomy

- **Title:** "Welcome to [logo] !", centered across both columns.
- **Close button (×):** top-right. Closes the popup. Esc does the same.
- **Step numbers** are gold (`--c-step`), the panel's one accent colour, so the 1→2→3 order reads at a glance. Headings stay white.
- **Left column, step 1 "Do stuff":**
  - Heading with an **"i" button**. Its tooltip (hover or keyboard focus) lists **every** way to earn gem points; the rows below show only the main ones.
  - Five reward rows: `player icon · verb icon · target = shard xN`. The verb is either a kill (crossed swords) or a pick-up (hand). Columns line up across rows. The tower and Roshan portraits use the larger cell (`--icon-big`). Every target sits on a faint round plate (`--target-plate`) so dark portraits don't sink into the background. The amounts are the variable part of each row, so they're bold and larger (`--fs-amount`).
- **Right column, step 2 "Get Gems":** `shard x75 = random gem (?)`. Its icons are a size up (`--exchange-shard`, `--exchange-gem`): it's the hinge of the loop.
- **Right column, step 3 "Power UP!!!":** three rows, one per gem tier:
  - `ability card + gem = ability card with the gem slotted`, then "DMG UP!" in buff green (`--c-result`) with 1, 2 or 3 stacked chevrons (= tier), tinted white / silver / gold by tier (`--tier-1..3`) to match the gem rims.
  - The ability card is CSS: the ability icon with hotkey "Q" (top-left) and level "120" (bottom-right), and 4 level pips below. It has a light rim (`--card-border`) so it separates from the dark panel.
  - The **before** card has no gem slot and is dimmed (`--card-before-opacity`). The **after** card has **one** slot, centered above the icon, holding the gem with a gold ring (`--card-slot` 30px, `--slot-ring`). The slot is the only difference between the two cards, so it's sized to stay visible at 720p. The before card keeps an invisible slot-sized space so both icons line up.

Layout rhythm:
- The title is `--title-gap` above the step headings. That spacing separates the header; there's deliberately no rule under it.
- The two columns are separated by `--col-gap` with a faint vertical divider (`--divider`) in the middle. Steps are not boxed: their heights differ too much, and the divider plus spacing already separate them.
- Every step heading sits `--head-gap` above its content; step 2 and step 3 are `--step-gap` apart, so they read as two steps rather than one block.
- The five reward rows sit `--row-gap` apart and are centered vertically against the right column.
- "Do stuff" is centered on its own; the "i" button hangs to its right without shifting it.

All rows are rendered from `DATA` at the top of the script: adding, removing or reordering a reward or tier is one data entry.

## States

| State | Trigger | What happens |
|---|---|---|
| `open` | Match start | Backdrop and panel shown; focus on the close button |
| `closed` | × button or Esc | Backdrop and panel fade out over `--t-close` |

There's no way to reopen it in the game (not specified). The prototype's "Show popup" control is for the demo only.

## Prototype-only layers

- `context-ingame.webp`: in-game screenshot behind the modal.
- "Prototype controls" box: Show popup, Asset list.

## Assumptions / open questions

- **"i" tooltip copy is a placeholder.** It lists the five shown cases plus a "…other sources" line. The final full list goes into `DATA.info`.
- Row targets read as: lane creep, tower, Roshan, hero, bounty rune. The tower/hero labels are inferred from the art.
- The "player" icon (the banner) is shown on every row, as in the sketch.
- The filled slot on the result card shows the actual gem icon, so the tier is visible in the slot too.
- Should the popup show again in later matches, or only the first? Is there a "don't show again" option?
- Closing on backdrop click is **not** implemented (to avoid accidental dismissal at match start).

## Notes for a Panorama implementation

- Chevron tinting uses a CSS mask in the prototype; in Panorama, use `wash-color` on the chevron `Image`.
- No CSS filters are used for contrast (plates, rims and box-shadows only), since filter support in Panorama is limited.
- Panorama scales from the 1080p reference, so the token sizes can be used directly.
- The panel can be a single `Panel` with a background image; rows map well to a `Panel` per row with child `Image`s and `Label`s, built from a data table.
- The chevron stack is the same `chevron.png` repeated, each one overlapping the previous by `--chevron-overlap`.
