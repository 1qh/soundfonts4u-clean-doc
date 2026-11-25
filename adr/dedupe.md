# Dedupe

What makes 2 presets the same sound, and which copy survives.

## MUST
- Treat 2 presets as 1 sound WHEN their segment renders are bit-identical. Why: identical PCM is identical sound by definition.
- Treat 2 presets as 1 sound WHEN they share every sample recording and their null-test residual is at most -60 dB relative to the louder render. Why: measured on all 1378 same-recording pairs, 0 pairs fall between -60 and -55 dB, so -60 is a natural gap.
- Null-test every pair that shares all recordings, never only the pairs a fingerprint ranks close. Why: a fingerprint distance of 0.06 nulled at -105 dB, so distance does not predict sameness.
- Keep the copy from the newest file of a version family; break remaining ties by source file name then bank then program. Why: newest version wins, and the order makes the choice deterministic.
- Drop a preset an older version file ships WHEN the newest version file ships a preset with the same name, case-insensitive. Why: newest version supersedes, even when the old one sounds different.
- Keep a preset whose name has no letter or digit WHEN its render differs from every named preset sharing its recordings. Why: the 21 dash-named SGM drum presets are real kits, 4 of them distinct.

## NEVER
- Never merge on a residual above -60 dB, however close it sits. Cost: the borderline kit at -57.1 dB is audibly a different kit.

## Pitfall
- Version families: 2Grands-Plus-Instruments-bs16i, Chateau Grand, ChateauGrand-Plus-Instruments-bs16i, Dore Mark's NY S&S Model B, Essential Keys-bs16i, Essential Keys-sforzando, Expressive Flute SSO, Nice-Bass-Plus-Drums. The Essential Keys -C, -SF, bs16i, sforzando and sfzBanks builds are separate player-app builds, never versions of each other.
