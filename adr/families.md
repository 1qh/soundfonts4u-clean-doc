# Families

How each sound gets a family, how a family becomes fonts, and the order of sounds and fonts.

## MUST

#### Labelling
- Match family keywords against the preset name split into words, with CamelCase boundaries and punctuation turned into spaces and digits kept. Why: `DryKit`, `SuitcaseEP`, `TR-808`, `E.Piano` and `C5` otherwise miss a word-boundary keyword or a digit-bearing one.
- Label a sound in this order: the hand table, then Drums for any GM/GS drum kit or bank 127-128 preset, then the single family its keywords match, then General MIDI program WHEN the source is a GM-layout font. Why: a kit named `Orchestra` is percussion, not strings, and a GM font can park a non-GM sound on a GM slot, such as `12 String Guitar` on an electric-piano program.
- Resolve a name matching 2 or more families' keywords in this order: its General MIDI program WHEN the source is a GM-layout font and that family is among the matches; the instrument named before a `+` or `&` WHEN exactly 1 matched family names it; the non-Synth family WHEN the matches are Synth and exactly 1 other (`Synth Brass` is Brass); otherwise the closest CLAP centroid among the matches, each centroid averaging the sounds whose name matched exactly 1 family. Record every decision in the cache's `ambiguities.json`. Why: a layer belongs with its lead instrument, a synth version with its instrument, and keyword order picked by list position.
- Read articulation and context words as no family: `tremolo` labels nothing, `orchestra` followed by `hit` is a stab (Fx), `horn` after `english` is a reed, `drum` after `steel` is a pitched mallet, and `chime` after `wind` is percussion. Why: each of these pulled whole groups into the wrong font.
- Read the bare words guitar and gtr as EGuitar ONLY WHEN no specific guitar family (Acoustic, Nylon, Bass, EGuitar) already matches, and read `stl` as steel. Why: `Bass Guitar` is a bass and `Stl Gtr+Pad` is a steel-string acoustic, while the generic word made both electric.
- Fall back to the source file's own name WHEN no keyword, hand entry, drum flag or GM program labels a sound, and stop the build WHEN that is ambiguous too. Why: `Rhodes EPs` and `Chris Mandolin` name their contents.
- Match the abbreviations the Chateau piano layers use (`Dyn Ch`, `D Ch G`, `Studio Ch`) as piano. Why: those layers otherwise land in Strings and Pad.
- Stop the build listing every sound no rule labels, for the hand table. Why: a guessed family ships as a real one.
- Flag a sound for review WHEN 3 or more of its 5 nearest CLAP neighbours agree on another family (`neighbours.ts`, written to the cache's `neighbour-flags.json`), and review every flag before publishing. Why: the audio check caught the English horn, steel drums, wind chimes, steel-guitar and Chateau layers that keywords had misfiled.
- Move a sound to the family whose other sounds already hold most of its sample bytes ONLY WHEN its name marks a layer with `+`, `&` or the word layer, and the move saves at least half its audio. Why: a piano layer filed under Strings stores the piano twice, while a name that merely matches 2 families (`Synth Brass 2`, `Sine Perc.`) is a single sound and was dragged into Bass and EGuitar by shared bytes.

#### Splitting into fonts
- Split Piano by topic before any size split: layered pianos (piano with pad, strings, orchestra, bell or FM) form PianoLayer, uprights and character pianos (upright, honky-tonk, CP80 and every electric grand) form Upright. Why: each font then answers one question a player asks.
- Split Drums by topic: single percussion instruments (timpani, taiko, toms, cymbals, woodblock, castanets, tambourine, SFX and the like) form Percussion, full kits stay in Drums. Why: a player looks for a kit or for 1 instrument, never for whatever was left over from a size cut.
- Split every other piano by tone: rank each by how much closer its CLAP audio sits to the text `bright grand piano` than to `dark grand piano`, keep pianos that share an instrument together, order those groups by mean rank, and fill fonts in that order up to the SF2 budget, naming them from the ladder BrightGrand, ClearGrand, MellowGrand, WarmGrand, DarkGrand by how many fonts are needed. Why: the solo grands need over 160000 instrument generators and SF2 holds 65535 per file, while a tone order gives each font a sound a player can choose by.
- Hold every font within SF2's 16-bit indices: at most 128 presets and at most 64000 instrument generators and instrument zones, counted over its distinct instruments. Why: spessasynth's extended limits write a file FluidSynth and MuseScore cannot read.
- Split a font still over that budget into named sub-families along recording groups, never alphabetically. Why: a recording split across 2 fonts is stored twice.
- Cut a recording group larger than 128 at the point, in similarity order, where both sides fit and the fewest sample bytes are shared, and log those bytes. Why: such a group cannot fit 1 bank, and the cheapest cut keeps the duplicate smallest.

#### Ordering
- Order sounds inside a font alphabetically by final preset name, case-insensitive with the exact name as the tie-break, and number programs in that order. Why: a player finds a sound by name, and alphabetical order is the one a reader predicts.
- Order fonts alphabetically by file name and encode it in each font's single bank number. Why: the picker orders sections by bank, then program.
- Keep audio similarity only where it decides MEMBERSHIP: topic splits, the tone ladder, splits along recording groups and the cheapest cut of an oversized group. Why: similarity puts sounds in the right font, and the name orders them.
- Keep drum kits in the same ordering scheme as any family, at any bank. Why: MuseScore selects by explicit bank and program, and a kit at bank 0 renders bit-identical to bank 128 on any channel.

## NEVER
- Never assign a family from the audio alone when no keyword, program or hand entry applies. Cost: measured 83.4% leave-one-out accuracy, so 1 in 6 such sounds would land in the wrong family.

## Pitfall
- A keyword without a leading word boundary matches inside ordinary words: `s s` for S&S matched the `ss` in Glass, Pass, Brightness and Bass, and `pipe` matched Bagpipe; most mid-word matches are true compounds (Contrabass, Windchime, Polysynth), so anchor the short or ambiguous keyword rather than banning mid-word matches.
- A keyword guard needs fixtures that match ONLY through the rule under test: `SuitcaseEP` also matches through `suitcase`, so it kept passing with the split removed; the guard in `families.test.ts` uses `DreamyEP`, `BigRoom`, `ChamberStr`, `TwinLP`, `TR-909`, `DynC5`, `S&S Bright`, all of which fail with matching on the raw name.
- Splitting names for naming drops digits, which silently broke every digit-bearing keyword (`C5`, `S6`, `B3`, `DX7`, `808`) until keyword matching got its own digit-keeping form.
