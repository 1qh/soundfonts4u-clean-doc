# Exclusions

Upstream files the build leaves out, each with the evidence and the trigger that would bring it back.

## MUST
- Exclude `8Rock11e.sf2`. Why: its `ICMT` says "Don't disassemble.", its samples are hybrid-lossy WavPack with no correction data, 12 of its samples reference the EMU8000 `1MGM` ROM no software ships, and FluidSynth refuses its `sfpk` RIFF type. Trigger to revisit: a lossless copy with RAM-only samples and a license that allows rebuilding.
- State every exclusion and its reason in the published README. Why: a user comparing against upstream finds the gap explained.
