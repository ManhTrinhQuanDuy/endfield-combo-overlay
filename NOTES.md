# Data & Schema Notes

Running list of things that are simplified, approximate, or unverified in
the current seed data — so future-you (or me) knows what to tighten up
when better sources turn up, instead of forgetting these were guesses.

## Unverified fields (explicit `null` or flagged in TAGS.UNVERIFIED_FIELDS)

- **Combo Skill cooldowns**: none of the 4 seed operators have a confirmed
  cooldown number in the sources reviewed so far. This means every
  duration-sensitive chain (anything involving Electrification's 5s window)
  currently shows as "TIMING UNVERIFIED" rather than "CONFIRMED" — which is
  the correct conservative behavior, but it also means we can't yet tell
  the user "yes, this chain reliably lands" for ANY electric-chain link.
  Cooldown numbers likely exist in in-game tooltips or datamined sources;
  worth checking endfield.wiki.gg's individual skill pages more closely or
  community spreadsheets (e.g. endfieldhub.org cites specific cooldowns
  like "Cooldown: 17s" for some kits).
- **Lift's duration** (`durationSec: 1`): approximated as "brief," not from
  a stated number. Lift is described as an instant launch effect in sources,
  not a long status, so 1s is a placeholder guess, not a sourced value.
- **Avywenna's produces list**: left empty. Her base Combo Skill (throwing
  lances) doesn't appear to reapply Electric Infliction per the sources
  reviewed — only the Ultimate-enhanced "Thunderlance EX" does, on return,
  via her Battle Skill. Modeling that properly needs a way to represent
  "this effect only happens after Ultimate is used," which the current
  schema can't express yet (see Schema gaps below).

## Schema gaps (known, deliberately deferred)

1. **No skill-type distinction.** Right now `produces`/`consumes` live on
   the operator's Combo Skill only. But in real kits, Battle Skill,
   Ultimate, and Combo Skill all have their own effects, and they interact
   (e.g. Endministrator's Combo Skill attaches Crystals, but it's the
   *Battle Skill* that consumes Vulnerable to detonate them). Currently
   collapsing all of this onto "the operator" works for our 4-operator demo
   but will misrepresent operators where Battle Skill and Combo Skill pull
   in different directions. Next iteration should probably split
   `comboSkill`, `battleSkill`, `ultimate` into separate sub-objects, each
   with their own produces/consumes/trigger.
2. **No "requires Ultimate to have been used" conditioning.** See Avywenna
   note above — some effects are gated behind a prior Ultimate cast, which
   isn't representable yet.
3. **AoE granularity is coarse.** `"single" | "small_aoe" | "aoe"` is a
   rough bucket; real radius/range differences between two "small_aoe"
   skills could still mean they don't actually overlap on the same pack of
   enemies. Treat the AoE caveat as "worth checking in-game," not gospel.
4. **No positional/timing-window mechanics beyond cooldown vs duration.**
   Real rotations care about sequencing (e.g. "use X's Ultimate right after
   Y's Combo, not before") which is more than a simple produce/consume
   graph can capture. The current matcher answers "can this chain happen
   at all," not "what's the optimal order." That's a reasonable v2 scope
   limit, not an oversight — revisit once the data layer is solid.

## Confidence levels — what each one actually means

- **CONFIRMED**: tag match works, AND either no duration/cooldown concern
  exists, or the known numbers are compatible.
- **TIMING UNVERIFIED** ("likely" internally): tag match works mechanically,
  but the produced effect has a duration and we don't have the consumer's
  cooldown to check whether it's reliably available in time.
- **CONDITIONAL**: tag match works, but with a caveat that changes how
  useful it is in practice — currently used for single-target-only
  producers (won't help against groups) and confirmed cooldown/duration
  mismatches.
- **BLOCKED**: the tag exists but the producer doesn't generate enough
  stacks to clear the consumer's threshold on its own. Not a dead end —
  it's a hint that a third operator or a repeated application could close
  the gap.

## Source reliability note

All current data comes from endfield.wiki.gg (community-maintained,
structured per-operator pages with explicit "COMBO TRIGGER" / "SKILL
DESCRIPTION" fields) cross-referenced against build-guide sites (Prydwen,
Mobalytics, endfieldhub.org, Game8) for produces/consumes/stack details
that the wiki's COMBO TRIGGER field doesn't spell out on its own. Since
Endfield is a live game, numbers can change with balance patches — worth
periodically re-checking seed operators against the wiki rather than
treating this file as permanently accurate.
