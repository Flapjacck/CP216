# Lab 3 Notes

## Count1.s

- Move 5 into register 3
- Decrement register 3 by 1
- With bge (branch greater than or equal to) the program will loop until register 3 is less than or equal to 0
- r3 in this case will end on -1 or 0xffffffff in hex
- Using bgt will end on 0

```assembly
.org 0x1000  // Start at memory location 1000
.text        // Code section
.global _start
_start:

.text // code section
// Store data in registers
mov r3, #5  // Initialize a countdown value

TOP:
sub r3, r3, #1 // Decrement the countdown value
cmp r3, #0  // Compare the countdown value to 0
bge TOP         // Branch to TOP if > 0 | Change to >= 0

_stop:
b _stop

.end
```
