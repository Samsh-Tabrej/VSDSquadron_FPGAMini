# VSDSquadron FPGA Mini Research Project
The VSDSquadron FPGA Mini (FM) board is an affordable, compact tool for prototyping and embedded system development. With powerful ICE40UP5K FPGA, onboard programming, versatile GPIO access, SPI flash, and integrated power regulation, it enables efficient design, testing, and deployment, making it ideal for developers, hobbyists, and educators exploring FPGA applications.<br/><br/>
![](https://github.com/Samsh-Tabrej/VSDSquadron_FPGAMini/blob/main/Media/FPGA_label.png)<br/>

# Block Diagram
![](https://github.com/Samsh-Tabrej/VSDSquadron_FPGAMini/blob/main/Media/block_diag.png)<br/>

<br/>The VSDSquadron FPGA Mini (FM) board features the Lattice ICE40UP5K FPGA with the following capabilities:
- 48-lead QFN package
- 5.3K LUTs for flexible logic design
- 1Mb SPRAM and 120Kb DPRAM for efficient memory usage
- Onboard FTDI FT232H USB-to-SPI interface for programming and communication
- All 32 FPGA GPIO accessible for rapid prototyping
- Integrated 4MB SPI flash for configuration and data storage
- RGB LED for user-defined signaling
- Onboard 3.3V and 1.2V power regulators, with the ability to supply 3.3V externally

<br/>Further details about this FPGA can be known from its [datasheet](https://github.com/Samsh-Tabrej/VSDSquadron_FPGAMini/blob/main/Media/VSDSquadronFMDatasheet.pdf).

# TASK-1
## 1. Understanding the Verilog Code<br/>
The Verilog implementation is available in the GitHub repository at [LINK](https://github.com/thesourcerer8/VSDSquadron_FM/blob/main/led_blue/top.v). This repository includes various projects designed for the VSDSquadron_FM board, leveraging open-source FPGA tools for development. It showcases how to drive RGB LEDs using an internal oscillator and a counter-based approach to generate the required control signals.
### Purpose of the Module:<br/>
The ```top``` module is designed to control an RGB LED using an internal high-frequency oscillator (```SB_HFOSC```). It features:
  - A frequency counter driven by the internal oscillator.<br/>
  - A test signal output based on the frequency counter.<br/>
  - An RGB LED driver instantiated with configurable current parameters.<br/>

### Description of Internal Logic and Oscillator<br/>
#### Internal Oscillator
 - The module uses the ```SB_HFOSC``` primitive to generate an internal clock signal.
 - The parameter ```CLKHF_DIV = "0b10"``` determines the clock division factor.
 - The oscillator is always enabled using ```CLKHFPU``` and ```CLKHFEN```, both set to ```1'b1```.
#### Frequency Counter
  - A 28-bit counter (```frequency_counter_i```) increments at each rising edge of ```int_osc```.
  - The test signal output (```testwire```) is driven by bit 5 of the frequency counter.

### Functionality of the RGB LED Driver
#### RGB LED Driver
  - This primitive is used to control the RGB LED.
  - The driver enables the LED output via the ```RGBLEDEN``` input.
  - Each color channel (Red, Green, Blue) is controlled by the corresponding PWM input signals (RGB0PWM, RGB1PWM, RGB2PWM).
  - In this configuration:
      - Red (```RGB0PWM```) = 0 (off)
      - Green (```RGB1PWM```) = 0 (off)
      - Blue (```RGB2PWM```) = 1 (on)
   - The output is connected to hardware pins ```led_red```, ```led_green```, and ```led_blue```.
   - The current for each channel is set using defparam statements, limiting it to a small predefined value (0b000001).

### Summary
The module generates an internal clock using ```SB_HFOSC```.
A 28-bit counter increments on each clock cycle, with bit 5 controlling a test output.
The RGB LED is controlled via ```SB_RGBA_DRV```, with only the blue LED turned on in this implementation.

## 2. Creating the PCF File
### Pin Mapping and Functionality
To ensure proper functionality, the pin assignments specified in the [PCF](https://github.com/thesourcerer8/VSDSquadron_FM/blob/main/led_blue/VSDSquadronFM.pcf) (Physical Constraints File) must be verified against the VSDSquadron FPGA Mini board [datasheet](https://github.com/Samsh-Tabrej/VSDSquadron_FPGAMini/blob/main/Media/VSDSquadronFMDatasheet.pdf). Below is an analysis of the assigned pins and their roles in the design.<br/>
The following table summarizes the pin assignments:<br/>
Signal Name | Assigned Pin | Purpose
| :--- | ---: | :---:
```led_red``` |	39 | Controls the red LED
```led_blue``` | 40 |	Controls the blue LED
```led_green```	| 41 | Controls the green LED
```hw_clk``` | 20 | Input clock signal
```testwire``` | 17 | Debugging/General-purpose signal
<br/>
These pin mappings ensure the correct hardware connections for controlling the RGB LED and monitoring test signals on the FPGA board. By ensuring the correct mapping, the module can drive the RGB LED and monitor internal operations effectively.

## 3. Integrating with the VSDSquadron FPGA Mini Board
Install the required softwares and and setup the Linux environment as specified in the respective datasheet.<br/>
To navigate through project directories in a UNIX environment, use the following commands:
```
cd
cd VSDSquadron_FM
cd blink_led
```
To make sure that the board is connected to the Oracle Virtual Machine. Perform below steps:<br/>
  - Connect the board to your PC through USB-C.
  - On the Virtual Machine, click on "Devices → USB → FTDI Single RS232-HS [0900]".
  - To confirm if the board is connected to the USB, type the ```lsusb``` command in the terminal. It should show a line stating "Future Technology Devices International".
<br/><br/>
To program the VSDSquadron FPGA Mini (FM) board, follow these steps:<br/>
```make clean``` : To clean up previous builds.<br/>
```make build``` : To build the binaries for the FPGA board.<br/>
```sudo make flash``` : To flash the code to the external SRAM.<br/>

## Final Output
https://github.com/user-attachments/assets/7614b608-868a-4342-bf5e-127de2ef0eac

<br/> The verilog file and pcf file required to blink the RGB led are [RGB_blink.v](https://github.com/Samsh-Tabrej/VSDSquadron_FPGAMini/blob/main/LED_Blink/RGB_blink.v) & [VSD_FM.pcf](https://github.com/Samsh-Tabrej/VSDSquadron_FPGAMini/blob/main/LED_Blink/VSD_FM.pcf) respectively.<br/>
#### Summary of the above verilog file:
- The module uses an internal oscillator (```SB_HFOSC```) to generate a clock signal.
- A 28-bit counter runs on this clock to create a time-dependent pattern.
- The RGB LED driver (```SB_RGBA_DRV```) receives PWM signals derived from specific bits of the counter.
- The LEDs blink in a specific pattern, alternating between red, green, and blue.
<br/><br/>This is a simple FPGA-based blinking RGB LED circuit that can be used for debugging, status indication, or decorative lighting. <br/><br/>


# Task-2
## UART Transmitter Module (8N1)
This document explains the 8N1 UART Transmitter Module written in Verilog. The module is designed to transmit serial data in an 8 data bits, No parity, 1 stop bit (8N1) format. It follows a state machine approach to control data transmission over a UART-compatible serial interface. Explaination of the 8N1 UART Tx [verilog code](https://github.com/Samsh-Tabrej/VSDSquadron_FPGAMini/tree/main/uart_loopback/uart_trx.v) is done below:<br/>
### Module Breakdown
#### Inputs:
- ```clk```: System clock for synchronizing transmission.<br/>
- ```txbyte[7:0]```: The 8-bit data byte to be transmitted.<br/>
- ```senddata```: A control signal that initiates transmission.<br/>
#### Outputs:
- ```tx```: The serial output line (to be connected to a receiver).<br/>
- ```txdone```: A flag that indicates transmission completion.<br/>
#### Internal Registers:
- ```state```: Holds the current FSM state.<br/>
- ```buf_tx```: A shift register that holds the byte to be transmitted.<br/>
- ```bits_sent```: A counter to track the number of transmitted bits.<br/>
- ```txbit```: Holds the current bit to be transmitted.<br/>

### Finite State Machine (FSM)
The transmission process follows a 4-state FSM:<br/>
#### 1. STATE_IDLE (0)
- TX line remains HIGH (idle state).
- Waits for ```senddata``` signal to start transmission.
- If ```senddata = 1```, it loads txbyte into ```buf_tx``` and moves to ```STATE_STARTTX```.
#### 2. STATE_STARTTX (1)
- Transmits the start bit (0) to indicate the beginning of a byte transmission.
- Moves to ```STATE_TXING```.
#### 3. STATE_TXING (2)
- Sends the 8 data bits LSB-first using ```buf_tx```.
- Shifts the buffer after each clock cycle.
- Once all bits are transmitted, moves to ```STATE_TXDONE```.
#### 4. STATE_TXDONE (3)
- Transmits the stop bit (1) to indicate the end of the byte transmission.
- Sets ```txdone = 1``` to notify that transmission is complete.
- Returns to ```STATE_IDLE``` for the next transmission.

## UART Top-Level Module
This document explains the top-level UART module implemented in Verilog. It includes an external UART transmitter/receiver module (uart_trx.v), an internal oscillator, a frequency counter, and an RGB LED driver. The module is designed for serial communication and visual indication using an LED. Explaination of the 8N1 UART Top-Level [verilog code](https://github.com/Samsh-Tabrej/VSDSquadron_FPGAMini/blob/main/uart_loopback/top.v) is done below:<br/>
