---
title: Registers and Tags
draft: true
tags:
  - PLCBasics
---

If you have any experience with PLCs, you'll know that they usually come in 2 flavors: tag based and register based addressing. Depending on which you choose to (or have to) work with, it determines how your program is documented and planned. 

### What is a register?
Typically, a register is just a small data storage location in a computer. This can be in the storage drive, RAM, or even in the CPU itself among other places. Most anything that stores values electronically will have those values organized into registers. 

#### On Chip
From x86 CPUs (like an Intel chip) to the AtMega328P on the Arduino R3 has registers for storing data. These processor registers are used by the CPU to read and write data to and from RAM, or to temporarily store data for arithmetic operations like addition and logical comparison. The size of an individual register is a common way to classify CPUs.

#### Playing with Power
For a CPU to do math, it uses registers and compares their bits. How many bits it can compare at a time is determined by the register size. For the original Nintendo Entertainment System, the Arduino UNO R3 and the popular Intel 8008 CPU that limit was 8-bits. 

