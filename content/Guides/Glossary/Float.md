---
title: Float
draft: false
tags:
  - PLCBasics
  - Terminology
  - Real
---

A float is the numerical value of a 32-[[Bit|bit]] register interpreted as a floating point decimal value. A float has 3 parts, the sign (1 bit), the exponent (8 bits), then the mantissa which includes a leading hidden bit for indicating whether a number is a fraction of a number greater than/less than +/- 1 or a fraction of +/- 1 and the 23 bits for the data.

```python
# Here the 
var1 = 3.1415
```