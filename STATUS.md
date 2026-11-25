# STATUS

Owed work for the soundfont rebuild, in order; `DONE.md` holds the definition of done and how each row is proven, and machine paths live in the workspace's operator-local file.

## Owed work, in order
1. Reproducibility on the final code. Why: the build must reproduce byte for byte. Check: the cache folder's `twice.sh` prints `identical` after 2 builds from a clean output folder.
2. Output verifier and neighbour check on that build. Why: the output rows of `DONE.md`. Check: `verify.ts` prints `ok` with every upstream preset accounted; every `neighbour-flags.json` entry reviewed.
3. Code repo CI green on the pushed head. Check: `gh run list -R 1qh/soundfonts4u-clean --limit 1`.
4. Dataset README, review set and Hugging Face upload. Why: the deliverable, which the owner approved to publish once checks pass. Check: the remote file list and sha256 match the local build.
5. Re-verify every number and capability claim in `adr/` and `DONE.md` against the final run. Why: no stale claim ships.

## Decided, do not re-ask
- Font names: see `adr/naming.md`; Piano splits into the tone ladder, Upright and PianoLayer, and Drums into kits and Percussion, see `adr/families.md`.
- Preset names carry no source, marketing, brand or source-abbreviation word; sound words such as Rhodes, Wurli, Clav, Hammond, DX, Moog, EP, FM stay.
- `8Rock11e.sf2` is excluded and the 4 guitar fret noises are dropped by the owner: see `adr/exclusions.md` and the manifest's `dropped` list.
- A recording group that cannot fit 1 font is split even though its samples are then stored twice.
- The dataset is public on Hugging Face and both repos are public on GitHub, published once every check passes.
- The output folder `fonts/` stays out of git through the code repo's `.git/info/exclude`, because `.gitignore` is synced by pm4ai.

## Lessons
- Run the class sweep at fix time, never after being asked: grep the shape, fix every member, add a guard that fails on the old shape, and prove the guard fails by planting it.
- A guard's fixtures must match only through the rule under test, or the plant stays green.
- A test that loops over an empty list passes vacuously; assert the list is non-empty first.
- Read a decision doc against the code before calling it current; 3 mandates here had drifted from the code.
- Read a library's write path in raw bytes when its object model shows no difference: spessasynth's model matched the source exactly while its writer changed attenuation and sends.
- A probe in a scratch folder resolves a bare package import to another copy, so a patch looks absent; run probes from inside the repo.
- Branch on a gate's own exit status; a gate piped through `tail` let a failing doc lint commit.
- Check that a large output folder is ignored before any `git add -A`.
- A tool that cleans a repo owns every folder its globs match; keep caches outside the repo.
