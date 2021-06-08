# Hardware

[![Diagram of computer](resources/8-bit-computer-fritzing-600w.jpg)](resources/8-bit-computer-fritzing.png)

The diagram above has all the components, but most of the wiring is not displayed.


## Summary

* 8-bit wide bus
* 16 bytes of memory (4-bit addressing)
* 2 general purpose registers
* Arithmetic logic unit with support for addition and subtraction
* 7-segment output display
* Programmable using DIP-switches
* Based on the SAP-1 architecture with some additions from SAP-2
* Instruction set is a subset of Intel 8080
* Support for single stepping the clock
* Instruction decoder based on microcode
* Zero and carry flags


## Integrated circuits

|Name|Type|
|---|---|
|555|Timer|
|74LS00|Quad NAND gate|
|74LS02|Quad NOR gate|
|74LS04|Hex Inverter|
|74LS08|Quad AND gate|
|74LS32|Quad OR gate|
|74LS86|Quad XOR gate|
|74LS107|Dual JK flip-flop|
|74LS138|3:8 line demultiplexer|
|74LS139|Dual 2:4 line demultiplexer|
|74LS157|Quad 2:1 line multiplexer|
|74LS161|4-bit binary counter|
|74LS173|4-bit D register|
|74189|64-bit (16 x 4 bits) Static RAM|
|74LS245|8-bit 3-state buffer / bus transceiver|
|74LS273|8-bit D register|
|74LS283|4-bit binary adder with carry|
|AT28C16|16Kb (2K x 8 bits) EEPROM|


## Clock

The clock is a square wave at 50% duty cycle and is used for triggering and synchronizing operations in the computer. Supports variable speed from 1.5Hz to 1kHz and also single stepping.

* Chips
  * 3x 555
    * The first is in astable mode. It generates a 50% duty cycle square wave for a continuous clock timer.
    * The second is in monostable mode. It's used as a debounce circuit for the single step push button, to generate a manual pulse.
    * The third is in bistable mode. It's used as a debounce circuit for mode toggle switch.
  * 74LS04 inverter: these 3 last chips are for creating the logic circuit that allows outputting the clock signal from the selected mode, as well as disabling the clock output when halted.
  * 74LS08 AND gate
  * 74LS32 OR gate
* Inputs
  * Potentiometer: for controlling the speed of the clock.
  * Push button: for single stepping the clock.
  * Toggle switch: for switching between continuous mode or single step mode.
* Outputs
  * The normal clock signal.
  * The inverted clock signal.
* LEDs
  * 3x Yellow
    * The first shows the square wave from the first 555 timer.
    * The second shows the debounced output of the second 555 timer.
    * The third shows the mode the clock is in. The LED is off in single step mode and on in continuous mode.
  * Blue: this LED shows the normal outgoing clock signal, either in sync with the clock timer or the single steps. 
* Control signals
  * HLT: halts the clock.


## Memory Address Register (MAR)

A 4-bit (0->15) register that keeps track of the active memory address for the RAM. 

Can be put in programming mode together with the RAM for manual control with DIP-switches.

* Chips
  * 74LS173 register: for storing the current 4-bit address in run mode.
  * 74LS157 multiplexer: for selecting between the address from the MAR or from the DIP-switch.
* Inputs
  * 4-bit DIP switch: for setting the address in programming mode.
  * Toggle switch: for switching between run mode and programming mode. Also applies to the RAM.
* Outputs
  * The 4-bit address from the selected run mode goes to the RAM.
* LEDs
  * Green/Red mode: for showing the mode. Green for run mode and red for programming mode.
  * 4x Yellow address LEDs: shows the current 4-bit address in the selected mode.
* Control signals
  * MI: store a 4-bit address from the bus in the MAR.


## Random Access Memory (RAM)

16 bytes of static RAM.

Any of the 16 bytes can be read and written to, but it behaves like a regular 8-bit register in that it only works with 1 byte at a time. The MAR must be used for selecting which byte to read from or write to.

Can be put in programming mode together with the MAR for manual control with DIP-switches.

* Chips
  * 2x 74189 RAM: for storing for 16 bytes of data or instructions. The outputs are inverted.
  * 2x 74LS04 inverter: to invert back the data in the RAM.
  * 74LS245 buffer: to control when it's outputting to the bus.
  * 3x 74LS159 multiplexer
    * The first is used for controlling whether the program button or the RI control signal can write to the RAM depending on mode.
    * The two others are used for selecting between the 8-bits of data coming from the bus or from the DIP-switch depending on mode.
  * 74LS00 NAND gate: enables the RI control signal only on the rising edge of the clock. 
* Inputs
  * 4-bit memory address from the MAR.
  * Push button: for setting the value from the DIP switch into the current memory address of the RAM in programming mode.
  * 8-bit DIP switch: for setting the value at the current address in programming mode.
  * Toggle switch: for switching between run mode and programming mode. From the MAR. Goes into all the multiplexers.
* LEDs
  * 8 Red LEDs: for showing the 8-bit value at the current address in RAM.
* Control signals
  * RI: store an 8-bit value from the bus in the current memory address.
  * RO: put an 8-bit value from the current memory address to the bus.


## Reset

Reset button that connects to all the different parts of the computer to clear the state. This is required before starting a program since the computer comes up in a random state, and when restarting a program.

* Chips
  * 74LS00 NAND gate: for creating the necessary signals that will reset the other chips.
* Inputs
  * Push button: for initiating a reset.


## Instruction Register

8-bit register that contains the currently executing instruction.

Instructions are made up of an opcode and an operand, both 4 bits each. The opcode tells which instruction to execute while the operand contains extra data related to the instruction, like a memory address to jump to, or a small value to put into another register. See [instruction_set.md](instruction_set.md) for more about instructions.

The instruction register is very similar to the general purpose A and B registers, except how it's used. The 4 bits for the opcode goes to the instruction decoder, and only the 4 bits of the operand can be put out onto the bus.

* Chips
  * 2x 74LS173 register: these are used for storing the 8-bit instruction.
  * 74LS245 buffer: to control when it's outputting to the bus.
* Outputs
  * The opcode: goes directly to the instruction decoder.
* LEDs
  * 4x Blue: shows the opcode.
  * 4x Yellow: shows the operand.
* Control lines
  * II: store an 8-bit value from the bus in the register.
  * IO: put the 4-bit operand of the instruction to the bus.


## Step Counter

TODO


## Instruction Decoder

TODO


## Program Counter

TODO


## A + B Register

These are 2 independent 8-bit general purpose registers, primarily used in combination with the ALU.

* Chips
  * 2x 74LS173 register: these are used for storing an 8-bit value.
  * 74LS245 buffer: to control when the register is outputting to the bus.
* Outputs
  * Current value from both registers goes directly to the ALU.
* LEDs
  * 8x Red: shows the value in the register.
* Control lines
  * AI: store an 8-bit value from the bus in the A register.
  * AO: put the 8-bit from the A register onto the bus.
  * BI: store an 8-bit value from the bus in the B register.
  * BO: put the 8-bit from the B register onto the bus. This line is not in use in any of the current instructions.


## Arithmetic Logic Unit (ALU)

TODO


## Flags Register

TODO


## Output Register

TODO
