# Pipeline

Stages that turn the pinned upstream dataset into the published family fonts, and what each stage proves before the next one runs.

```mermaid
flowchart LR
    Pin[pin upstream commit] --> Verify[verify every file sha256 against its LFS oid]
    Verify --> Render[render every preset at 64000 Hz, cache 16-bit segments]
    Render --> Embed[render at 48000 Hz, embed with CLAP]
    Embed --> Version[drop presets an older version supersedes]
    Version --> Dedupe[merge same-recording presets at null at most -60 dB]
    Dedupe --> Drop[drop owner-dropped sounds]
    Drop --> Family[label families]
    Family --> Name[name every preset]
    Name --> Topic[split families by topic]
    Topic --> Split[split a font over the SF2 budget along recording groups]
    Split --> Order[order presets and fonts alphabetically]
    Order --> Stage[stage only fonts whose plan changed]
    Stage --> Write[write those fonts, delete stale ones]
    Write --> Prove[prove every changed font bit-exact against its source]
    Prove --> Manifest[emit manifest with resolved merge chains]
    Manifest --> Checks[verify.ts, neighbours.ts, readme.ts, musescore-proof.ts]
```

## MUST
- Read the upstream commit from one pinned value in the repo, and refuse to run when any local file's sha256 differs from that commit's LFS oid. Why: a rebuild must start from a proven-identical input.
- Render every preset through FluidSynth at 64000 Hz with reverb and chorus off, using the probe MIDI the repo generates. Why: 64000 Hz makes the 64-sample synth block exactly 1 ms, so every event lands on a block edge and segments are position-independent.
- Silence each segment with all-sound-off on the melodic and drum channels at the start of its final 240-tick pad, and reset controllers before the next program change. Why: a reset at the next segment's start let tails leak across, so one preset nulled at -78 dB against itself and a loud neighbour pushed a proof to -50 dB; with the pad silenced every segment nulls at -999 dB wherever it sits.
- Stamp every written font's creation-date field with the upstream commit's date. Why: the only byte that otherwise differs between 2 builds of the same font.
- Prove every output preset by rendering it from the written font and requiring a bit-exact match with its source render. Why: a writer that reports zero losses has not yet been observed to keep the audio; all 1350 sounds measure bit-exact, so any residual at all is a defect.
- Write the manifest from the same in-memory decisions the writer used. Why: a second derivation can drift from what was written.

## NEVER
- Never keep the pipeline cache inside the code repo at `.cache`. Cost: pm4ai's `clean.sh`, run by `pm4ai fix` and `up.sh`, deletes every `.cache` folder; it wiped 4 GB of renders once. The cache is a folder beside the repo, passed as the third argument.
- Never continue past a stage whose own check failed. Cost: a later stage builds on an unproven input and every report downstream lies.
- Never cache a render keyed on anything but the font file's sha256 plus the renderer key: a hash of the FluidSynth version and the probe MIDI for a fixed set of slots, so any change to either renders afresh. Cost: a stale render passes a dedupe it would now fail.

## Pitfall
- Prove each output sound the way it was rendered at the source: a drum kit plays drum notes, and on the MIDI drum channel only at bank 128; a kit stored at another bank plays on a melodic channel with an explicit bank select, which is how MuseScore addresses it.
- spessasynth `clonePreset` opens a new clone session per call, so `reuseChildren: 'identity'` only shares within that 1 preset: staging 84 presets copied 1763 samples to 11750 and a 757 MB source to about 5 GB, and a family font overflowed the 16-bit zone indices. Every bank built by cloning uses 1 `SoundBankCloner` session for all its presets, the library's own mechanism, and still runs the content-keyed sample dedupe for samples that arrive from different staged sources.
- SF2 preset names hold 20 bytes, so a staged preset carries a short positional name (`s0`, `s1`…) mapped back through the wanted list, never its source key.
- audio-decode maps 16-bit PCM asymmetrically: a negative code divides by 32768 and a positive one by 32767, so the exact inverse is `round(x * 32768)` below zero and `round(x * 32767)` at or above zero; a single symmetric scale turns full-scale positive samples into an out-of-range 32768.
- FluidSynth processes events on 64-sample blocks; at 44100 Hz a block is 1.4512 ms, so the same preset renders a different hash at a different position in one run.
- A batch render can still differ by a few samples for the same preset at different positions, so identity is decided by the null test, never by a hash of the segment.
- A cache written under a wrong conversion stays wrong after the code is fixed: the 64000 Hz segments stored under the symmetric 16-bit scale had to be deleted and re-rendered.
