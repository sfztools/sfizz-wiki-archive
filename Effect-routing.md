## Analysis 1: RXP single

Synth: RXP

SFZ template
```
<region>
sample=sinewave.wav
oscillator=on
effect1=100

<effect>
bus=fx1
fx1tomain=100
type=fverb
reverb_type=hall
reverb_size=20
reverb_tone=90
reverb_input=100
reverb_dry=80
reverb_wet=50
```


| `effect1`  | `bus` | `fx1tomain` | `reverb_input` | *Result*        |
| ---------- | ----- | ----------- | -------------- | --------------- |
|            |       |             |                | Effect is heard (high volume) |
| Omit       | Omit  | Omit        |                | Effect is heard (moderate volume) |
| Omit       |       | Omit        |                | Effect is not heard |
| Omit       |       |             |                | Effect is not heard |
|            | Omit  |             |                | Effect is heard (high volume) |
|            |       | Omit        |                | Effect is not heard |
|            | Set to `fx2`  |             |                | Effect is not heard |
|            | Set to `fx2`  | Set opcode to `fx2tomain` |                | Effect is not heard |
| Set opcode to `effect2` | Set to `fx2`  | Set opcode to `fx2tomain` |                | Effect is heard (high volume) |
|            |       |             | Any value | Does not change anything |
|            |       | Set opcode to `fx1tomix` |                | Effect is heard (high volume) |

- With `reverb_dry` to **0**

| `effect1`  | `bus` | `fx1tomain` | `reverb_input` | *Other* | *Result*        |
| ---------- | ----- | ----------- | -------------- | ------- | --------------- |
|            |       |             |                |         | Hear a mix of reverb sound and dry sound |
| Omit       | Omit  | Omit        |                |         | Hear the reverb sound only |
|            |       |             |                | add `directtomain=0` in `<effect>` | Hear the reverb sound only |
