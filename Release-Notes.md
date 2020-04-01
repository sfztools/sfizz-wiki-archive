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