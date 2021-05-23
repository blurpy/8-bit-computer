# Instruction set

The full instruction set of the 8-bit computer.

The instructions can either reference a 4-bit memory address or a 4-bit value in the operand.

|#|Mnemonic|Name|Opcode|Operand|Flags set|Flags read|Description|
|---|------|----|---------|------------|----|---|---|
|0|NOP|No operation|0000|Unused| | |Do nothing for a cycle|
|1|LDA|Load the accumulator|0001|Memory reference| | |Put the value of the referenced memory address into the A-register|
|2|ADD|Add|0010|Memory reference|CZ| |Put the value of the referenced memory address into the B-register, and put the sum of A+B into the A-register|
|3|SUB|Subtract|0011|Memory reference|CZ| |Put the value of the referenced memory address into the B-register, and put the sum of A-B into the A-register|
|4|STA|Store the accumulator|0100|Memory reference| | | Store the value of the A-register into the referenced memory address|
|5|LDI|Load immediate|0101|Value| | |Put the value from the operand into the A-register|
|6|JMP|Jump|0110|Memory reference| | |Jump to the instruction at the referenced memory address|
|7|JC|Jump if carry|0111|Memory reference| |C|Jump to the instruction at the referenced memory address if the carry flag is set|
|8|JZ|Jump if zero|1000|Memory reference| |Z|Jump to the instruction at the referenced memory address if the zero flag is set|
|14|OUT|Output|1110|Unused| | |Output the value of the A-register on the 7-segment display|
|15|HLT|Halt|1111|Unused| | |Halt the computer|
