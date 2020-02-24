## Curve numbers

When a `<curve>` element without an explicit `curve_index` is met, it takes the next available index starting from 1.

Most times, software will reserve 6 slots for predefined curves.
This will make the first user curve be 7, and it's a good idea to follow this as a convention.

Example
```
<curve> v000=0 v64=0.1 v127=1.0 // the curve at index 7
<curve> v000=0 v64=0.1 v127=0.2 // the curve at index 8
```

## Explicit curve index

ARIA opcode `curve_index` permits to specify the explicit index.

It can't seem to mix curves which are explicitly indexed and those which aren't.

Explicitly-indexed curves after implicitly-indexed ones seem to be discarded by the loader, and vice-versa.