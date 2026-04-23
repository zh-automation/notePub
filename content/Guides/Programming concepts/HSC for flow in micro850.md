Programming a Micro850 for flow totalization involves a few distinct steps: configuring the hardware, reading the high-speed counter (HSC) value, and then performing the math to convert those pulses into flow rate and total volume.
Here is a breakdown of what your program structure and logic should look like in Connected Components Workbench (CCW).
1. Hardware Configuration
Before writing code, you must enable the HSC in the Controller Properties.
 * Open your project and go to the Micro850 configuration tab.
 * Select Embedded I/O and find the HSC section.
 * Set HSC 0 (or your chosen input) to "Pulse/Direction" or "Up Counter" depending on your meter.
 * Note the input assigned (usually _IO_EM_DI_00).
2. The High-Speed Counter Function Block
In your Ladder Diagram (LD) or Structured Text (ST), you need to use the RA_HSC_Micro800 function block. This block interacts with the hardware counter to give you the "Accumulator" (the current pulse count).
Key Parameters:
 * HSC_External_Reset: Set to FALSE (unless you have a physical reset button).
 * HSC_App_Reset: Used to zero out the totalizer via software.
 * HSC_Accumulator: This outputs a DINT (32-bit integer) representing the total pulses received.
3. The Logic Structure
You will need two main calculations: Flow Rate (how fast it’s moving) and Totalization (how much has passed).
Step A: Totalization
To get the total volume, you divide the accumulated pulses by the K-Factor (the number of pulses the meter produces per unit of volume, e.g., 100 pulses/gallon).
> Note: Since HSC_Accumulator is a DINT, you should convert it to a REAL (floating point) before dividing to keep your decimals. Use the ANY_TO_REAL block.
> 
Step B: Flow Rate Calculation
Flow rate is the change in pulses over a specific window of time. The easiest way to do this in a Micro850 is to use a Periodic Timer (TON).
 * Every 1 second, capture the current pulse count.
 * Subtract the Previous Count from the Current Count to get "Pulses per Second."
 * Calculate the rate using this formula:
4. Example Code (Structured Text)
If you are using a Structured Text block, your logic would look like this:
(* 1. Call the HSC Function Block *)
HSC_Inst(Enable := TRUE, HSC_Number := 0, ...);

(* 2. Periodic Rate Calculation (Every 1 Second) *)
Timer1(IN := NOT Timer1.Q, PT := T#1s);

IF Timer1.Q THEN
    CurrentPulses := ANY_TO_REAL(HSC_Inst.Accumulator);
    
    // Calculate pulses since last second
    DeltaPulses := CurrentPulses - LastPulses;
    
    // Convert to Gallons Per Minute (GPM)
    FlowRate := (DeltaPulses / K_Factor) * 60.0;
    
    // Update last pulses
    LastPulses := CurrentPulses;
END_IF;

(* 3. Continuous Totalizer *)
TotalVolume := CurrentPulses / K_Factor;

5. Important Considerations
 * Rollover: The DINT accumulator will eventually hit its max value (2,147,483,647) and roll over to a negative number. You should write a small routine to detect if CurrentCount < LastCount and handle the math accordingly, or use the HSC_App_Reset to zero the counter and your "LastPulses" variable simultaneously.
 * Data Types: Always use REAL for flow rate and totalization to avoid rounding errors.
 * K-Factor: This is usually found on the flow meter's calibration certificate.
Would you like me to show you how to handle the "Rollover" math to ensure your totalizer doesn't break when the counter hits its maximum limit?
Handling the rollover is critical for flow totalization because once the High-Speed Counter (HSC) hits its maximum value (2,147,483,647), it will "roll over" to its minimum value (-2,147,483,648). If you are simply subtracting, your flow rate will suddenly spike or drop to a massive, incorrect number.
Here is how to handle the rollover and build a robust totalizer.
1. The Rollover Logic
The most reliable way to handle this is to calculate a Delta (the number of pulses that occurred since the last scan) and add that Delta to a "Master Total" variable.
When the current pulse count is less than the previous pulse count, a rollover has occurred. The math to find the true difference during a rollover is:

2. Structured Text Implementation
Using a LREAL (64-bit float) for your Master_Total is highly recommended. Standard REAL (32-bit) variables lose precision as they get larger—eventually, the number becomes so big that adding a small pulse count won't change the value at all.
(* Variables needed:
   Current_Accum (DINT), Last_Accum (DINT), Delta_Pulses (DINT)
   Master_Total_Pulses (LREAL), Total_Volume (LREAL)
*)

// 1. Calculate Delta Pulses since last scan
```iecst
IF HSC_Inst.Accumulator >= Last_Accum THEN
    // Normal operation
    Delta_Pulses := HSC_Inst.Accumulator - Last_Accum;
ELSE
    // Rollover occurred
    // 2147483647 is Max DINT, -2147483648 is Min DINT
    Delta_Pulses := (2147483647 - Last_Accum) + (HSC_Inst.Accumulator - (-2147483648)) + 1;
END_IF;

// 2. Add Delta to the Master Pulse Total
Master_Total_Pulses := Master_Total_Pulses + ANY_TO_LREAL(Delta_Pulses);

// 3. Update Last_Accum for the next scan
Last_Accum := HSC_Inst.Accumulator;

// 4. Calculate Final Volume
Total_Volume := Master_Total_Pulses / K_Factor;
```

3. Dealing with Precision (The "LREAL" Advantage)
In the Micro850, a REAL only has about 7 decimal digits of precision. If your totalizer reaches 1,000,000, and you try to add 0.001 gallons, the PLC will mathematically ignore it because the number is too small relative to the total.
 * Totalizer < 100,000: REAL is usually fine.
 * Totalizer > 1,000,000: Use LREAL (Long Real) to ensure you don't lose accuracy over months of operation.
4. Adding a "Reset" Function
You will eventually want to reset the totalizer (e.g., at the start of a new batch). Because we are using a Master_Total_Pulses variable, resetting is simple:
IF Reset_Button THEN
    Master_Total_Pulses := 0.0;
END_IF;

Summary Table: Logic Components
| Component | Function | Data Type |
|---|---|---|
| HSC Accumulator | Raw pulse count from hardware. | DINT |
| Delta Pulses | Pulses elapsed since last PLC scan. | DINT |
| Master Pulses | The "Big" count that ignores hardware rollovers. | LREAL |
| K-Factor | Pulse-to-Volume conversion ratio. | REAL |
Would you like me to help you create a User-Defined Function Block (UDFB) for this so you can easily reuse this logic for multiple flow meters?
