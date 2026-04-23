### Calculate density
- First fill vessel to a known point:
	- Note the locations of sight gauges, float switches, etc. anything that can independently verify the current height
- Solve for density: $$ \rho = P/h $$
	- Where $\rho$ is density, $P$ is pressure, and $h$ is height at a certain point. Gravity is already factored into the measurement of the pressure, so we can ignore it.
- Use density to find height to verify:
	- given $h=120 in$ , $P=4.33 PSIG$, and $\rho=0.0361 lb/in^3$:
$$ h_1 = 4.33/0.0361 = 119.9446$$
### Implement in code
- If you have a float, then this is simple:
1. Find height in vessel of non-shutdown float (looking for internal height, not external. These tanks are often lifted above the ground by some amount)
2. Fill/drain tank to that level and move that pressure into a separate variable. You now have height and pressure, so solve for density.
3. Use density as a multiplier to the pressure to determine height based on a scale. In structured text it may look like:
```iecst
(* float_trigger is the input from the float switch. If it is steady, a one-shot would be needed *)

IF float_trigger THEN
	pressure := PIT100.PV;                  // snapshot of pressure
	density := pressure / height_constant;  // calculate density
END_IF;

tank_ft := PIT100.PV / density;             // height is calculated
```

