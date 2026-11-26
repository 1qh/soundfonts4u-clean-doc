# STATUS

Owed work for the soundfont rebuild, in order; `DONE.md` holds the definition of done and how each row is proven, and machine paths live in the workspace's operator-local file.

## Owed work, in order
None.

Every row of `DONE.md` holds on code commit `eb26b96`; re-run its proofs after any change: the cache folder's `twice.sh`, then `verify.ts`, `neighbours.ts`, `readme.ts`, `bun packages/soundfonts4u-clean/src/musescore-proof.ts fonts <work dir>`, then upload and compare the remote sha256.

## Published
- Dataset: https://huggingface.co/datasets/1qh/soundfonts4u-clean
- Code: https://github.com/1qh/soundfonts4u-clean
- Docs: https://github.com/1qh/soundfonts4u-clean-doc

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
