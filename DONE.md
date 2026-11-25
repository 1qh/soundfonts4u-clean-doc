# DONE

The definition of done for soundfonts4u-clean: every requirement, the command that proves it, and the evidence that counts. The work is done only when every row is proven on the published commit; a row without fresh evidence is open.

## Output
| Requirement | Proof |
|---|---|
| Every distinct upstream sound ships, except the declared exclusion and drops | `manifest.json` accounts for every upstream preset as shipped, merged, dropped or excluded; a script counts upstream presets and asserts the 4 sets sum to that count with no key in 2 sets |
| Every output sound is bit-exact against its source | the pipeline's proof logs `N of N bit-exact` for every font and refuses any other null |
| 1 bank per font, at most 128 presets, within SF2 16-bit indices | the writer refuses an overflow; a script reads every written font and asserts 1 bank, at most 128 presets, every index under 65536 |
| Every preset name unique across all fonts, CamelCase letters only, at most 20 characters, no source, maker or brand word | the pipeline refuses on any violation; a script re-checks the written fonts' own preset names |
| Font file names are short CamelCase family words | a script asserts every file name matches `^[A-Z][A-Za-z]+\.sf2$` and appears in `adr/naming.md` |
| Similar sounds sit next to each other | the order is the CLAP clustering leaf order; reviewed by ear in the listening samples |
| Families are right | `.cache/ambiguities.json` audited, and the CLAP neighbour check flags no unreviewed sound |
| The build is deterministic | 2 builds from a clean output folder produce byte-identical fonts and manifest, compared by sha256 |
| MuseScore loads the fonts | official MuseScore 4 with every font in its SoundFonts folder logs `added sound font` for each of them and no `Failed parse`; playback is proven by the bit-exact FluidSynth render, the engine MuseScore plays SF2 through, because the MuseScore command line ignores user soundfonts: a score set to a missing font and one set to a real font export identical audio |

## Code and docs
| Requirement | Proof |
|---|---|
| Code passes the max-strict gate | `bun run fix` prints `ok` in the code repo |
| Tests pass and guard every class fixed | `bun test` in the package; each guard proven by a planted failure |
| Code repo published | public `1qh/soundfonts4u-clean`, CI green on the pushed head |
| Docs repo published and current | public `1qh/soundfonts4u-clean-doc`, doc gate `ok`, every claim in `adr/` re-checked against the code and the latest run |
| Dataset published | public Hugging Face dataset `1qh/soundfonts4u-clean` holding every font, `manifest.json` and the generated README; the remote file list and sha256 match the local build |

## Proven
Facts settled by a probe; read before any new spike.
- 64000 Hz renders make each 64-sample block 1 ms; with the pad silenced every segment nulls at -999 dB in any position (probe: same preset rendered alone, repeated 40 times, and after 39 others).
- spessasynth 4.4.12 changes attenuation and sends on load and write; the `bun patch` keeps them raw, and a source round trip then nulls at -999 dB.
- 1 `SoundBankCloner` session per bank keeps shared samples shared; `clonePreset` alone copies them per preset.
- FluidSynth renders a kit at bank N on a melodic channel with a bank select identically to the source kit on the drum channel.
- SF2 preset names hold 20 bytes; staged presets carry short positional names.
- The MuseScore 4 command line renders only its built-in MS Basic font: a score whose track names a missing soundfont and one naming a loaded font export the same RMS; the app itself loads user soundfonts from its SoundFonts folder.
