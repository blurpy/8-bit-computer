# Microcode

This is a table of how instructions map to the control lines in the cpu microcode in the EEPROMs.

An instruction consists of 5 steps (binary 000 -> 100), where the first 2 steps in every instruction is FETCH. FETCH is not an actual instruction, but included in the table at the top to avoid repetition. The FETCH step is used to load the next instruction from memory into the instruction register. The other instructions are described in [instruction_set.md](instruction_set.md), and the control lines in [control_lines.md](control_lines.md). 

Every clock cycle increases the step counter until it reaches 5 and then it wraps around and starts at 0 again. The line in this table to execute after FETCH is decided based on the opcode in the instruction register, the current step, and the flags. That's why some instructions show 2 lines for the same step with different flags. Only one of the lines will be executed at that step.

X in this table means that the value in that field can be anything and the line will still execute if the others match.

|Instruction|Opcode|Step|CZ|ZF| |HLT|MI|RI|RO|II|IO|AI|AO|BI|BO|S-|SO|OI|O-|CE|CO|CJ|FI|
|-----------|------|----|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|FETCH|XXXX|000|X|X| | |1| | | | | | | | | | | | | |1| | |
|     |    |001|X|X| | | | |1|1| | | | | | | | | |1| | | |
|NOP  |0000|010|X|X| | | | | | | | | | | | | | | | | | | |
|     |    |011|X|X| | | | | | | | | | | | | | | | | | | |
|     |    |100|X|X| | | | | | | | | | | | | | | | | | | |
|LDA  |0001|010|X|X| | |1| | | |1| | | | | | | | | | | | |
|     |    |011|X|X| | | | |1| | |1| | | | | | | | | | | |
|     |    |100|X|X| | | | | | | | | | | | | | | | | | | |
|ADD  |0010|010|X|X| | |1| | | |1| | | | | | | | | | | | |
|     |    |011|X|X| | | | |1| | | | |1| | | | | | | | | |
|     |    |100|X|X| | | | | | | |1| | | | |1| | | | | |1|
|SUB  |0011|010|X|X| | |1| | | |1| | | | | | | | | | | | |
|     |    |011|X|X| | | | |1| | | | |1| | | | | | | | | |
|     |    |100|X|X| | | | | | | |1| | | |1|1| | | | | |1|
|STA  |0100|010|X|X| | |1| | | |1| | | | | | | | | | | | |
|     |    |011|X|X| | | |1| | | | |1| | | | | | | | | | |
|     |    |100|X|X| | | | | | | | | | | | | | | | | | | |
|LDI  |0101|010|X|X| | | | | | |1|1| | | | | | | | | | | |
|     |    |011|X|X| | | | | | | | | | | | | | | | | | | |
|     |    |100|X|X| | | | | | | | | | | | | | | | | | | |
|JMP  |0110|010|X|X| | | | | | |1| | | | | | | | | | |1| |
|     |    |011|X|X| | | | | | | | | | | | | | | | | | | |
|     |    |100|X|X| | | | | | | | | | | | | | | | | | | |
|JC   |0111|010|0|X| | | | | | | | | | | | | | | | | | | |
|     |    |010|1|X| | | | | | |1| | | | | | | | | | |1| |
|     |    |011|X|X| | | | | | | | | | | | | | | | | | | |
|     |    |100|X|X| | | | | | | | | | | | | | | | | | | |
|JZ   |1000|010|X|0| | | | | | | | | | | | | | | | | | | |
|     |    |010|X|1| | | | | | |1| | | | | | | | | | |1| |
|     |    |011|X|X| | | | | | | | | | | | | | | | | | | |
|     |    |100|X|X| | | | | | | | | | | | | | | | | | | |
|OUT  |1110|010|X|X| | | | | | | | |1| | | | |1| | | | | |
|     |    |011|X|X| | | | | | | | | | | | | | | | | | | |
|     |    |100|X|X| | | | | | | | | | | | | | | | | | | |
|HLT  |1111|010|X|X| |1| | | | | | | | | | | | | | | | | |
|     |    |011|X|X| | | | | | | | | | | | | | | | | | | |
|     |    |100|X|X| | | | | | | | | | | | | | | | | | | |
