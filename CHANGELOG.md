# Changelog: Simple Match

All notable changes to this package.

---

## [1.0.0]

First release.

### Puzzles

- **The rules:** swap two neighbouring pieces to line up three or more of a kind. Lines clear, pieces fall, falling
  pieces that line up clear too (cascades), and a swap that makes no line doesn't happen. Lines that touch clear as one
  group.
- **Goals:** clear the board, collect pieces of given shapes, clear every layer, or bring every exit piece down, within a
  move limit of par plus the moves to spare you choose.
- **Boards:** 3 to 12 cells each way with 2 to 6 piece types, on the full rectangle, a cross, ring, diamond, circle,
  heart, L, T, U or your own shape, with obstacles if you want them.
- **Bonus pieces:** four in a line makes a Line clear, an L, T or plus a Pattern clear (its area paintable), five in a
  line a Type clear, and two bonus pieces swapped together make a combo. Bonus puzzles can be asked to need them.
- **Gimmicks:** layered cells, breakable crates, fixed pieces, type filters, spreading moss, countdown pieces, exit pieces
  and conveyor belts, each placed only where the puzzle's win still works and kept only when it changes the puzzle.
  Portals, gravity in any direction and spawners are in the rules, the solver, the pictures and the demo, for placing by
  hand.
- **Refill levels:** a full board where new pieces drop in from a seeded stream after every fall and the board reshuffles
  when no swap is left, so a level plays the same every time. Collect, clear the layers, or bring down exit pieces that
  drop in.
- Every piece type and every gimmick has its own shape as well as its colour, so colour-blind players can tell them apart.

### Solving and rating

- An exact solver over every reachable position gives the **proven par**, one fewest-move solution, the number of them,
  and how many first swaps start one. A search the budget cuts short is marked as not proven, and the generator never
  accepts one.
- **Difficulty** (1 to 5 stars) from the exact chance that a player swapping at random wins within the move limit, with
  bands measured over 293 everyday and 234 bonus puzzles; refill levels from 400 seeded half-greedy players, measured over
  112 refill levels.
- `MatchSolver.Outlook`: can a position in play still be won? Won, can win (in how many moves, along which line, the
  hint), can't (and why, in the player's words) or unknown when its budget runs out (never wrong).

### Generation

- `MatchGenerator.Generate`, `GenerateVerified` (keeps trying seeds until a puzzle is solvable, proven, inside the par
  window, on its star target and not a freebie, and can be cancelled or stopped at a time limit) and `GenerateBatch`.
- Puzzle-mode boards are built backwards from the goal; bonus boards are dealt full and kept only when the solver proves
  them; refill levels are proven over their stream.
- Deterministic: the same settings and seed always give the same puzzle, and every puzzle stores the seed that made it.
- Every miss is written to the puzzle's notes. Nothing is quietly downgraded.
- 27 presets, each measured on 24 seeds, and **Auto**, which fixes settings that fight each other and says what it
  changed.

### Playing

- `MatchRules`: the play rules your game uses, the same ones the solver counted with: swaps with the reason a swap is
  refused, every wave of a move (matches, bonus pieces made and fired, combos, layers, crates, moss, exits, belts,
  countdowns, refills and reshuffles), a traced move with every step and the position after it for animating, the goal
  check and a data validator.

### Editor

- The **Match Generator** window: every setting with tooltips, a live warning of what Auto would change, presets, a seed
  you can keep, batches of up to 50 built on a background thread with progress, Cancel and a time limit, Only on target,
  and Save as a puzzle, a pack or pictures.
- The **analysis pane**, in the window and in both inspectors: the goal in words, the verdict, a picture of the puzzle with
  a plain-words legend, a slider that steps through the solution with each move's caption, the facts and the notes.
- **Export** as PNG or SVG: the colour view, a printable black on white sheet that tells every piece by its shape alone,
  or an answer key with the solution's swaps numbered. A pack exports every puzzle in one go.
- **Inspectors** with Verify (solve again and restamp the verdict) and Re-roll (a new puzzle from the same settings).

### Demo

- One scene and one component, `MatchDemo`, which builds everything in code with no prefabs, models, textures or audio
  files.
- A welcome screen, then four modes: Clear, Bonus Pieces, Gimmicks and Refill. Each has Endless puzzles made to your choice
  of kind and difficulty, and a level pack that climbs from 1 star to 5. The demo ships with 48 levels.
- Every step of a move is shown: the swap, matches popping, pieces falling along their real paths, new pieces sliding in,
  belts moving, and a reshuffle that lifts each piece to its own height so none passes through another.
- Play has undo, restart, hints, a solver that plays the rest, a warning when the level can't be won any more (it can be
  turned off), a camera you can turn, tilt and zoom, and moves against the limit.
- Tap or drag to swap, with keyboard shortcuts for everything else.
- Sounds and effects made in code, the same in every render pipeline.
- Works in URP, HDRP and the Built-in pipeline. On HDRP a small volume gives it the same look, and a build step keeps the
  demo's shader in builds so it never draws pink.

### Documentation

- Offline HTML documentation (Home, Quick Start, Generating, API Reference, Demos, Troubleshooting and About, plus a
  single-page version), opened from **Window > Living Failure > Simple Match > Documentation**.
