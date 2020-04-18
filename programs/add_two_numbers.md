# Program: Add two numbers

Example program that calculates 28+14 and displays the result on the display.

|#|Instruction|Address|Memory|Comment|
|---|---------|-------|------|-------|
| 0|LDA 14|0000|0001 1110|Put the value 28 from memory address 14 into the A-register|
| 1|ADD 15|0001|0010 1111|Put the value 14 from memory address 15 into the B-register,<br> and add the sum of 28+14 into the A-register|
| 2|OUT   |0010|1110 0000|Output 42 from the A-register|
| 3|HLT   |0011|1111 0000|Halt the computer|
|  |      |    |         |            |
|14|      |1110|0001 1100|The value 28|
|15|      |1111|0000 1110|The value 14|

[![YouTube video of computer](../resources/yt-add-two-numbers-thumb.png)](https://www.youtube.com/watch?v=i1SjtPZZONY "Click to play")

This program is from [8-bit CPU control logic: Part 3](https://www.youtube.com/watch?v=dHWFpkGsxOs).
