# Filters

The filter design of sfizz is made after the models listed in SFZ specification.
This design is compared against other established sampler software for verification.

Filter reference:
- https://sfzformat.com/opcodes/fil_type
- https://sfzformat.com/opcodes/fil2_type

## Comparison protocol

For comparison, one can implement a SFZ using a `*noise` generator input, and observe the spectrum of the output.

For an equivalence to this noise generator, one can compile a white noise generator of 1/4 amplitude.
```
import("stdfaust.lib");
process = no.noise : *(0.25);
```

SFZ Example
```
<region>
sample=*noise
lokey=0
hikey=127
cutoff=1000.0
fil_type=lpf_2p
fil_gain=0.0
resonance=10.0
```

## The biquad

The filters of 2, 4 or 6 poles kind are provided as biquad filters. A biquad is an IIR filter of the second order.

The transfer function of the generic biquad is:

`H(z) = (b0 + b1*z^-1 + b2 * z^-2) / (a0 + a1 * z^-1 + a2 * z^-2)`

From this formula, the response is computed by substituting `z` for `exp(2*pi*fc/fs)`.
The polar form of the result are the filter's amplitude and phase response.

When H(z) is substituted with `Y(z)/X(z)`, and then this identity applied to pass from z-domain to time domain:
`(A*z^-N)` → `(A*x[n-N])`

It's the implementable Direct Form I equation:

`y[n] = (1/a0) * (b0*x[n] + b1*x[n-1] + b2*x[n-2] - a1*y[n-1] - a2*y[n-2])`

Then, `a0` is factored into the equation to save an instruction in the computation:

`y[n] = (b0/a0)*x[n] + (b1/a0)*x[n-1] + (b2/a0)*x[n-2] - (a1/a0)*x[n-1] - (a2/a0)*x[n-2]`

It's the normalized form which is needed to use this filter in faust function `fi.iir`.

### The RBJ biquad

The most frequently used biquad kind is [RBJ](https://webaudio.github.io/Audio-EQ-Cookbook/audio-eq-cookbook.html), named after initials of the author.

It has a set of formulas for calculating coefficient for various kinds of responses.

We select this filter for our implementation, because it matches near perfectly in the comparisons.

It's available in faust in `maxmsp.lib`.
Only in late or developer versions, this library was relicensed permissively, so instead it's copied in our source tree with the appropriate license mention. (as `rbj.lib`)

## Implementation

The implementation of the filters is subject to following prerequisites:
- have controls which map to the documented opcodes
- have ability to modulate frequency and resonance and fast rate
- have stability, particularly in presence of important modulation

We have a source `sfz_filters.dsp` written in faust language, an entry point to the filters.

Faust is a specialized programming language which is able to generated optimized DSP code, in some aspects which are not sometimes possible in a non-specialized language.

For instance, Faust is aware of some notions of variability, and adapt code generation and optimization accordingly.
It can be defined like this:
- constant: a value which never varies
- a-rate: a value which updates at the audio rate, once per frame
- k-rate: a value which updates at the control rate, once per `process` call

When you have a filter which can modulate dynamically its response, there is potentially expensive computation involved.
For instance, formulas of RBJ biquads involve trigonometry.

To make modulation efficient as much as possible, this computation must be avoided at a-rate, but rather be made at k-rate.

For this reason, we define out filter controls as parameters, as in the sliders of a hypothetical UI, and not signals.
It's how to implement k-rate update. (even if no UI, faust calls it "sliders")
```
cutoff = hslider("[01] Cutoff [unit:Hz] [scale:log]", 440.0, 50.0, 10000.0, 1.0);
Q = vslider("[02] Resonance [unit:dB]", 0.0, 0.0, 40.0, 0.1) : ba.db2linear;
```

Our filter corresponding to each type has a naming convention starting with "sfz".
As an example, the 2-pole lowpass is the function `sfzLpf2p`. It is 1-in, 1-out.