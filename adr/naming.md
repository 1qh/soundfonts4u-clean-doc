# Naming

Rules for preset names and font file names; MuseScore shows the font file's base name as the section header and the preset name as the row.

## MUST

#### Preset names
- Name every preset uniquely across all output fonts, in CamelCase built from ASCII letters only, at most 20 characters. Why: the SF2 `achPresetName` field holds 20 bytes and the user picks by name.
- Build a name from the preset name minus every word its family's font header already says (Piano drops Grand and Piano; EPiano drops EP, Electric, Piano). Why: the header carries the family, so repeating it only lengthens the row.
- Drop every word that names a source file, a maker or a brand (Plus, Best, Nice, Ultimate, Yamaha, Steinway, Chateau, Salamander and the like), keeping names that are the sound itself (Rhodes, Wurli, Clav, Hammond, DX, Moog). Why: the user picks by sound, never by origin.
- Drop every 2 or 3 letter all-capital token and every single letter left in a name (OB, JP, KG, CC, DG, RF, AR, SG, CP, the C of C5), keeping only abbreviations that name the sound (EP, FM, DX, TR, CR, SD, SFX), and spell out `Brt` as Bright. Why: the user picks by sound, and source shorthands describe nothing.
- Append a descriptor WHEN a name is empty or 2 or more sounds share it, choosing for each sound the word from the fixed descriptor list that its CLAP audio embedding matches more than its siblings' do, each word used once per group, and a second descriptor the same way WHEN a clash remains. Why: the word describes the sound itself, and the choice is deterministic.
- Spell a digit-bearing model name by a letter form a reader recognises, such as `Dx` for DX7. Why: no digits.
- Keep every abbreviation and every hand-chosen name in 1 table in the repo that the build reads. Why: rebuilds on a new upstream rename the same sounds the same way.
- Refuse the build WHEN any name violates a rule or collides. Why: a guessed name ships as a real name.
- Spell out every source shorthand as its whole word from 1 table the build reads (`Cbs` contrabass, `Str` strings, `Trm` tremolo and the like), and drop source, model and brand marks (`GU`, `Rol`, the SC-55 map label, `Mod` for model). Why: a shorthand names the source's typing, never the sound.
- Choose a clash-breaking descriptor only from those that keep the name within 20 characters, and never one that is already a word of the name. Why: a descriptor that overflows or repeats a word produces a name the build must refuse.
- Refuse a name longer than 20 characters for the hand table rather than shortening it, and shorten only by dropping whole words that say nothing about the sound. Why: an abbreviation is exactly what the shorthand rule removes.

#### Font file names
- Name font files by the short family word: BrightGrand to DarkGrand (the tone ladder in `adr/families.md`), Upright, PianoLayer, EPiano, Organ, Harpsichord, Mallets, Nylon, Acoustic, EGuitar, Bass, Strings, Harp, Choir, Brass, Reed, Flute, Lead, Pad, Synth, Ethnic, Fx, Drums, Percussion. Why: shortest name that still reads as the instrument.
- Name font files as CamelCase family names without digits, dashes, spaces or symbols. Why: MuseScore reads the header from the file name.

## NEVER
- Never put a bank, program, file name, version number or digit in a preset name. Cost: noise the flat picker exists to remove.
- Never abbreviate a word to fit the length limit. Cost: it reintroduces the shorthand the spelled-out table removes.
- Never repeat a word inside a name. Cost: `DarkDark` reads as a defect and tells the player nothing.
