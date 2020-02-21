### 0.3.0

- Added filter and EQ handling (the `filN_...` and `eqN_...` opcodes). There are also no limits to the amount of filters and EQs you can slap on each region beyond your CPU. Most if not all of the relevant filter types from the SFZ v2 spec are supported.
- Reworked the parsing code for faster dispatching and better handling of complex opcodes with multiple parameters in their opcode name.
- Reworked the panning and stereo image process. The new process uses tabulated functions and avoid expensive calls to compute sine and cosine functions.
- Added a crude `*noise` generator. This generator is a bit expensive for what it does but it's mostly useful to test the filters.
- Corrected a bug with Ardour where saving a session with no file loaded would crash on reopening.
- Corrected a bug where voices triggered on key off would never end and fill up the polyphony (#63)
- Improved and completed CI on all platforms.

### 0.2.0

- Added LV2
- Added a windows build process for both the shared library and the LV2