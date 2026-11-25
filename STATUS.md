# STATUS

Owed work for the soundfont rebuild, in order, each with why it is owed and how to check it; machine paths live in the workspace's operator-local file.

## Owed work, in order
1. Owner review. Why: names are the product. The review set sits in the code repo's review folder: a names table listing every sound per font in picker order with the second it starts, and 1 AAC sample per font playing every sound in that order. Rebuild it with `bun packages/soundfonts4u-clean/src/review.ts fonts review`. No Hugging Face push before the owner approves.
2. Audit `.cache/ambiguities.json`. Why: every multi-family keyword match is resolved by CLAP; review each decision whose family differs from what the name says.
3. CLAP neighbour check. Why: `adr/families.md` mandates flagging a sound whose 5 nearest CLAP neighbours mostly carry another family; it is not built.
4. Lint gate for the code repo. Why: lintmax fix was last clean before most of the pipeline code; run it and fix every finding.
5. Hugging Face publish. Why: the deliverable. Dataset `1qh/soundfonts4u-clean`, README stating CC-BY-NC-SA-4.0, source credit with the upstream commit, the `8Rock11e.sf2` exclusion and its reason, and how to rebuild.
6. Re-verify every number and capability claim in `adr/` against the code and the latest run. Why: the owner asked for a class sweep of stale claims.

## Decided, do not re-ask
- Font names: see `adr/naming.md`; Piano splits into BrightGrand, ClearGrand, MellowGrand, WarmGrand, DarkGrand, Upright and PianoLayer, see `adr/families.md`.
- Preset names carry no source, marketing or brand words; sound-model words Rhodes, Wurli, Clav, Hammond, DX, Moog stay unless the owner says otherwise.
- `8Rock11e.sf2` is excluded: see `adr/exclusions.md`.
- A recording group that cannot fit 1 font is split even though its samples are then stored twice.
- The output folder `fonts/` stays out of git through the repo's `.git/info/exclude`, because `.gitignore` is synced by pm4ai.

## Lessons
- Run the class sweep at fix time, never after being asked: grep the shape, fix every member, add a guard that fails on the old shape, and prove the guard fails by planting it.
- A guard's fixtures must match only through the rule under test, or the plant stays green.
- A test that loops over an empty list passes vacuously; assert the list is non-empty first.
- Read a decision doc against the code before calling it current; 3 mandates here had drifted from the code.
- Read a library's write path in raw bytes when its object model shows no difference: spessasynth's model matched the source exactly while its writer changed attenuation and sends.
- A probe that leaves a scratch folder resolves a bare package import to another copy, so a patch looks absent; run probes from inside the repo.
- Branch on a gate's own exit status; a gate piped through `tail` let a failing doc lint commit.
- Check that a large output folder is ignored before any `git add -A`.
