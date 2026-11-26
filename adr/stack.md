# Stack

Runtime, gate and every dependency, each with what it is for, why it won, and what lost.

## Adopted

| Need | Pick | Resolved version, maintenance | Why |
|---|---|---|---|
| Runtime + package manager | Bun, latest | 1.4.5; latest release inside the 6-month window | Only runtime the owner allows |
| Lint + format gate | lintmax, latest | 0.1.107; latest release inside the window | Owner's max-strict TypeScript gate |
| Read and write SF2 | spessasynth_core, latest | 4.4.12; latest release inside the window | Round trip renders byte-identical audio; `clonePreset`, `removeUnusedElements`, `getSF2Losses`, `flush(false)`, `writeMIDI` cover the whole build |
| Render audio | FluidSynth binary, invoked | 2.6.3; latest release inside the window | The engine MuseScore builds on, so renders match what plays |
| Decode WAV | audio-decode, latest | 3.12.0; latest release inside the window | Bun has no WAV decoder |
| Sound similarity | @huggingface/transformers, latest, model `Xenova/larger_clap_general` | 4.3.2; latest release inside the window | Family leave-one-out 5-NN accuracy 83.4%, against 82.0% `larger_clap_music_and_speech`, 81.9% `clap-htsat-unfused`, 63.5% mel statistics |
| Read and write MuseScore scores (zip) | fflate, latest | 0.8.3; latest release just OUTSIDE the window, see Exceptions | `Bun.Archive` reads tar only, and the MuseScore playback harness must rewrite a score's `audiosettings.json` |
| Ordering | ml-hclust, latest | 4.0.0; latest release OUTSIDE the window, see Exceptions | Only maintained hierarchical clustering in the TypeScript ecosystem |

## Exceptions
- fflate's latest release sits just outside the 6-month window. Evidence: the repo is not archived and is the standard pure-JavaScript zip implementation; it is used only by the MuseScore playback harness, never by the build. Trigger to revisit: Bun shipping zip support, an archive of the repo, or a maintained replacement.
- spessasynth_core carries a `bun patch` that keeps SF2 initial attenuation and reverb and chorus send amounts as the raw file integers. Evidence: the library stores attenuation as `round(raw × 0.4)` in a 16-bit integer and multiplies sends by 5 with a cap of 1000, so a plain load and write of an untouched SGM preset moves attenuation by 1 cB and clamps sends above 200 to 200, a null of -47.9 dB in FluidSynth; with the patch the same round trip nulls at -999 dB. The patch is safe here because spessasynth never synthesises in this pipeline. Trigger to revisit: an upstream option for raw SF2 values, or a release the patch fails to apply to, which the output proof then refuses.
- ml-hclust's latest release sits outside the 6-month window. Evidence: its repo carries recent dependency and docs commits and is not archived. Trigger to revisit: an archive, or a maintained TypeScript library offering optimal leaf ordering.

## Rejected
- sf2utils (Python): reader only, latest release outside the window, crashes on SF2Pack.
- libinstpatch: C library whose Python bindings need an off-by-default GObject-introspection build, not packaged for macOS.
- Polyphone: its command line converts formats and cannot merge presets from many fonts into new ones.
- Rust `soundfont`, `rustysynth`, `oxisynth`: readers and players, no writer.
- meyda, essentia.js, reorder.js, fft.js: latest releases outside the window.
- wavedraw mel features: CLAP measures 20 points more accurate on the same family test.
- Python with librosa and scipy for analysis: a second language beside the TypeScript writer.
