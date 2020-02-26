### 0.3.0

- Added filter and EQ handling (the `filN_...` and `eqN_...` opcodes). There are also no limits to the amount of filters and EQs you can slap on each region beyond your CPU. Most if not all of the relevant filter types from the SFZ v2 spec are supported.
- Added a new command-line option for the JACK client to set the client's name (#75, #76).
- Added initial MIDNAM support (#79). The MIDNAM shows the named CCs in the SFZ file for now.
- Reworked the parsing code for faster dispatching and better handling of complex opcodes with multiple parameters in their opcode name (#40).
- Reworked the panning and stereo image process. The new process uses tabulated functions and avoid expensive calls to compute sine and cosine functions (#47, #56).
- Added a crude `*noise` generator. This generator is a bit expensive for what it does but it's mostly useful to test the filters.
- Added fine timings within the callbacks for performance improvements and regression testing (#65).
- Corrected a bug with Ardour where saving a session with no file loaded would crash on reopening.
- Corrected a bug where voices triggered on key off would never end and fill up the polyphony (#63)
- Improved and completed CI on all platforms.

### 0.2.0

- Added an LV2 plugin version.
- The parser now falls back to case-insensitive search if it doesn't find the sample file in its current path (#28), so that the behavior of SFZ libraries on case-sensitive filesystems will match Windows and macOS default case-insensitive filesystems.
- The file now reload automatically on file change, and you can force a reload if necessary (#17)
- Corrected a bug where memory would be read past the end of the file in memory, generating artifacts.
- Corrected a bug where the real-time queue handling background loading of the voices would fail spuriously.
- Corrected a bug where in the LV2 plugin the unknown opcode list was truncated (#18)
- Added dynamic updates for the current modifiers (panning, stereo image, volume and amplitude mainly) (#19, #28)
- Added timing for callbacks and file loading times.
- The JACK client will warn you instead of crashing if you do not give it a file to load (#27)
- Added a windows build process for both the shared library and the LV2. `sfizz` now builds on all major platforms.

### 0.1.0

- Initial release