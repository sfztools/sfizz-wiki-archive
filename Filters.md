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

`H(z)=(b0 + b1*z^-1 + b2 * z^-2) / (a0 + a1 * z^-1 + a2 * z^-2)`

From this formula, the response is computed by substituting `z` for `exp(2*pi*fc/fs)`.
The polar form of the result are the filter's amplitude and phase response.

When H(z) is substituted with `Y(z)/X(z)`, and then this identity applied to pass from z-domain to time domain:
`(A*z^-N)` → `(A*x[n-N])`

It's the implementable Direct Form I equation:

`y[n] = (1/a0) * (b0*x[n] + b1*x[n-1] + b2*x[n-2] - a1*x[n-1] - a2*x[n-2])`

Then, `a0` is factored into the equation to save an instruction in the computation:

`y[n] = (b0/a0)*x[n] + (b1/a0)*x[n-1] + (b2/a0)*x[n-2] - (a1/a0)*x[n-1] - (a2/a0)*x[n-2]`

It's the normalized form which is needed to use this filter in faust function `fi.iir`.

### The RBJ biquad

The most frequently used biquad kind is [RBJ](https://webaudio.github.io/Audio-EQ-Cookbook/audio-eq-cookbook.html), named after initials of the author.

It has a set of formulas for calculating coefficient for various kinds of responses.

We select this filter for our implementation, because it matches near perfectly in the comparisons.
