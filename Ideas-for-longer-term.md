## Synthesis

- Oscillator mode with FM & AM

- Arpeggiator

- Random walk LFO
  - see https://www.muffwiggler.com/forum/viewtopic.php?t=10563

- LFO sync to host tempo

- Virtual analog filters with non-linearities

- 2D Wavetable

- Granular

- Physical modelling (waveguide opcodes)

- Scala tuning support (load Scala file, set root note of scale & mapped A4=Hertz value)
  - see http://www.huygens-fokker.org/scala/scl_format.html
  - default: (Equal Temperament, C, A4_MIDI_note_69=440hz) 

## Sampling

- Global memory storage, allow multiple Sfizz instances to use same file pool

## Effects

- Standard effect set

- Signal flow arrangements
  (cf. `dsp_order`)

- Effects per region

## Routing

- Flexible routing : allow regions to be routed to separate sends

## Expressivity

- Metalanguage to write SFZ more easily, with less repetition.
  Consider some ideas from CamelAudio Alchemy.
  - edit several properties of an item with indenting syntax
  - load data from CSV files

## Intruments

- Import other existing sample libraries

## MIDI Standards

- MPE
- MIDI 2.0