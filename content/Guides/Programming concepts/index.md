---
title: Programming Concepts
draft: false
tags:
  - PLC
---

## What Happens in the Shadows
Sometimes you come across situations that just don't happen very often. It's hard to justify the research and development time to invest in instruments or programming concepts that aren't all that likely to come up again. Here are some of those I've come across:

#### [[Dynamic Density Level via Pressure]]
Setting up a bit of automation for a vessel whose level is measured with a pressure sensor and whose media tends to vary in density.

#### [[Frenic VFD Config|Fuji Frenic-Eco Inverter Config]]
Modbus RTU is a fairly simple, but somehow, the Fuji Frenic-Eco series still throws some wrenches into the process.

#### [[HSC for flow in micro850|High Speed Counters and Flow in the Micro850]]
The best solution for totalizing flow is a flow meter with a built in totalizer. A equally best solution is a flow computer like the ROC. Somewhere down the list is a cheap PLC with a high speed counter. Here's how that works.

#### [[How First Out Works|TAS Explains First Out]]
This was an explanation for a customer to help them understand how the first out logic was triggered in their alarm AOI. While the code is specific to that AOI, the overall concept can apply to many situations.