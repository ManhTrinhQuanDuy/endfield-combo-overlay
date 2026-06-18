# EF // Chain — Endfield Combo Overlay

A squad-combo synergy tool for **Arknights: Endfield**. Pick your 4 operators, see exactly which Combo Skills chain into which, with honest confidence levels (CONFIRMED, TIMING UNVERIFIED, CONDITIONAL, BLOCKED) instead of one-size-fits-all "this works."

This is a proof-of-concept seed — the long-term plan is to wrap it in Electron as a transparent always-on-top overlay you can keep open next to the game. For now it's a single-file web prototype that runs in any browser with no build step.

## Run it

Open `index.html` in any modern browser. That's it. No npm install, no server, no dependencies — everything is inlined.

## What it does

- Pick up to 4 operators from the roster
- See every Combo Skill chain link between them, including:
  - **Trigger links** (A's combo enables B's combo to fire)
  - **Consume links** (A produces stacks that B can spend for bonus damage)
  - **Resource conflicts** (two operators competing for the same stacks)
- Each link is labeled with how confident the match is:
  - `CONFIRMED` — mechanically and (where applicable) numerically verified
  - `TIMING UNVERIFIED` — works mechanically, but cooldown vs duration data is incomplete
  - `CONDITIONAL` — works, with an AoE or timing caveat
  - `BLOCKED` — the tag matches but the stack threshold isn't met by this pair alone

## Structure

```
.
├── index.html              # Self-contained prototype (inlined operators + matcher)
├── src/
│   ├── data/operators.js   # Operator definitions with structured combo trigger/effect tags
│   └── lib/comboMatcher.js # Chain-matching engine (stack/duration/AoE-aware)
└── NOTES.md                # Honest accounting of what's unverified or simplified
```

The `src/` files are how this would be structured if/when it moves into a real React/Vite project. Right now they're identical in logic to what's inlined in `index.html`.

## Current seed roster

4 operators, picked because they exercise two distinct synergy lanes:

- **Perlica** (Electric) — applies Electrification on Final Strike
- **Avywenna** (Electric) — needs an Electric-affected target to fire her combo
- **Chen Qianyu** (Physical) — stacks Vulnerable via Lift
- **Endministrator** (Physical) — universal trigger, consumes Vulnerable for big damage

Together they show: an elemental producer→consumer chain (Perlica→Avywenna), a physical producer→consumer chain (Chen→Endministrator), a universal trigger (Endministrator off any ally combo), and a consume relationship.

## Data sourcing

Combat trigger conditions and skill effects come from [endfield.wiki.gg](https://endfield.wiki.gg/wiki/Operator) — the community-maintained Endfield wiki — cross-referenced against build guides for stack counts and AoE details. See `NOTES.md` for what's still a guess vs sourced.

## Roadmap

The current single-file prototype is intentionally Stage 1 + Stage 2:

1. ✅ Data layer + matching logic (this)
2. ✅ Working UI as a normal web page (this)
3. ⬜ Migrate to a real React/Vite project
4. ⬜ Wrap in Electron as a transparent always-on-top overlay
5. ⬜ Expand from 4 seed operators to the full roster (~23-27)
6. ⬜ Split operator effects into Combo Skill / Battle Skill / Ultimate sub-objects (see NOTES.md — current schema collapses these)

Screen-reading / OCR detection of the in-game squad is **explicitly not** planned — manual squad input is the deliberate design choice that keeps this project finishable.

## License

MIT (or your choice — pick a license when you set the repo public).
