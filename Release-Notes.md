### 0.5.1

- Corrected race conditions that appeared with the new thread and file pools (#507 #508 #514 #521)
- Take the internal oversampling factor into account for loop points (#506)
- Fix an implementation error for the internal hash function when applied on a single byte (#512)
- Knobs are linear in the AU plugin (#517)
- Fix a crash in VSTGUI (#520)
- Fix the resource path in the LV2 plugin under windows (#524)
- Add MacOS make install rules (#525)

### 0.5.0

Big stuff:
- Added basic support for Flex EGs (#388) as modulation sources (targets to come)
- Added basic support for LFOs (#338)  as modulation sources (targets to come)
- EGs and LFOs can now target EQs and filters (#424)
- A new GUI has been added and is common to the LV2 and VST plugin (#397 #404 #419 #489 #492 #495 #496 #497); still mostly work in progress, more to come!
- Provided build systems to use sfizz with the VCV-Rack SDK and the DISTRHO Plugin Framework

New features:
- Added support for `sustain_lo` (#327)
- Audio files are now read incrementally, improving the availability under load (#294)
- A new output port for active voices has been added in the LV2 plugin (#321)
- Added support for effect types `reverb`, `disto`, `gate` and `comp`
- The voice stealing is now configurable using `hint_stealing`, with possible values as `first`, `oldest` (default), and `envelope_and_age`. The latter is the more CPU-consuming version which requires to follow the envelope of each voice to kill low-volume ones preferably. Note that the voice stealing continue to kill all voices started on the same event by default (i.e. all layers of the same note). (#344 #384 #353)
- `sfizz` now internally uses a modulation matrix to link all modulation sources (CC, LFOs, and EGs) and targets (#335 #351 #386)
- Added support for `off_time` and complete support for `off_mode`. The voice stealing logic was improved to take into account `polyphony`, `note_polyphony` and `group_polyphony` properly (#349 #352 #393 #413 #414 #467). Note that this support is also available for the engine polyphony. In this case, some additional voice will take over for the release duration (#477).
- The wavetable quality has been improved (#347)
- Support for `offset_cc` (#385)
- `sfizz_render` now supports a `--quality` switch, which acts like the `sample_quality=` opcode (#408)
- `pitch_keycenter=sample` is now taken into account (#362)
- Support `oscillator_detunecc` (#434)
- Support basic FM synthesis for oscillator regions (#436)
- CC 7, 10 and 11 are now linked by default to pan, volume and expression (#475)
- Support `hint_ram_loading` for loading the whole samples in RAM (#353)
- Support for `loop_crossfade` (#464)
- All phase-related opcodes in sfizz now use the 0->1 convention, as does ARIA/Sforzando, instead of the 0->360 convention (#499)

Issues:
- Loading probable wavetable files, or wav files containing wavetable metadata now sets `oscillator=on` on the region (#431)
- The default `sample_quality` was put back to 1 for live playing and 2 for freewheeling (#405)
- Fix an unwanted copy in the realtime thread (#334)
- Improve the filter shortcut path (#336)
- Fix the default `ampeg_attack` and `ampeg_release` to avoid clicks (#437)
- Corrected a race condition in freewheeling mode (#500)
- Fixed a potential non-realtime operation in the realtime thread (#498)
- Fix a bug when using a larger internal oversampling for regions with an `offset` value (#469)
- Fix an issue when loops occured more than once in a block (#462)
- Increase the range of the clamping on amplitude (#468) and pitch (#474)
- Fix CC modulations with their source depth set to 0 (#475)
- Fix an overshoot for crazily large cutoff values (#478); cutoffs are now clamped 
- Improve the file loading logic to keep files in memory for a short while in case they get reused (#425)
- Fix the MIDNAM output for the case where extended CCs are used (#420)
- Fixed a bug where release voices where not ignored on self-mask search (#348)
- Improved the release logic in many cases (#324 #414 #413)
- Set the level of the `*noise` generator to match ARIA's (#429)
- Support for `atom:Blank` atoms in the LV2 plugin (#363)
- Fixed `amp_veltrack` behavior (#371)
- Fix the ADSRH envelope release rate (#376)
- Fixed an error for files where the loop spans the entire file (#378)
- Fixed `sustain_cc` behavior (#377)
- Match the default volumes with ARIA (#381)
- Properly set the `loop_mode` for release regions (#379)
- Regions with `end=0` are now properly disabled (#380)
- Fix `fil_random` to be bipolar (#452)
- The `sequence` order now properly starts at 1 (#451)
- Fix an issue on Flush to Zero on some ARM platforms (#455)
- Fix `pitch_veltrack` (#461)
- Opcode values now properly stop at the `<` character (#439)
- Fix various build errors and issues on all platforms (#345 #401 #400 #399 #417 #447 #449 #443 #453 #456 #459 #471 #484 #487 #488 #491)
- The file dialog initial directory is now the root of the current loaded file (#428)
- Existing and known CC values are now correctly taken into account for modulations (#421)
- Fix various performance regressions and improved performance especially on ARM builds (#410 #412 #415 #426)

API changes:
- Added API support for setting the playback state, time position and signature (#354)
- The API documentation on the sfizz's website has been streamlined alot !

### 0.4.0

Big stuff:
- Added support for polynomial resamples and `sample_quality` opcodes (#238 #267). The engine now defaults to a value of `2` for this opcode, which is more intensive than the original linear interpolation resampler but provides a better quality. Added support for better resampling algorithms also in the wavetables via `oscillator_quality` (#287).
- Support `_curvecc` and `_stepcc` opcodes (#166 #155 #77) as well as `_smoothcc` opcodes (#181 #48 #22 #153 #297 #285)
- Added support and API for Scala tuning files in the engine and the plugins (#253 #268 #282)

Other new features:
- Added support for unison oscillators (#161)
- Support for the `polyphony` opcode at all levels (#171 #275), as well as `note_polyphony`. The `group=` polyphony is also more flexible and can be defined anywhere.
- Added support for `offset_cc` (#170 #159)
- Added support for `direction=reverse` (#185 #179)
- Added support to label the keys using a `label_key` opcode. This is not really standard yet, but it is now integrated in the LV2 plugin to advertise the names in the MIDNAM file and possibly change their labels in hosts that support it. (#174 #154)
- Added support for block comments `/* */` in the parser (#196 #195)
- Added a `sfizz_render` client in tree; you can build it with the make target `sfizz_render` if the `SFIZZ_RENDER` CMake variable is set to `ON`. (#200 #201 #206)
- Add support to integrate sfizz in DPF plugins (#216)
- Added an AudioUnit target (#224)
- Added support for the `set_hdcc` opcodes and overall added the ability to support floating-point CCs from the API (#233 #232 #244)
- Added support for FLAC loops (#242 #229)
- Support the `mapPath` feature of the LV2 specifications, for tentatively better portability in plugin states (#303)
- New instances of the sfizz LV2 plugin will now load a default `*sine` instrument (#283)

Issues:
- Solved some issues with DSmolken's drumkits related to the ampeg envelope (#172)
- An exception problem was thrown if an sfz file was deleted (#182 #184)
- Properly bundle the `dylib` for macOS (#188)
- Improved the filter stability (#198 #199 #210)
- Handle `USE_LIBCPP` properly on configure (#203)
- Fix the handling of loop markers if sample `end=` is present (#202 #204)
- Handle note on with 0 velocity as note offs in the jack client (#208 #211)
- Solved an issue with super short files (#215)
- Corrected a stack smashing bug in the LV2 plugin (#226)
- Fixed some parsing issues with `$variables` (#230)
- Properly advertise the VST plugin parameters (#241)
- Process `$` expansions in `#include` (#247)
- Change the default build type to `RelWithDebInfo` (#249)
- Improve the note stealing algorithm (#214); note that this is still very much a work in progress since many heuristics are in play here. Feel free to report misbehavior regarding note stealing as we improve this regularly.
- Corrected a bug with SFZ v1 `velcurve` (#263)
- Properly support the `off_by=-1` opcode to correctly reset the value. (#235)
- Corrected some errors with null-terminated atoms in the LV2 plugin (#269)
- Ignore garbage values following e.g. a key number in opcode values (as in `key=64Garbage` -> `key=64`) (#263)
- `ampeg_****_onccXX` modifiers now properly consider multiple CC modifiers (#300 #167) 
- Add headers and group sources in the CMake project for integration with e.g. Qt (#312)
- Trigger on CC does not require disabling the key triggering through e.g. `key=-1` (#315)
- Support flat notes parsed as string values (#291 #289)
- Improved handling of `release_key` (#298); still not perfect, if the region spans multiple key and multiple notes happened with the pedal down, only a single voice will start.
- Properly read the LV2 option list until the end (#323, by @atsushieno)
- Corrected a parsing issue when `$variables` were part of an opcode name (#328)
- Various other plumbing changes
 
API additions:
- Added API calls to set `$variable` define values prior to loading an SFZ file (#168 #119 #130)
- Added API calls to get key labels and cc labels defined by `label_key` and `label_cc` (#174)
- Added an API call to load an sfz file as an `std::string` or `const char*` (#217)
- Added API calls for Scala files and tunings (#253)
- Added high-definition floating point CC API calls (#244)
- Added API calls to change the default resampling quality (#267 #238)

### 0.3.2

- sfizz now builds down to gcc-4.9 with stricter C++11 compliance. The main release builds use C++17 mode on newer compilers (#111, #110)
- Upstream libraries updates (abseil, filesystem and atomic_queue) (#121)
- Added an experimental support for `make uninstall` (#118, #120)
- Add the autopan (#105), width, rectifier, gain, limiter (#131), and string resonator (#143) effects
- Curves are now registered within the synth but cannot be referenced yet (#96)
- Corrected a bug where the VST plugin got recreated needlessly in some hosts (#122)
- Added a "panic button" API that kills voices (#122)
- Corrected a potential overflow for CC names (930bfdf)
- Added support for more generators using wavetables (#61)
- Added support for the `oscillator` opcode, to create generators from files (#128)
- Generators using wavetables are now correctly tuned (#126)
- The stereo panning stage of the process was corrected; width is now set to 100% by default as it should, and panning is properly applied (1faa7f, b55171, #133)
- The logging API can be used to set a log filename (a6cbb48)
- Corrected errors in the performance report script related to display values (file names and histogram range)
- Reworked the parser; the new one is more efficient, and can indicate error/warning ranges (#130)
- The VST plugin now reloads the file automatically, like the LV2 plugin (#139)
- The max number of CCs was increased to 512, to accomodate some libraries that use cc300 modifiers.
- The engine uses floating point values internally for midi events (#137); this prepares it for high-resolution midi down the line.
- Fixes some realtime synchronization issues in the VST (#140)
- Added support for `note_polyphony`, `polyphony`, and `note_selfmask` (#142)
- Added support for `pitch_cc` and `tune_cc` modifiers (#142)
- The modifier support was overhauled; all regions can now have multiple CCs modifying the same target (#142).
- Corrected bugs and differences with Cakewalk/ARIA in the ADSR envelope (#136, #129)
- Improved performance of the amplitude stage gain of the rendering process (#145)
- The VST3 are now a submodule; more architecture targets have been added (#158, #147, patch proposed by @hexdump0815)

### 0.3.1

- Added a VST3 plug-in front-end to the library. It is still quite experimental and suffers from problems that stem from the VST3 SDK itself. (#99)
- Added effect buses and processing. There is a "lofi" effect available for now, as well as the same filters and EQs you can apply on the regions. More will come soon! (#84)
- Added a script to parse and render the timings. This can help tracking performance issues and regressions. (#89)
- Various fixups, performance improvements, and CI updates.

### 0.3.0

- Added filter and EQ handling (the `filN_...` and `eqN_...` opcodes). There are also no limits to the amount of filters and EQs you can slap on each region beyond your CPU. Most if not all of the relevant filter types from the SFZ v2 spec are supported.
- Added a new command-line option for the JACK client to set the client's name (#75, #76).
- Added initial MIDNAM support (#79). The MIDNAM shows the named CCs in the SFZ file for now.
- Reworked the parsing code for faster dispatching and better handling of complex opcodes with multiple parameters in their opcode name (#40).
- Reworked the panning and stereo image process. The new process uses tabulated functions and avoid expensive calls to compute sine and cosine functions (#47, #56).
- Added a crude `*noise` generator. This generator is a bit expensive for what it does but it's mostly useful to test the filters.
- Added fine timings within the callbacks for performance improvements and regression testing (#65).
- Corrected a bug with Ardour where saving a session with no file loaded would crash on reopening.
- Corrected a bug where voices triggered on key off would never end and fill up the polyphony (#63).
- Improved and completed CI on all platforms.

### 0.2.0

- Added an LV2 plugin version.
- The parser now falls back to case-insensitive search if it doesn't find the sample file in its current path (#28), so that the behavior of SFZ libraries on case-sensitive filesystems will match Windows and macOS default case-insensitive filesystems.
- The file now reload automatically on file change, and you can force a reload if necessary (#17).
- Corrected a bug where memory would be read past the end of the file in memory, generating artifacts.
- Corrected a bug where the real-time queue handling background loading of the voices would fail spuriously.
- Corrected a bug where in the LV2 plugin the unknown opcode list was truncated (#18).
- Added dynamic updates for the current modifiers (panning, stereo image, volume and amplitude mainly) (#19, #28)
- Added timing for callbacks and file loading times.
- Added support for pitch bends (#6) as well as pitch-bend activation for regions (`lobend` and `hibend` opcodes).
- The JACK client will warn you instead of crashing if you do not give it a file to load (#27).
- Added a windows build process for both the shared library and the LV2. `sfizz` now builds on all major platforms.

### 0.1.0

- Initial release