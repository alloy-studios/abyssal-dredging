# Abyssal Dredging — Rework Notes

What changed between the original single-file build and the reworked one.

| | Original | Reworked |
|---|---|---|
| File | `Abyssal Dredging - Upgraded.html` | `index.html` |
| Size | 2,370 lines · 166 KB | ~3,300 lines · 225 KB |
| Scene | DOM/CSS rig + rain canvas, top ~55% of the page | One full-bleed canvas, whole viewport |
| Layout | Fixed split: rig strip on top, control panel below | Floating collapsible docks over the scene |

The original is still in the folder untouched — delete it once you're happy.

---

## 1. The panel rework (the original ask)

The old layout hard-split the page: the rig got a strip at the top, a static control
panel took the rest, and nothing could be moved or hidden.

- **Four independent docks** — Upgrades (left), Cargo Hold (right), Extraction Log
  (bottom-right), Console (bottom-centre). Each folds to a thin labelled tab on the
  screen edge with a click.
- **Focus mode** — one button (or `Tab`) folds all four at once. Nothing on screen but
  the sea.
- **Dock state saves** with the rest of the game.
- **Phones start folded**, so a first-time player on mobile lands on the water instead
  of behind a drawer.
- The old mobile "Upgrades / Cargo Hold" tab bar is gone — folding replaces it, and the
  same interaction now works on desktop.

### Controls

| Key | Action |
|---|---|
| `Tab` | Focus mode — fold/unfold everything |
| `Space` | Dredge |
| `1` `2` `3` | Fold Upgrades / Cargo / Log |
| `Esc` | Close any modal |

---

## 2. The ocean (the substance of the rework)

Previously the cable dropped off the bottom of the rig strip and the haul only ever
appeared as text in a list. Now there is an actual water column and the net travels
down into it.

**Direction**, written into the top of the file so future edits have something to steer
by: a wet steel deck at night, anchored on *DREDGE* (Black Salt Games) for the cold
storm sea, plus oil-rig sodium lighting — one warm lamp, everything else cold. Rarity
hues are **reserved**: if something on screen is coloured, it's a find.

### Above water
- Storm sky with two parallax cloud bands and a horizon haze.
- Rain as real streaks (120 drops, 210 in a storm) that punch ripple rings into the
  surface where they land.
- Lightning draws **branching bolts**, not just the old full-screen flash.
- Distant derelict silhouettes on the horizon for depth.
- A jack-up rig drawn in full: lattice legs, deck plate and railing, derrick A-frame,
  jib boom with a turning sheave, winch drum, and a sodium lamp with a feathered light
  cone. The submerged part of each leg is drawn *before* the water so the sea tints it
  and the rig stands **in** the ocean rather than on it.
- Wave surface from summed sine harmonics, with a foam scatter band and a crest that
  varies along its length.

### Below water
- Depth-graded column running from lit shelf down to true black, with drifting
  thermocline strata.
- Marine snow in three parallax layers.
- Drifting jellyfish with pulsing bells and trailing tentacles.
- Seeded bioluminescent points that pulse in the deep.
- A **sunken salvage field** — broken hull with exposed ribs, a collapsed rig leg, a
  spilled container stack — silhouetted against backscatter murk so it reads instead of
  vanishing into black.
- A leviathan silhouette that crosses the deep roughly every one to two minutes.
- Bubbles running up the cable while the winch is moving.

### Feel
- Trauma-based screen shake (`shake = trauma²`) on dredge start, surfacing, upgrades,
  lightning and Descent.
- Splash burst and expanding ripple rings when the net breaks the surface.
- Hauled items fly from the net to the Cargo Hold on arcing paths with tapered
  motion trails in their rarity colour.
- Grain and vignette pass over the whole frame.

---

## 3. One gameplay change

**The loot roll now happens at the bottom of the dive, not at the end.**

In the original everything was rolled when the timer expired and reported as text. Now
the roll resolves when the net reaches depth, so the catch physically rides up through
the water — each item drawn as a seeded silhouette rim-lit in its rarity colour, with
the net glowing in the colour of the best item in the bag.

You read the result off the ocean before the log tells you.

**No balance was touched.** Every drop chance, upgrade cost, cost multiplier, max level,
crush yield, the Pressure Core formula (`floor(sqrt(lifetimeScrap / 400))`) and all rank
thresholds are byte-identical to the original. Only upgrade *description* text was
rewritten.

---

## 4. New settings

| Setting | Original | Now |
|---|---|---|
| Auto-scrap ceiling | Uncommon / Rare / Epic only | Full ladder through **Absolute** |
| Cutscene skipping | Single on/off "Always Skip" | **Per-tier threshold** |
| Find pop-ups | Always on | Toggle |
| Storm banner | Always on | Separate toggle |

### Auto-scrap
Now covers Uncommon → Rare → Epic → Legendary → Mythic → Eldritch → Unfathomable →
Everything. Anything from Legendary up turns the picker rust-red and shows a warning,
because it can eat a 1-in-50-million pull.

Order of operations is deliberate: **the cutscene fires and the Collection records the
discovery before the crush.** You keep the reveal and the permanent completion credit;
you just don't keep the item.

### Cutscene skip threshold
*Never skip · Mythic and below · Eldritch and below · Unfathomable and below · Skip all.*
Anything at or under the chosen tier becomes a pop-up; anything rarer still plays.

| Setting | Mythic | Eldritch | Unfathomable | Absolute |
|---|---|---|---|---|
| Never skip | plays | plays | plays | plays |
| Mythic and below | skip | plays | plays | plays |
| Eldritch and below | skip | skip | plays | plays |
| Unfathomable and below | skip | skip | skip | plays |
| Skip all | skip | skip | skip | skip |

The overlay's old "Always Skip" checkbox is now a button labelled with the tier you're
actually watching — *"Always skip Eldritch and below"* — so you can silence one band
mid-cutscene without demoting the rarer ones.

Old saves migrate automatically: `Always Skip = on` becomes **Skip all**, off becomes
**Never skip**.

---

## 5. Bugs fixed that were in the original

- **Cursor vanished during cutscenes.** The stylesheet set `cursor: none` on the active
  overlay, so you couldn't see what you were aiming at to hit Skip. Removed.
- **Cutscene hero text overflowed narrow windows.** It was a fixed `4.5rem`; on anything
  smaller than a wide desktop the text ran off both edges. Now clamped responsively.

### Hardening in the new code
- The depth gauge read `NaN m` if the browser fired a zero-size layout pass before the
  canvas had dimensions. Guarded, with safe pre-layout defaults and a `ResizeObserver`
  on the canvas.

---

## 6. Carried over unchanged

Reused verbatim rather than rewritten, so nothing was lost in translation:

- The entire procedural `AudioSystem` — rain bed, thunder, winch clang/grind, and all
  the per-tier cutscene drones including the 18-second Absolute sequence.
- The `CutsceneParticles` engine and all ten per-item particle presets.
- All ten cutscene rune emblems and the full cutscene stylesheet (391 lines).
- All 9 rarity tiers and 34 items with their exact odds.
- Pressure Cores / Descend prestige, the Curator's Collection and its +25% completion
  bonus, rank titles, abyssal storms, auto-dredge, the odds table.

---

## 7. Running it

Just open `index.html` — it's a single self-contained file, no server or build step.

```bash
start "" "index.html"
```

`.claude/launch.json` is only there for the local preview server used during
development; deleting it does nothing to the game.
