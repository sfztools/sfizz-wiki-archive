## Automatic pannner

This is the automatic panner.

Not exactly like the reference, more info in comments. (the depth control)

```
/**
   Auto-panner

   Frequency: same as `apan_freq`
   Phase offset: same as `apan_phase`

   Other:
     - `apan_waveform`: LFO wave number (cf. `lfoN_wave`)
     - `apan_depth`:
          0 to disable, >0 to enable (reference)
          jpc: in this implementation, depth is allowed 0-100% granularity
               to make it more meaningful

   Note:
     Reference uses a linear pan function.
     Below, logarithmic is provided but it's commented.
 */

import("stdfaust.lib");

f = hslider("[1] Frequency [unit:Hz]", 5.0, 0.01, 5.0, 0.01);
o = hslider("[2] Phase offset [unit:deg]", 180.0, 0.0, 360.0, 1.0) : *(1.0/360.0);
d = hslider("[3] Depth [unit:%]", 50, 0, 100, 1) : *(0.01);

autopan = *(panL(lfo01)), *(panR(lfo01))
with {
  // any chosen waveform with output in domain [0:1]
  calcLfo = triangle;

  // output of the overall LFO, which modulates left and right exactly opposite
  // it's the difference of 2 sub-LFO with a phase offset between them
  lfoDD = d*(calcLfo(phase1)-calcLfo(phase2)); // outputs [-d:+d]
  lfo01 = 0.5*(lfoDD+1.0); // outputs [0:1]

  // running phase of 2 synced LFO with fixed offset
  phase1 = os.lf_sawpos(f);
  phase2 = wrap(phase1+o);

  // ordinary stereo pan function (+3dB)
  //panL(x) = cos(x*(ma.PI/2.0))*sqrt(2.0);
  //panR(x) = sin(x*(ma.PI/2.0))*sqrt(2.0);

  // linear stereo pan function
  panL(x) = 2.*(1.-x);
  panR(x) = 2.*(x);

  triangle(p) = 1.-abs(2.*p-1.);
  wrap(x) = x-int(x);
};

/* Test */
process = autopan;
// process = os.osci(110.0) : *(g) <: (_, _) : autopan with {
//   g = hslider("[4] Gain adjust", 0.4, 0.1, 1.0, 0.01);
// };
```

## Bit reduction

Matched `bitred` effect with 0-100 control range.
Implementation below is naive, it needs antialiasing.

**Note:** reference seems using a transition function to reduce aliasing between jumps from a quantization step to another.

PolyBLEP: http://www.martin-finke.de/blog/articles/audio-plugins-018-polyblep-oscillator/

```
import("stdfaust.lib");

amount = hslider("Amount", 90, 0, 100, 1);
oversampling = 1;//fconstant(int gOversampling, <math.h>);

bitred = *(steps) : roundToInt : /(steps) : hpf1p(20.0/oversampling) with {
  roundToInt(x) = ba.if(x<0, ma.neg(a), a) with { a = int(abs(x)); };
  steps = 5.+(1.-amount*0.01)*512;
};

hpf1p(f) = fi.iir((0.5*(1.+p),-0.5*(1+p)),(0.-p)) with {
  p = exp(-2.*ma.PI*f/ma.SR);
};

/* Test */
process = os.osci(110.0/oversampling) : *(0.15) : bitred <: (_, _);
```

Figure: reference effect 99% on 110Hz sine wave (green), implementation with hiir oversampling 2x (red).
The tiny peak between steps suggests a use of transition function (like polyBLEP)

![Bitred reference vs Oversampling 2x](https://user-images.githubusercontent.com/17614485/75026113-aea67f80-549c-11ea-9262-b9570ad45430.png)

## Decimation

Matched by exponential fit on the target sampling frequency, in 0-100 range.

Same remarks apply as for `bitred` regarding polyBLEP, it's the same.

```
import("stdfaust.lib");

amount = hslider("Amount", 99, 0, 100, 1);

decim(x) = y : hpf1p(20.0) letrec {
  'y = ba.if(p+(f/ma.SR)>1.0, x, y);
  'p = wrap(p+(f/ma.SR)); // the position 0-1
}
with {
  // exponential curve fit
  a=5.729950e+04; b=-6.776081e-02; c=180;
  f=a*exp(amount*b)+c;
};

hpf1p(f) = fi.iir((0.5*(1.+p),-0.5*(1+p)),(0.-p)) with {
  p = exp(-2.*ma.PI*f/ma.SR);
};

wrap(x) = x-int(x);

/* Test */
process = os.osci(110.0) : *(0.15) : decim <: (_, _);
```