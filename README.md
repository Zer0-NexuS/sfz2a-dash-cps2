[README.md](https://github.com/user-attachments/files/32588395/README.md)
# Street Fighter Alpha 2 DASH (GOLD) — CPS2 Arcade Reconstruction

**SFZ2A-DASH-CPS2 Project · GOLD Test 15 · 23 September 2026**

**THIS REVISION IS STILL IN BETA. CERTAIN ASPECTS OR MECHANICS TIED TO CAMMY'S CHARACTER HAVE YET TO BE FULLY IMPLEMENTED.**

This project brings Alpha 2 Gold's Cammy, character data, presentation and story into the native CPS2 version of **Street Fighter Zero 2 Alpha**, using the decrypted `sfz2ald` ROM set. The result runs through the arcade game's own 68000 code, graphics system, inputs and QSound hardware interface. The current release includes the repaired graphics allocation, direct USA boot and removal of the Phoenix startup screen and region selector.

The goal is a faithful arcade recreation of the Gold experience. Its strongest claim is that Cammy's implementation is grounded in recovered Gold data and executable behavior, with native arcade integration and repeatable validation. **It is not yet established as a perfect, complete port of every Gold rule, roster change or console feature.** The broader engine and roster remain based on SFZ2A except where the project explicitly changes them. “GOLD Edition” is this project's edition name, not an official Capcom arcade release.

## Quick start: upgrade a stock ROM ZIP

Use **`SFZ2A_GOLD_IPS_Upgrade_Test15.zip`**. Extract that package into an ordinary folder first. It contains patches and an upgrade helper; the stock ROM is a separate input.

### Recommended: automatic ZIP upgrade

The helper requires **Python 3.8 or newer**, with no additional Python packages. It checks the actual ROM files inside the ZIP, so equivalent stock archives with different compression, timestamps, ordering or enclosing folders are supported.

**Windows:** drag your stock `sfz2ald.zip` onto `Apply_GOLD.bat`. Alternatively, put the stock ZIP beside the helper and double-click the BAT file.

**macOS / Linux:** run this from the extracted patch folder:

```bash
python3 apply_gold.py "/path/to/stock/sfz2ald.zip"
```

The output is **`GOLD/sfz2ald.zip` beside your stock ZIP**. The original is preserved, and an existing output file is never overwritten. To choose a different output folder:

```bash
python3 apply_gold.py "/path/to/stock/sfz2ald.zip" --output "/path/to/output/sfz2ald.zip"
```

The helper verifies all 19 stock members, applies the appropriate IPS patches, then verifies all 19 result files against the released Test 15 build. It rejects different ROM revisions, incomplete sets, already-patched builds and damaged patches. Use a **complete decrypted `sfz2ald` set**, including its shared graphics/sound ROMs and `phoenix.key`; a small clone ZIP containing only program files is insufficient.

### Direct `.ips` application

**`SFZ2A_to_GOLD_Test15_ZIP.ips` patches the stock ZIP file itself.** Select the ZIP as the input in a standard IPS patcher, using its “all files” filter if needed. Do not unzip the ROMs for this particular patch, and do not apply it to an individual ROM chip. Apply it to a copy and retain the output filename `sfz2ald.zip`.

This direct patch requires the exact stock ZIP fingerprint below. IPS does not validate the input's identity by itself. If your stock ZIP has a different archive hash, use the included helper; it can accept the same underlying ROMs in a repacked ZIP.

| File | Bytes | SHA-256 |
| --- | ---: | --- |
| Exact stock `sfz2ald.zip` | 12,648,255 | `4f7568c92d95033f9da390821c0aa9eec4514dfacffc4f29f3a00add9eeb97b9` |
| Released GOLD Test 15 `sfz2ald.zip` | 14,066,413 | `ff000d013d8e12b1acce4d05d39e62301b4b65c71147ba75fd74f8d077dc937d` |

The `rom_patches/` folder also contains conventional IPS patches for the **16 changed individual ROM files**. Advanced users can apply each to its matching extracted stock member and rebuild the ZIP. `szaa.05`, `szaa.06` and `phoenix.key` are retained unchanged. Never apply both the direct ZIP patch and the member patches to the same build. Exact member sizes, CRCs, SHA-256 hashes and patch fingerprints are in `patch_manifest.json`.

With the helper's member route, a different system's ZIP compressor may produce a different archive hash; the 19 decompressed game ROM files must still match Test 15 exactly.

## Playing GOLD Test 15

Load the output as **Street Fighter Zero 2 Alpha (decrypted)** and keep its name **`sfz2ald.zip`**. Validation used the unmodified MAME 2010 libretro core. Modified ROM checksums differ from the emulator's stock database, so a frontend that enforces a strict stock audit may reject the custom set.

To select Cammy, **highlight M. Bison, press Start twice, then confirm Cammy**. Both players are supported; her six costume palettes are installed.

Start from a fresh boot after upgrading. Old emulator save states can restore the previous program and region. The game initializes **USA** on every boot, including when EEPROM previously held a Phoenix Japan or Europe selection. The normal service/test menu remains accessible. Fresh USA defaults use the game's native coin pricing, which required two coin pulses per player in the selection test; existing operator settings are preserved.

## What has been implemented

| Area | Current implementation |
| --- | --- |
| Native character integration | Gold Cammy's character ID 30 maps to native custom slot 31. Native 68000 handlers connect her to the arcade engine's animation, collision, movement, combat and object routines. |
| Character data | 1,103 fight/auxiliary animation records, 386 sprite compositions, 5,853 fight/auxiliary tiles, all 85 recovered Cammy attack records, throw pairs, movement values and six costumes. |
| Combat | Standing, crouching and jumping normals; Cannon Drill, Thrust Kick, Spin Knuckle and Hooligan; Spin Drive Smasher and Psycho Streak; grounded throws, custom combo, reactions, intro and victory paths. Psycho Streak includes its Bison effect. |
| Player and campaign data | Cammy's native slot bindings and common character/campaign normalization entries use the recovered Gold values with the required character-ID remap. |
| Portraits and labels | Gold's large portrait, actual Gold HUD portrait tiles, menu/battle names, VS glyphs and the result quote: “Fighting data retrieved successfully!” |
| Ending and story | Gold's ending artwork and palette, 564 unique artwork tiles, all 17 narration lines including the Psycho Limiter story, and a reconstructed controller connected to the native ending dispatcher. A PS2 text-pointer alignment error was repaired. |
| Voices | Gold event/sequence mappings drive 14 recovered shared-bank Cammy samples through native QSound. The earlier mismatched SFA3 audio route was replaced. |
| Title presentation | Extracted Saturn Gold title artwork is retained with the project's blue vertical Alpha background. Test 11's correction hides the old logo's leftover shine masks. |
| Graphics repair | Test 14 separates the custom sprite pool from the ending pool and preserves the original stage artwork bank, eliminating the reproduced vertical sprite strips. |
| Boot and region | Test 15 skips the Phoenix logo, delay and region mini-menu and initializes USA before region-dependent setup. The normal service menu and decrypted operation are retained. |

The build retains the baseline capacities: **3 MiB program, 20 MiB graphics and 4 MiB samples, across 19 ROM members**. The game runs native CPS2 code and data. The PS2 executable supplied behavior and data to analyze; its MIPS code is not executed by the patched arcade ROM.

## Where the source material came from

“Source” here means recovered game resources, executable analysis and the project's native assembly/conversion tools. It does not mean an original Capcom commercial source-code release.

| Source | How it is used |
| --- | --- |
| Stock decrypted `sfz2ald.zip` | Native SFZ2A arcade engine, base roster, stages, sound system, ROM layout and engine helper routines. This is the patch's baseline. |
| `Alpha_2_Gold_Extracted_Data.zip` | Main Cammy donor, extracted from PS2 Street Fighter Alpha Anthology's Alpha 2 Gold. `X_0526.dec` supplies character data, animation/collision/attack tables, commands, story information and executable behavior for translation. The X0528/X0529/X0531 resources supply recovered graphics and related data. |
| `Alpha_3_Upper_Extracted_Data.zip` → `family_reference/X_0333_MOMO` | Recovered Anthology shared Cammy voice bank. Multiple independent waveform matches identify the samples; Gold supplies the command/event/sequence mappings. The archive location does not make the current build an Alpha 3 Cammy moveset. |
| Saturn Gold `OPENING.BIN` / `OPENING.Z` | Extracted indexed title-logo pixels and palette/layout evidence, preserved through the earlier title work. The surrounding background is the project's retained presentation. |
| Arcade Checkpoint 09, Battle Test 11 and Gold Source 12A/12B / Integration 12C/12D | Recovered project code, native bindings, selection shortcut, presentation and conversion work. Integration 12D was first rebuilt byte-identically before later repairs and additions. |
| `Alpha_2_Gold_Primary_Audio.zip` and `Anthology_Shared_Data.zip` | Recovered reference material used during source/audio investigation. These are not a claim that every recovered resource is installed in the current ROM. |

Gold's animation and collision structures are closely related to the arcade formats. The work converts and relocates these records, preserves recovered indexed graphics, translates character behavior into native 68000 routines and binds it to the existing arcade engine. That is why this is substantially more than a sprite replacement: Cammy's combat records, presentation, voice events and story are integrated into the native game.

## Project history

| Checkpoint | Main result |
| --- | --- |
| Battle Test 11 | Earlier playable Cammy/title/selection build; corrected the inherited title shine overlay. |
| Gold 12A–12D | Decoded and converted Gold data; recovered/lifted character routines and native bindings; established the Gold integration baseline. |
| Battle Test 13 | Recovered a reproducible project, completed the shared-voice route, corrected Gold portrait/HUD insertion, connected the scrolling ending and exercised both players in controlled combat tests. |
| Battle Test 14 | Reproduced and fixed the regularly spaced vertical strips. Ending tiles had occupied transparent native stage tiles that were still referenced by background maps. Relocating the custom graphics restored those stage tiles. |
| Battle Test 15 | Removed the Phoenix startup UI and forced USA at boot. Only three program bytes changed from Test 14; all other 18 ROM members are identical. |

## What has been verified

| Evidence | What it establishes |
| --- | --- |
| Test 13: 58 controlled gameplay cases | Both players' six standing, six crouching and six jumping normals, four specials, two level-one supers, custom combo and four grounded throw inputs. Damaging cases hit, both players produced matching damage/meter results under the same setup, and the cases returned to neutral. |
| Test 13: 22 active voice-event cases | Eleven active animation voice events produced audio for each player. Separate hurt/KO/dizzy audition coverage is not established. |
| CPU-vs-CPU smoke test | Movement, attacks and reactions were exercised over 1,410 frames. This is limited AI coverage. |
| Ending test | A forced entry through the normal ending dispatcher displayed the artwork and all 17 scrolling narration lines and exited the controller. |
| Test 14 graphics comparison | The captured glitch contained 6,955 erroneous pixels. The repaired build matched the clean reference with zero differing pixels. All 6,170 added sprite/UI/title cells and 564 ending tiles preserved their pixels, and the native stage tail bank matched stock. |
| Test 14 runtime checks | Cold boot, both-player selection, ending display/exit and four targeted combat regressions passed. Gold gameplay data/code and sound ROMs matched Test 13. |
| Test 15 boot checks | Fresh settings, saved Japan and saved Europe all initialized USA. The native service menu opened and both players selected Cammy and entered battle. Graphics, audio and Cammy gameplay remained identical to Test 14. |
| Standalone source rebuild | A clean extraction of Source Checkpoint 15 rebuilt all 19 ROM members and the complete released ZIP byte for byte. |

The 58-case matrix uses controlled position, health and meter setup with emulated controller inputs. It was not a natural arcade campaign, and the full matrix was not repeated for the later graphics-only and three-byte boot changes.

## Fidelity and remaining work

The current build is a tested native CPS2 Gold reconstruction focused on Cammy and the documented presentation/story work. Calling it “perfect” would require further evidence: a complete natural campaign victory-to-ending run, every AI path, higher super levels and Hooligan follow-ups, all costumes and roster interactions, an audit of broader Gold engine/roster differences, and physical CPS2 hardware testing remain open.

Audio also has a deliberate hardware adaptation. The voices are converted to **12,019 Hz signed 8-bit PCM** for QSound; PS2 sequencer envelopes and reverb are not reproduced. To remain inside the original 4 MiB sample space, 156 existing long one-shot samples are resampled by 15/16 with pitch compensation. All 435 existing samples are retained; existing loops and the QSound driver code are preserved. The shared Cammy bank is supported by waveform evidence, but its PS2 bank-loader binding was not independently emulated. Native UI placement is retained, and the VS-name vertical position was chosen by visual correspondence rather than a completely traced source scene binding.

These are the specific boundaries of the current evidence. The project's strength is the recovered Gold material, native integration and preserved verification trail.

## Rebuilding and continuing development

Use **`SFZ2A_Gold_Source_Checkpoint_15.zip`** for development. It is separate from this upgrade package and contains the baseline ROM, donor data, converted caches, native source, assembler and build tools. Earlier checkpoint overlays are not required.

On Linux x86-64, install the Python dependencies recorded in `requirements-build.txt` and provide FFmpeg and a C compiler. From the extracted source root:

```bash
bash REBUILD.sh
```

The output is `build15_rebuilt/sfz2ald.zip`; the script checks all members and the ZIP against the released fingerprints.

| Path inside the source checkpoint | Purpose |
| --- | --- |
| `project_restore/SFZ2A_Cammy_Arcade_Checkpoint_09/arcade/tools/build_gold.py` | Active master builder. |
| The same arcade directory's `source/` and the root `gold_native/` | Native character, graphics/UI, audio and ending code. |
| `analysis/` | Conversion, recovery, graphics-layout and verification tools. |
| `analysis/patch_boot_usa.py` | Guarded Test 15 startup/region patch. |
| `build15/manifest.json` and `VALIDATION_15.json` | Current build identities, integration metadata and Test 15 checks. |
| `RECOVERY_INDEX_15.json` and `CLEAN_REBUILD_15.json` | Recovered archive fingerprints, emulator provenance and rebuild evidence. |
| `HISTORY_TEST_14.md` | Previous graphics correction and broader integration/test scope. |

Do not regenerate the current builder with `analysis/create_gold_builder.py`; that obsolete Test 12 generator is disabled because it would overwrite subsequent integration work. The current native sources and conversion caches are the build inputs.

The IPS package additionally includes `patch_manifest.json`, source provenance records and `PATCH_VERIFICATION.json`. The direct ZIP patch and all 16 member patches were independently applied with the unmodified Floating IPS IPS engine and matched the released outputs. The helper reproduced the complete Test 15 ZIP from both the exact stock archive and a repacked archive on the validation host. Wrong revisions, incomplete sets, already-GOLD inputs, in-place writes and existing output files were rejected. Python workflows were exercised on Linux; the Windows launcher was not separately run on Windows.

These checks establish the exact stock-to-Test-15 transformation separately from the game's historical runtime tests. This README/patch release packages the existing Test 15 ROM; it does not change gameplay or create a new game revision.
