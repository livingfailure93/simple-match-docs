# Simple Match

**Match-3 puzzles for Unity that are generated, solved exactly and rated.**

Simple Match builds a match-3 puzzle from your settings, then proves it can be won and works out its **par**, the fewest
moves it takes. It makes puzzles to clear the board, collect pieces, peel layers off or bring exit pieces down, with bonus
pieces, crates, moss, countdowns, belts, portals and more, and refill levels where new pieces drop in from a seeded stream.
Every puzzle is rated 1 to 5 stars and can be saved as a ScriptableObject, exported as a PNG or SVG picture, or played in the
included 3D demo, which can also tell the player when a level can't be won any more.

*Documentation:* `Window > Living Failure > Simple Match > Documentation` (offline HTML, in `Documentation/`).

---

## The rules

Swap two pieces side by side to line up three or more of a kind in a row or a column. The line clears, the pieces above
fall into the gap, and falling pieces that line up clear too (a cascade). A swap that makes no line doesn't happen. Lines
that touch clear as one group, so an L, a T or a plus is one match. Four in a line makes a **Line clear**, an L, T or plus a
**Pattern clear**, five in a line a **Type clear**, and two bonus pieces swapped together set off a combo.

| Gimmick | What it does |
|---|---|
| **Shaped boards, obstacles** | a diamond, heart, ring or your own shape; cells nothing enters (pieces rest on them). |
| **Layered cells** | 1 to 3 layers under the piece; each match on the cell takes one off. |
| **Breakables** (crates) | fill their cell; 1 to 3 hits from matches beside them or bonus pieces over them. |
| **Fixed pieces** | can't be swapped and never fall; only a match clears them. |
| **Type filters** | a cell that takes only one shape. |
| **Spreading** (moss) | after a move that clears none of it, it takes a piece beside it. |
| **Countdown pieces** | clear them before their moves run out, or the level is lost. |
| **Exit pieces** | never match; bring them down onto an exit and they leave. |
| **Conveyor belts** | after every move they carry their pieces one cell along. |
| **Portals, gravity** | a falling piece comes out somewhere else; cells that fall up, left or right. |
| **Spawners** | refill levels: new pieces drop in only from spawner cells. |

Goals: **clear the board**, **collect** pieces of given shapes, **clear every layer**, or **bring every exit piece down**,
within a move limit (par plus the moves to spare you choose). Every piece type and every gimmick has its own shape as well
as its colour, in the pictures and in the demo, so colour-blind players can tell them apart.

---

## What makes it "verified"

- **The par is real.** The solver searches every position the puzzle can reach, so a 7-move puzzle really takes 7 moves.
  A search cut short is marked as not proven, and the generator never accepts one.
- **It can be won.** Puzzle-mode boards are built backwards from the goal and proven by the solver. Refill levels draw
  their new pieces from a seeded stream, so they play the same every time and are proven over it.
- **Stars from measurement.** Puzzle mode: the exact chance that a player swapping at random wins within the move limit,
  with bands set over 293 everyday and 234 bonus puzzles so each star covers about a fifth of them. Refill levels: how often
  400 seeded half-greedy players win, measured over 112 refill levels. Ask for a star count and the generator keeps trying
  until it gets one.
- **Every gimmick matters.** A crate, fixed piece, filter, belt or countdown is only kept when the puzzle really uses it,
  and a bonus puzzle can be asked to need its bonus pieces.
- **Can this still be won?** `MatchSolver.Outlook` answers it for any position in play: won, can win (in how many moves,
  along which line: the hint and auto-solve), or can't (and why).
- **Misses are reported.** When a star count, a par window or a gimmick can't be met, the puzzle's notes say what happened
  and what you got instead.

---

## Quick start

1. Open **Window > Living Failure > Simple Match > Match Generator**.
2. Press **Presets**, pick one (for example *Gimmicks/Breakables*) and press **Generate**. Puzzles are built and solved on a
   background thread, with a progress bar, Cancel and a time limit. **Auto** fixes settings that fight each other.
3. Read the pane: the goal in words, the par, the stars, the picture, and every move of the solution step by step.
4. **Save** the selected puzzle as an asset, the whole set as a pack, or pictures of the set as PNG or SVG.
5. Open `Scenes/SimpleMatchDemo.unity` and press Play.

### From code

```csharp
using SimpleMatch;

var settings = MatchPresets.Find("Gimmicks/Breakables").Make();
settings.Seed = "level-1";                       // the same seed, the same puzzle
settings.TargetDifficulty = 3;                   // keep trying until it rates 3 stars
MatchData puzzle = MatchGenerator.GenerateVerified(settings);
Debug.Log($"{MatchNarration.GoalSentence(puzzle)}: par {puzzle.Par}, {puzzle.Difficulty} stars");
```

Run your own game on it with the same rules the solver used, so your game always agrees with the par:

```csharp
MatchBoard board = puzzle.Board();
MatchState state = puzzle.Start();
if (MatchRules.TrySwap(board, state, a, b, out MoveReport report, out string why))
    Debug.Log($"cleared {report.Cleared} in {report.Steps.Count} waves");   // every wave, bonus and gimmick hit
else
    Debug.Log(why);                                                         // "the swap makes no match", ...

MatchOutlook o = MatchSolver.Outlook(puzzle, state, 1);                     // can it still be won?
if (o.Kind == OutlookKind.CanWin) Debug.Log($"{o.MovesNeeded} more, hint {o.Hint}");
```

A move can be traced (`MoveReport.Trace`): every step - the swap, each wave's clear, the falls, the new pieces, the belts -
with the pieces it moved and the position after it, for animating it exactly.

---

## The demo

One scene and one component, `MatchDemo`, which builds everything in code: no prefabs, models, textures or audio files. It
opens on a welcome screen, then four modes:

| Mode | What you play |
|---|---|
| **Clear** | Clear the whole board, on square or shaped boards. |
| **Bonus Pieces** | Make bonus pieces and set them off, to clear the board or collect. |
| **Gimmicks** | Layers, crates, fixed pieces, filters, moss, countdowns, exits or belts. |
| **Refill** | Classic match-3 with new pieces dropping in: collect, clear layers or bring exit pieces down. |

Each mode has **Endless** puzzles made to order (its kind and a difficulty of 1 to 5 stars), and a **Level Pack** that
climbs from 1 star to 5. The demo ships with 48 levels. Play has undo, restart, hints, a solver that plays the rest for
you, a warning when the level can't be won any more (it can be turned off), a camera you can turn, tilt and zoom, moves
against the limit, and sounds and effects made in code.

Controls: tap a piece then a neighbour, or drag it, to swap. U undo, R restart, H hint, F solve, W the dead-end warning on
or off, [ ] turn, Home / End tilt, wheel zoom, V reset the view, M mute, Esc for the menu.

---

## Layout

```
SimpleMatch/
├─ Runtime/
│  ├─ Api/               MatchGenerator, MatchPresets (and Auto), MatchAudit
│  ├─ Data/              MatchSettings, MatchData, MatchBoard, MatchState, MatchPattern
│  ├─ Generation/        the board builder (backwards from the goal), dealing, the gimmicks, refill levels
│  ├─ Solving/           the solver, MatchOutlook, random-player odds, bots, star rating, cancelling
│  ├─ Rules/             MatchRules: swaps, matches, bonuses, falls, refills, reshuffles, the move report
│  ├─ Presentation/      MatchExport (PNG and SVG), MatchNarration (a puzzle in words)
│  ├─ ScriptableObjects/ puzzle asset, pack, IPuzzleProvider
│  └─ Demo/              3D board, pieces, gimmicks, game, HUD, hub, Endless, sounds, effects, HDRP look
├─ Editor/               Match Generator window, analysis pane, inspectors, documentation menu, build look keeper
├─ Documentation/        offline HTML docs
├─ Generated/            the demo's four level packs
└─ Scenes/               the demo scene
```

Three assemblies: `SimpleMatch` (the core, pure C# with no package dependencies), `SimpleMatch.Demo` (Input System and
Unity UI, plus HDRP when it is installed) and `SimpleMatch.Editor`.

---

## Requirements

- Unity **6000.6** or newer.
- **URP** (recommended), **HDRP** and the **Built-in** pipeline. The demo uses the active pipeline's default lit shader and
  keeps it in builds, and on HDRP it adds a small volume so it looks the same.
- Demo only: the Input System package (set Active Input Handling to *Input System Package* or *Both*) and Unity UI.

## Limits, honestly

- Boards are 3 to 12 cells each way with 2 to 6 piece types. A puzzle-mode board is built from up to 15 lines, and its par
  comes out below the lines it is built from, so long puzzles need a big board and many lines.
- Refill levels take longer to prove: most presets build in under a few seconds, but a large or hard refill level can take
  10 to 30 seconds. Generation runs on a background thread with a time limit and Cancel.
- On refill levels "can this still be won?" may come back *unknown* when the search budget runs out (it is never wrong);
  raise the budget, or the demo looks once more, ten times as far.
- Collect goals on bonus boards are easy once bonus pieces fly: they rate up to about 4 stars.

## Support

**livingfailuregames@gmail.com**. Please include your Unity version and, for anything about a particular puzzle, its seed
and settings, since a seed rebuilds the puzzle exactly.

Licensed under the Unity Asset Store EULA. © Living Failure.
