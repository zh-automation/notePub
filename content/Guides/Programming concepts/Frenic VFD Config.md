To set up the FRENIC-Eco inverter for your application, you must configure the motor-specific characteristics and then assign command priority to the RS-485 port.

### 1. Setting Motor Data

To ensure proper operation, motor ratings from the nameplate must be entered into the inverter's parameters. The most efficient method involves a combination of manual entry and parameter initialization.

- **Initial Motor Parameters:**
    - **P99 (Motor Selection):** Select the motor type (0 for Fuji standard 8-series; 1 for HP-rated/GE motors; 4 for other manufacturers).
    - **P02 (Rated Capacity):** Enter the motor's rated capacity in kW or HP as printed on the nameplate.
    - **F04 (Base Frequency):** Set to the rated frequency on the nameplate.
    - **F05 (Rated Voltage):** Set to the rated voltage on the nameplate (setting this to "0" allows the output voltage to follow the input source).
- **Initialization (H03):** After setting **P02** and **P99**, set **H03 to "2"** and press the **FUNC/DATA** key while holding the **STOP** key. This automatically initializes the motor's rated current (**P03**), no-load current (**P06**), and internal resistance/reactance data based on the capacity you entered.
- **Auto-Tuning (P04):** If using a non-Fuji motor or long cables, set **P04** to **1** (tuning at stop) or **2** (tuning while running) to allow the inverter to detect precise motor characteristics automatically.

### 2. Enabling Full Control via RS-485

To give the RS-485 port authority to issue both Run and Frequency commands, you must change the **Communications Link Function (H30)**.

- **Link Selection (H30):**
    - **H30 = 3:** Enables full control (Frequency and Run commands) via **Port 1** (the built-in RJ-45 port).
    - **H30 = 8:** Enables full control via **Port 2** (the optional RS-485 communications card).
- **Link Operation Command (LE):**
    - By default, if the "Enable communications link" command (**LE**) is not assigned to any digital input terminal, the RS-485 link is **always valid**.
    - If you have assigned **LE (data = 24)** to a terminal (such as **X1-X5** via function codes **E01-E05**), that specific terminal must be **ON** for the inverter to accept Modbus commands. When the terminal is **OFF**, the inverter reverts to local control.
- **Communication Settings (y Codes):** Ensure the following settings match your host equipment:
    - **y01 / y11:** Station address.
    - **y04 / y14:** Transmission speed (baud rate).
    - **y10 / y20:** Protocol selection (Set to **"0"** for Modbus RTU).

**Analogy:** Think of **H30** as the **"Command Center Switch."** Even if the motor data is perfectly set, the inverter will only listen to the keypad or physical wires until you flip the switch (H30) to "Remote." Once flipped, the inverter ignores local voices and follows the instructions coming through the digital "phone line" (RS-485).

### Identifying Registers
Registers are defined by the parameter group and the parameter number within that group. The register is built using the parameter group (e.g. y) in the most significant byte location (read left-most location in this application) and the parameter number within that group in the least significant byte location (read right-most location). 

For example, if you wanted to set the modbus slave address, the parameter group is y and the parameter number is 11. The y parameter group is 14 (x0E) and the parameter is still 11 (x0B). All registers are read with the 3 function code, read holding registers, so the full register is 41411.

The following table lists all parameter groups with their associated number and description:

| Group | Code (Hex) | Name                          | Group | Code (Hex) | Name                      |
| ----- | ---------- | ----------------------------- | ----- | ---------- | ------------------------- |
| F     | 0 (x00)    | Fundamental Function          | o     | 6 (x06)    | Operational Function      |
| E     | 1 (x01)    | Extension Terminal Function   | M     | 8 (x08)    | Monitor Data              |
| C     | 2 (x02)    | Control Function of Frequency | P     | 3 (x03)    | Motor1 Parameters         |
| J     | 13 (x0D)   | Application Function 1        | H     | 4 (x04)    | High Performance Function |
| d     | 19 (x13)   | Application Function 2        | A     | 5 (x05)    | Motor2 Parameters         |
| y     | 14 (x0E)   | Link (Field Bus) Function     | b     | 18 (x12)   | Motor 3 Parameters        |
| W     | 15 (x0F)   | Monitor 2                     | r     | 10 (x0A)   | Motor 4 Parameters        |
| X     | 16 (x10)   | Alarm 1                       | S     | 7 (x07)    | Command/Function Data     |
| Z     | 17 (x11)   | Alarm 2                       |       |            |                           |

### Common Registers
For control you use:

| Parameter | Register | Description                                                                                                                                                     |
| --------- | -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| S05       | 41797    | Reference frequency as a value between 0 and 65535 in 0.01 Hz (i.e. to send 23.52 Hz you would send 2352)                                                       |
| S06       | 41798    | Operation command bits:<br>0 - Forward<br>1 - Reverse<br>2 - X1<br>3 - X2<br>4 - X3<br>5 - X4<br>6 - X5<br>13 - XF (Forward)<br>14 - XR (Reverse)<br>15 - Reset |
For monitoring you would use:

| Parameter | Register | Description                                                                                                                                                                                                                                                                                                                                                                                                    |
| --------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| M09       | 42057    | Frequency output as a value between 0 and 65535 in 0.01 Hz (i.e. if you read 3775 that equals 37.75 Hz)                                                                                                                                                                                                                                                                                                        |
| M10       | 42058    | Power consumption of the motor as a % of nominal motor current - 0 to 39999 in 0.01% steps                                                                                                                                                                                                                                                                                                                     |
| M11       | 42059    | Output current as a percent of maximum inverter rated current 0 to 39999 in 0.01% steps                                                                                                                                                                                                                                                                                                                        |
| M12       | 42060    | Output voltage - 0 to 10000 in 0.1 V steps (only changes in 1 V increments, but reads with an extra 0                                                                                                                                                                                                                                                                                                          |
| M14       | 42062    | Inverter status bits:<br>0 - Rotating in forward<br>1 - Rotating in reverse<br>2 - DC braking<br>3 - Inverter shutdown<br>4 - Braking<br>5 - DC link circuit established (0 = under voltage)<br>6 - Torque limiting active<br>7 - Voltage limiting active<br>8 - Current limiting active<br>9 - Accelerating<br>10 - Decelerating<br>11 - Alarm present<br>12 - Comms good<br>15 - Function code being written |
