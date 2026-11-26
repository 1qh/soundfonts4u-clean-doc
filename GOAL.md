# GOAL

What `soundfonts4u-clean` delivers and the bar each output is judged against.

## MUST
- Rebuild the upstream dataset `projectlosangeles/soundfonts4u` at a pinned commit into family fonts, deterministically. Why: an upstream update reruns to the same shape.
- Keep every distinct sound the upstream ships, with bit-identical sample data and identical effective parameters. Why: quality is the non-negotiable.
- Hold exactly 1 copy of each sound: bit-identical renders, and renders whose null-test residual is at most -60 dB, are one sound. Why: duplicates are the navigation pain.
- Drop a preset an older version of the same font family ships when the newest version ships a preset of the same name. Why: newest version wins.
- Write 1 font per sound family, or per topic where a family splits (Piano into tone, upright and layer fonts, Drums into kits and percussion), each with 1 bank, at most 128 presets, and within SF2's 16-bit indices. Why: SF2 caps a bank at 128 programs and a file at 65535 zones and generators; 1 bank lets a flat picker show 1 row per sound.
- Name every preset uniquely across the whole output: CamelCase, ASCII letters only, at most 20 characters, as short as uniqueness allows. Why: the user picks by name; SF2 preset names hold 20 bytes.
- Order presets alphabetically by name inside every font, and fonts alphabetically by file name, encoded in program and bank numbers. Why: the picker shows stored order, and a name is found fastest in alphabetical order.
- Publish to the Hugging Face dataset `1qh/soundfonts4u-clean` under CC-BY-NC-SA-4.0 with source credit, only after the owner reviews names and listening samples. Why: the license requires attribution and share-alike; publishing is outward-facing.
- Emit a manifest mapping every upstream preset to its output preset, or to the preset it merged into with the measured reason. Why: nothing disappears without a record.

## NEVER
- Never re-encode, resample, or compress sample data. Cost: the only lossless path MuseScore plays is plain 16-bit SF2.
- Never merge 2 sounds on name or parameter similarity alone. Cost: a rendered null test is the only proof 2 sounds sound the same.
