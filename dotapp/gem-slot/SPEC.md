# Gem Slot — dota++

Replaces the old "Gems Level N!" panel (Accept / Reroll / Later). It pops up when the player gains a gem, and **one click on an ability slots the gem there**. The old flow was: open the bench, pick the gem, drag it onto an ability, close the bench.

- Prototype: `index.html` (serve the `ui-playground` folder, e.g. `python -m http.server`, then open `/dotapp/gem-slot/`)
- Original concept: `reference.webp`
- Backdrop: `context-ingame.webp`, an in-game 1080p screenshot. The old panel has been painted out; the Gems Bench is open in it.

## Placement

The whole prototype is a **1920×1080 stage** scaled to fit the window, so every size in the tokens is a 1080p pixel size.

The panel is a **stone slab docked flush to the right screen edge**, so it reads as part of the HUD rather than a floating window:
- No right border, and nothing between it and the screen edge.
- **Anchored from the top** (`--dock-top`, 120 px). The height grows downward with content and never touches the bottom-right HUD; the tallest case, an epic Mobility gem, ends about 25 px above the quick-buy box.
- **Three stacked pieces**, each a `clip-path` shape with its own `--rim` outline (left off the screen-edge side) and the tier material:
  1. **Main piece**: the crest (gem + name) and the slab as **one shape**, so there's no seam between them.
  2. **Upper brace tier**, tucked `--brace-overlap` under the main piece.
  3. **Lower brace tier**, tucked under the upper one.

  Each piece casts `--layer-shadow` (a short downward drop shadow) onto the piece behind it, and each lower tier is a little darker (`--shade`), so the brace reads as stacked slabs rather than one cut rock.
- **Crest:** the gem and its name share one trapezoid (`--crest-w` wide, sides angled over `--crest-slope`) that rises above the slab.
  - The gem is the most prominent element. Three subtle layers separate it from every panel color, including same-hue cases like a Fortune gem on its yellow panel:
    1. a **thin white-ish border** around the icon (`--gem-ring`, 1.5px at 70% white). It's neutral, so it reads the same on every tinted panel. Soft halos behind the gem were tried and dropped: dark ones muddied the tints, light ones read as a smudge or glared.
    2. a **glow** on the icon, tinted by rarity (`--common/rare/epic-gem-glow`)
    3. a **contact shadow** below it (`--gem-shadow`)
  - The socket and labels are vertically centered on the visible header, which runs from the crest's top to the first row (header + slab padding).
- **Dice tab:** the gold reroll tab sits **behind** the crest. The crest's right slope overlaps its left end and shades it, and its bottom tucks into the slab.
- **Brace:** a **stepped corbel** under the slab, wedged into the screen edge: two tiers (`--brace-tier` each), each starting with a diagonal cut (`--brace-cut`). The lower tier starts `--brace-step` in from the brace's left edge, leaving a short ledge. It is `--brace-w` wide at the top.
- The whole panel also casts a drop shadow onto the game, to the left.
- **Rows are depressions** carved into the slab: a translucent dark fill with an inner shadow and a faint lower lip, like the Gems Bench's storage well. Being translucent, they carry a hint of the panel color. The linked drawer is a deeper depression.
- The bottom button is **dark neutral stone**: the game's base button skin, darkened to near the Gems Bench's own color. It has no hue, so it never clashes with a gem type's panel (blue clashed with Utility, teal with Time). It reads as a raised button against the sunken rows.
- **Color = gem type.** Each piece's fill is a vertical gradient in the gem type's color, multiplied by the game's `cross_texture` line art (the same treatment as the orb bars in the earlier game). The hues come from the gem art (`--type-*` tokens, switched by `data-type` on `.dock`):

  | Type | Color | Type | Color | Type | Color |
  |---|---|---|---|---|---|
  | power | red | mobility | orange | time | cyan |
  | protection | gray | vitality | green | utility | blue |
  | destruction | pink | fortune | yellow | space | purple |
- **Rarity** (switched by `data-tier` on `.dock`) shows as **light rays behind the panel**, echoed by the gem's glow color:

  | | Behind the panel | Gem glow |
  |---|---|---|
  | common | nothing | white rim light |
  | rare | silver light rays (`--rare-ray-*`) | soft silver |
  | epic | gold light rays (`--epic-ray-*`) | soft gold |

  The rarity word under the gem name takes the same color. The panel and the rows look the same at every rarity. The gem art carries rarity too: epic icons have a slim gold rim, rare icons a silver one.

  Dropped along the way: outlines around the whole panel and around each row (the rays made them redundant), the `forge_socket` art behind the gem (it muddied the gem), and a metallic ring around the gem (it doubled up the gem art's own rim).

  The **light rays** are a mock-up of the in-engine particle effect. There are `--ray-count` wedges (16) fanning out from the panel's center over a soft glow, each fading along its length, the whole set turning once every `--t-rays` (24 s), blended additively (`screen`). They show where the panel doesn't cover them: left, above and below.

  The rarity word under the gem name takes the outline's color (`--c-rarity-*`).

## Anatomy

```
                                [LATER (n) ▶]║  ← above the panel, flush right; stays when the panel rolls out
  ╱‾‾‾‾‾‾‾‾‾‾‾‾‾‾‾‾‾‾‾‾‾‾‾‾‾‾‾‾‾╲            ║
 ╱ [gem]   GEM NAME (rarity)     ╲[dice xN]║  ← crest: gem + name in one piece; dice tab tucked behind it
 ┃┌───────────────────────────────────────┐║
 ┃│[icon]  Slot gem into                  │║  ← row (a depression); kicker + name pinned to the top
 ┃│[○○○]   ABILITY NAME                   │║
 ┃│        effect lines (vertically       │║  ← effects centered in the remaining height
 ┃│        centered)                      │║
 ┃└───────────────────────────────────────┘║
 ┃   └ 🔗 [linked icon] linked bonus lines ║  ← linked-ability drawer (only when it applies)
 ┃  … one row per ability …                ║
 ┃ [          Send gem to bench           ]║
 ╲_______________________________________ ║  ← screen edge on the right
                   ╲ upper tier          ║  ← brace: two separate pieces, each behind and shaded by the one above
                    ▔▔▔▔╲ lower tier     ║
                         ▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔║
```

- **Ability row**: the icon, with the ability's gem slots below it (an empty socket or the slotted gem). The slot group is horizontally centered under the icon, which matters when an ability has fewer than 3 slots. The icon column is vertically centered.
- **Linked-ability drawer**: some heroes have more than 4 abilities. The extra one is "linked": it inherits the gem bonuses of one of the main four (Shadow Shaman's Fowl Play gets its gems from Hex). The drawer hangs under the parent's row, indented by `--linked-indent`, and holds a chain icon, the linked ability's small icon (no slots) and its 1–2 bonus lines. It **only appears when the current gem grants the linked ability something**. Fowl Play has bonuses for Protection, Utility, Mobility, Fortune and Time only. It is part of the parent's click target.

All sizes, colors and timings are named tokens in the `:root` block at the top of `index.html`.

## Data model

| Concept | Rule |
|---|---|
| Abilities | Always the hero's abilities. They never change during a match. |
| Slots | `maxSlots` per ability. In game it grows 1 → 2 → 3 over a match. The demo fixes the ultimate (Mass Serpent Ward) at 2 and the others at 3, to show both cases. Slots fill in order. |
| Gem | `type × tier`. 9 types: destruction, fortune, mobility, power, protection, space, time, utility, vitality. 3 tiers: common, rare, epic. |
| Gem icon | `assets/t{tier}_{type}.png`: t1 has no rim, t2 a silver rim, t3 a gold rim. `gem_slot.png` is the empty socket. |
| Effects | `abilities[i].gems[type] → [line…]`. A line is a string, or `{ icon, text }` when it has an effect icon. The data is Shadow Shaman's real gem-upgrade values. |
| Linked ability | `abilities[i].linked = { name, icon, gems }`. `gems` has the same format as above and lists only the types that grant a bonus. |
| Effect text | `{a\|b\|c}` holds the value for tiers 1\|2\|3, and only the rolled tier's value is shown. A single `{a}` applies to every tier. Braced values render in the highlight color. |
| Effect icons | Optional. Lines without one span the full text width. |

## States & interactions

**UI must be snappy: no blocking animations.** The motion is:
- the slide-in on open (`--t-slide`, 160 ms, ease-out)
- the slide-out (`--t-slide-out`, 120 ms, ease-in)
- a gem flying to the bench (`--t-fly`, 380 ms), which doesn't block input
- a pop on a gem as it lands in the HUD or the bench

Input is locked only during a slide-out.

**Resolving a gem** (slot, swap or bench):
1. The HUD updates immediately.
2. The panel slides out (state `leaving`).
3. The next gem's panel slides straight back in (state `choosing`). In the prototype every gem is followed by another, random one, to simulate continuous level-ups.

| State | Entered by | What happens | Exits to |
|---|---|---|---|
| `choosing` | Panel opens (new gem) / reroll ends / cancel / back from LATER | Rows show hover feedback: the depression lightens. Every control is enabled. | `leaving`, `picking`, `rerolling`, `deferred` |
| — | **Click an ability (or its linked drawer) that has a free slot** | The gem goes into its first free slot, the HUD shows it at once, and the panel slides out. | `leaving` |
| `leaving` | Any resolution | Input locks. The panel slides out to the right, then the next gem's panel slides in. | `choosing` |
| `picking` | **Click an ability whose slots are all full** | That row gets a dark gold fill and a gold inner border. Its text becomes a single heading, **ALL SLOTS FULL**, with the hint "Click a gem to bench it and slot this one in its place." Its slotted gems become buttons, and hovering one previews the new gem in its place. The other rows dim but stay clickable. | see below |
| — | **Click one of the picking row's gems** | The new gem takes that slot. The old gem **flies from that slot into the bench**. | `leaving` |
| — | **Click another ability** while picking | Behaves like a normal click on that ability. | `leaving` / `picking` |
| — | **Click the picking row again, or Esc** | Cancels. | `choosing` |
| `rerolling` | **Click the dice tab** (costs 1 die; disabled at 0) | The panel **pops out and back in** (slide-out, then slide-in) with a new random gem: any type/tier other than the current one. Cancels picking. | `choosing` |
| — | **Send gem to bench** | The gem **flies from the medal into the next free spot of the Gems Bench** while the panel slides out. | `leaving` |
| `deferred` | **Click LATER (n)** | The panel rolls out to the right and stays out. The button stays on screen and becomes **"◀ GEMS (n)"**: same position, arrow flipped to point left. Clicks meant for the panel are ignored. | `choosing` |
| — | **Click GEMS (n)** | The panel slides back in with the same gem. The button returns to "LATER (n) ▶". | `choosing` |

`n` is the number of gems awaiting a decision. The prototype keeps it at 1.

### Supporter bonus

Supporters have a **25% chance** (`supporterUpgradeChance` in the data) that a newly gained gem arrives **upgraded one tier**: common → rare, or rare → epic. Epic gems can't be upgraded.

| State | Entered by | What happens | Exits to |
|---|---|---|---|
| `upgrading` | A new gem arrives and the bonus triggers | The panel slides in showing the **original** rarity, which holds for `--t-upgrade-hold` (250 ms). Then a white **flash** washes over the main piece (`--t-flash`, 360 ms). At its peak the panel switches to the upgraded rarity: the outline, the rarity label, the medal icon (with a quick pop) and the effect values all change. The notice **"Supporter bonus activated! Gem upgraded."** appears at the top of the slab. Input is locked for this ~0.4 s reveal. | `choosing` |

The notice stays visible while the upgraded gem is on offer, including after LATER → GEMS, and goes away when the gem is resolved or rerolled.

The bench cap (15) is handled by the game; the prototype ignores it.

Reduced motion (`prefers-reduced-motion`) sets every timing token to 0.

## Prototype-only layers

These are not part of the panel:
- **Live gem strips** over the screenshot's ability bar. They are positioned at Q/W/E/R using `HUD_SLOT_X` in the script, so they line up with this screenshot only.
- **Bench contents** over the screenshot's open Gems Bench: the counter (over its "0/15") and the stored gems in its well (`BENCH` in the script, `--bench-gem`). Benched gems fly to the next spot there.
- The dashed **Prototype controls** box at the top-left:
  - **Supporter** checkbox (on by default)
  - **Force an upgrade**: brings in a new common or rare gem with the bonus guaranteed
  - **Reset demo**
  - a log of the last action

## Materials & skins

| Surface | Art | How |
|---|---|---|
| Main piece, brace tiers | `cross_texture_tile.png` | The tier gradient, multiplied by the line art, inside each piece's shape. The tile is `cross_texture.png` mirrored 2×2 so it repeats without seams, drawn at half size (the game art is authored at 2×). |
| Rows, linked drawer | none | Translucent dark fill + inner shadow (`--well*` tokens) |
| Send gem to bench | `button_base.png` | 9-sliced: slice 6 → 3 px border; `brightness(.62)` for dark neutral stone |
| Dice / reroll tab | `button_active.png` (gold) | 9-sliced, as above |
| LATER / GEMS | `button_base.png` | 9-sliced, as above |

The button skins are drawn on a `::before` layer behind the label, so hover (`brightness`) and disabled (`grayscale`) filters never touch the text.

Mirroring the tile makes the line art symmetric, a soft diamond rhythm that mostly shows in the crest and the gutters between rows. A purpose-made seamless version of `cross_texture` would avoid that.

Not used: `bench_bg`, `bench_header`, `choice_bg` (they were per-piece skins, which caused seams between pieces), `glow`, `sub_bg_*`, the `forge_*` / `trash*` / `reset` / `stone_button` / `button_cancel|confirm` / `mouse_*` art, and the earlier ChatGPT renders (`stone_texture_0*`, `metal_texture_0*`).

## Assets

Full file list with links: [assets.html](assets.html) (also linked from the prototype controls).

| File | Status |
|---|---|
| `t{1-3}_{type}.png`, `gem_slot.png` | Final art (64×64). Epic icons use the slim, inset gold rim, so the gem sits clear of the rarity ring. |
| `ether-shock.png`, `hex.png`, `shackles.png`, `mass-serpent-ward.png`, `fowl-play.png` | Final art |
| `effect-mana.png`, `effect-heal.png`, `effect-hp.png`, `dice.png` | **Placeholders** cropped from `reference.webp`. Only mana-cost, healing and max-health lines have an icon so far. |
| `cross_texture_tile.png` | Derived from `cross_texture.png` (see Materials) |
| `button_base`, `button_active`, `button_active_v2` | Game art |
| Chain icon | Inline SVG |

## Assumptions / open questions

- Reroll odds are uniform across type and tier. The real weighting is unknown.
- The supporter bonus rolls only for **newly gained** gems. A rerolled gem never triggers it, and rerolling an upgraded gem replaces it with a normal roll.
- The bonus is decided when the gem arrives; the reveal is presentation only. A gem slotted after the reveal is the upgraded one.
- Row height varies with effect count (from 1 line up to 4), and the linked drawer appears and disappears, so the panel's height changes on reroll.
- The demo starts with Hex full (3/3), Shackles at 2/3, Ether Shock at 1/3 and Mass Serpent Ward at 0/2.
- When the Gems Bench is closed in game, the benched gem should fly to wherever the bench is reached from (its HUD button). The prototype only covers the open-bench case shown in the screenshot.

## Notes for a Panorama implementation

The prototype uses plain web CSS. Panorama's dialect is close but not identical; check these points against the Panorama docs:
- Layout uses `flow-children` rather than grid/flex.
- The pieces are `clip-path` polygons. In Panorama, the two brace tiers can be fixed-size images. The main piece needs a crest image plus a vertically stretching slab, cut so their join falls inside flat fill, which keeps it seamless. The between-piece shadows map to Panorama's `box-shadow`, or can be baked into the images.
- The dice tab sits behind the crest, so the header box is click-through and only the tab takes clicks. The strip of the tab hidden under the crest's slope is clickable too.
- The tier material is a gradient multiplied by a grayscale line texture. In Panorama, `wash-color` on a grayscale texture panel gives the same result.
- CSS custom properties (`--tokens`) map to Panorama's `@define` variables.
- The button skins are 9-sliced with `border-image`. If Panorama has no 9-slice, author them at size.
- The row group (row + linked drawer) is one click target with the slot buttons layered above it. In Panorama, a row panel with `onactivate` plus child slot panels that only accept input while picking (e.g. toggled with `hittest`).
- The slides are a `translateX` between 100% and 0, which maps to Panorama transitions on classes added on open and close. The bench flight is a short-lived panel animated from the medal to the bench slot.
