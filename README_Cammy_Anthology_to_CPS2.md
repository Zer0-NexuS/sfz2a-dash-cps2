# Cammy GOLD: From Alpha Anthology Back to CPS2

**SFZ2A-DASH-CPS2 Project · Technical overview of GOLD Test 15**

## What we built

We used **Street Fighter Alpha Anthology for PlayStation 2** as the source for the Gold Cammy upgrade: her character graphics, animation and combat records, executable behavior, portraits, names, voice-event mappings, and ending material. We then converted that material and translated the character routines into native Motorola 68000 code, integrating the result into the CPS2 version of **Street Fighter Zero 2 Alpha**, using the decrypted `sfz2ald` ROM set.

This is a character backport into the arcade engine. The finished ROM runs Cammy through the game's native character, collision, graphics and sound systems. Anthology provides the donor material; CPS2 provides the runtime.

The source distinction matters: Cammy's Gold content comes from Anthology, including its shared voice resources. The host arcade engine and existing roster come from SFZ2A. The project's retained title logo comes from the Saturn Gold resources, and its surrounding background comes from earlier project presentation work. Those title assets are separate from the Anthology Cammy upgrade.

## 1. Recovering Cammy's material from Anthology

The primary donor was **`Alpha_2_Gold_Extracted_Data.zip`**, containing the extracted Alpha 2 Gold resources from Anthology. We recovered both the visible character assets and the data and routines that determine how the character behaves.

| Donor material | What we recovered or used |
| --- | --- |
| Gold's `X_0526.dec` module | Character records, commands, animation/collision/attack tables, story information and executable behavior used for the native translation. |
| Gold's X0528/X0529/X0531 resources | Character and presentation graphics, including the actual Gold HUD portrait data. |
| Anthology's shared Cammy bank, `X_0333_MOMO` | Fourteen voice samples used with Gold's event and sequence mappings. This bank was recovered under `Alpha_3_Upper_Extracted_Data.zip/family_reference/`. |
| Gold's ending resources | Artwork, palette and all 17 narration lines, including the Psycho Limiter story. |

The shared voice bank's extraction location does not make this an Alpha 3 moveset conversion. The character's gameplay donor is Alpha 2 Gold, and Gold's records determine how the recovered voice samples are triggered. Multiple independent waveform matches supported the bank identification; its PS2 bank-loader binding was not independently emulated.

The integrated character data includes **1,103 fight/auxiliary animation records, 386 sprite compositions, 5,853 fight/auxiliary tiles and all 85 recovered Cammy attack records**, together with throw pairs, movement values and six costume palettes.

## 2. Separating character data from executable behavior

Extracting the graphics was only part of the work. A sprite gives the engine something to draw; it does not tell the engine how to recognize a command, advance a move, apply movement, create an attack or finish a throw.

We therefore treated the donor as two connected parts:

- **Character data:** animation records, sprite compositions, collision and attack records, movement values, palettes, portraits, text and sound-event mappings.
- **Character behavior:** the executable routines that select and advance those records, manage move states, and call the surrounding game's services.

Gold's animation and collision structures retain close relationships to the arcade formats. That allowed recovered records to be converted and relocated for the native engine while preserving their source content. The executable routines required an additional translation and integration step.

## 3. Translating the behavior into native 68000 code

Anthology's PS2 executable behavior was analyzed and lifted into a form that could be implemented as native CPS2 character handlers. The source routines and their referenced tables provided the basis for reconstructing Cammy's control flow and move behavior.

The translation connected three things: **what the Gold routine does, which character records it uses, and which native Alpha 2 engine operation should perform each shared task.**

We implemented that behavior in Motorola 68000 assembly and supplied bindings to the arcade engine's existing helpers. These bindings connect the translated character code to animation, movement, collision, combat and object processing. The native source also contains the associated projectile/effect and ending-controller work.

“Translated back to CPS2” describes this recovery of behavior and its implementation for the arcade CPU. It does not mean the PS2 binary was copied into the ROM or that a compiler automatically restored Capcom's original 68000 source. This was executable analysis, data conversion, native code translation and engine integration. The original commercial source code was not an input.

At runtime, the patched game executes the assembled 68000 implementation. It does not run the donor's MIPS routines or require a PS2 runtime inside the arcade emulator.

## 4. Reintegrating Cammy into the arcade engine

Once the data and native handlers were available, we connected them to the existing game. Gold's Cammy character ID **30** was mapped to the project's native custom slot **31**, with the corresponding character bindings and common character/campaign records adjusted for that mapping.

The integration includes standing, crouching and jumping normals; Cannon Drill, Thrust Kick, Spin Knuckle and Hooligan; Spin Drive Smasher and Psycho Streak; grounded throws, custom combo, reactions, intro and victory paths. Psycho Streak also includes its Bison effect.

The character-select route supports both players: highlight **M. Bison, press Start twice, then confirm Cammy**. After selection, the arcade engine uses her installed records and native routines through its normal gameplay systems.

Her Gold portraits, HUD graphics, names and result quote were also installed. The ending required its own native controller, connected to the existing ending dispatcher, along with 564 unique artwork tiles, the palette and the 17 scrolling narration lines. We repaired a source text-pointer alignment issue during that conversion.

## 5. Adapting graphics and sound to CPS2

The recovered indexed graphics were converted into the tile and palette arrangements used by the arcade build. Sprite compositions and references had to point to their new locations, and the added artwork had to coexist with the original stages and interface.

That placement work exposed the vertical-strip glitch fixed in Test 14: transparent tiles inside native stage artwork had been treated as available space even though background maps still referenced them. We separated the custom sprite and ending allocations and restored the stage bank. The relocation preserved the added graphics' pixel data.

For sound, Gold's event/sequence mappings were connected to native QSound playback. The recovered voices were converted to **12,019 Hz signed 8-bit PCM**. To fit the existing 4 MiB sample capacity, 156 existing long one-shot samples were resampled by 15/16 with pitch compensation; existing loops and the QSound driver code were preserved. PS2 sequencer envelopes and reverb are not reproduced by this adaptation.

The resulting build retains **3 MiB of program ROM, 20 MiB of graphics and 4 MiB of samples**, distributed across the existing 19-member ROM set.

## 6. Building and checking the result

The native assembly and converted data are assembled and inserted into the arcade ROM layout by the project's builder. The standalone source checkpoint preserves the donor resources, conversion caches, native sources, tools and fingerprints needed to repeat the build.

Historical validation includes 58 controlled gameplay cases across both players, 22 active voice-event checks, CPU-versus-CPU smoke testing, ending display and exit through a forced normal-dispatch entry, and the graphics repair comparison. Test 15 additionally verifies direct USA boot, removal of the Phoenix startup interface, normal service-menu access and both-player Cammy selection.

A clean rebuild reproduced all 19 released ROM members and the complete Test 15 ZIP byte for byte. This establishes a reproducible native integration. Full console parity, every gameplay branch, a complete natural campaign-to-ending run and physical CPS2 hardware operation remain outside the verified scope.

## Source and continuation reference

Use **`SFZ2A_Gold_Source_Checkpoint_15.zip`** to continue development. The active builder is:

`project_restore/SFZ2A_Cammy_Arcade_Checkpoint_09/arcade/tools/build_gold.py`

Native character and integration code lives in **`gold_native/`** and the arcade directory's **`source/`**. Recovery, conversion and verification tools live in **`analysis/`**. The checkpoint's `RECOVERY_INDEX_15.json`, `build15/manifest.json`, `VALIDATION_15.json`, `CLEAN_REBUILD_15.json` and `HISTORY_TEST_14.md` preserve the provenance, build identities and test scope.

This README documents the existing Test 15 implementation. It was prepared from the project's saved release documentation and does not represent a new ROM revision or a fresh code audit.
