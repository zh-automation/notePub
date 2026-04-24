---
title: TAS First Out Explainer
draft: false
tags:
  - guides
---
All alarm AOIs have the same global AOI assigned to it. This AOI has a boolean tag called FirstOut. This is the reset logic for it:

```iecst
// First out reset
if FOReset then
	FOResTMR.TimerEnable := 1;
	FirstOut := 0;     // <-- This is our boy
else
	FOResTMR.TimerEnable := 0;
end_if;
TONR(FOResTMR);
if FOResTMR.DN then
	FOReset := 0;
end_if;
```

Because this AOI exists in every alarm AOI, all the tags in the globals datatype are available to all the alarms. As such, the mechanism to trigger the first out is like this:

- AOI triggers a shutdown (i.e. HHShutdown)
- Once the shutdown is confirmed, the AOI checks if Globals.FirstOut is true
- IF Globals.FirstOut is true, the local FirstOut bit is not set.
- IF Globals.FirstOut is NOT true, the local FirstOut bit is set AND the Globals.FirstOut bit is set

Here is an example of that logic from the AOI you are using:

```iecst
// ... other code up here ...

// Checks for a low low shutdown and the global first out bit
if LLShutdown and not Globals.FirstOut then

// Sets the first out bit locally and in the global datatype
	LLFirstOut := 1;
	Globals.FirstOut := 1;
	
// Reset local first out
elsif not LLShutdown and LLFirstOut and Globals.FOReset then
	LLFirstOut := 0;
end_if;	
```

One thing to keep in mind is the Auto Ack/Reset functionality of the alarms. If the LAAREn is true, then, after a short period if the conditions for the alarm are removed, the AAR will trigger a local acknowledge and reset for the alarms. This will NOT reset the FirstOut for the global datatype or locally. The only thing that clears the last FirstOut trigger is the Globals.FOReset tag that can be triggered on the HMI by pressing the reset button (looks like the recycle symbol). Here is the code for that:

```iecst
//Individual Ack+Reset
// ... other code ...


if LLAARTMR.DN then     //<-- This timer is triggered later
	LLAckLocal := 1;
	FaultAckLocal := 1;
	CommAckLocal := 1;
	LLResetLocal := 1;
end_if;

// ... most of the rest of the AOI code ...

//Auto Ack+Reset Logic
AAREn := HAAREn or LAAREn;
if LAAREn then
	if (LL or LLShutdown or CommFailAlm or Fault) and not LLActive then
		LLAARTMR.TimerEnable := 1;     //<-- Trigger to init AAR
	else
		LLAARTMR.TimerEnable := 0;
	end_if;
LAARTMR.PRE := LLDebounceOff * 1000;
TONR(LAARTMR);
LLAARTMR.PRE := LLDebounceOff * 1000; //<-- Time to wait after trigger
TONR(LLAARTMR);
```

This breaks down into these steps:

- If you have LAAREn set to true AND there is a LL or LLShutdown active but the LLActive is not true THEN the timer is enabled.
	- LLActive means that the conditions exist for a low low alarm, so here it is saying that LL and/or LLShutdown are latched to true but the conditions for it no longer exist
- Once the LLDebounceOff timer completes (e.g. the .DN becomes true) then the LL and LLShutdown are reset to false given the conditions exist for them to do so