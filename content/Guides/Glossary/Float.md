---
title: Float
draft: false
tags:
  - PLCBasics
  - Terminology
  - Real
---

A float is the numerical value of a 32-bit register interpreted as a floating point decimal value. A float has 3 parts, the sign (1 bit), the exponent (8 bits), then the mantissa which includes a leading hidden bit for indicating whether a number is a fraction of a number greater than/less than +/- 1 or a fraction of +/- 1 and the 23 bits for the data.

```python
# Try this out in python. It will show you how a float is constructed in python
import struct

def float_to_binary(f):
    # Pack float as 32-bit, unpack as unsigned int, then format as binary
    packed = struct.pack('>f', f)
    int_val = struct.unpack('>I', packed)[0]
    binary = format(int_val, '032b')
    
    # Split into sign, exponent, mantissa (IEEE 754)
    sign     = binary[0]
    exponent = binary[1:9]
    mantissa = binary[9:]
    
    print(f"Float: {f}")
    print(f"Binary: {binary}")
    print(f"  Sign:     {sign}")
    print(f"  Exponent: {exponent}")
    print(f"  Mantissa: {mantissa}")

float_to_binary(3.14)
```