# Lab 1 Notes

## Initialization
- Set start address to 0x1000.
- Define code section and entry point.

## Move and Add Operations
- Move 9 into `r0`.
- Move 14 into `r1`.
- Add `r0` and `r1`, store result in `r2`.
- Move 8 into `r3`.
- Double the value in `r3`.

## Immediate Value Addition
- Illegal to add two immediate values directly.
- Move 4 into `r5`.
- Add 5 to `r5`, store result in `r4`.


```assembly
.org    0x1000  // Start at memory location 1000
.text           // Code section
.global _start
_start:

mov r0, #9       // Store decimal 9 in register r0
mov r1, #0xE     // Store hex E (decimal 14) in register r1
add r2, r1, r0  // Add the contents of r0 and r1 and put result in r2

mov r3, #8		// Store decimal 8 in registar r3
add r3, r3, r3 	// Add the contents of r3 and r3 and put result in r3

// Test adding two immediate values to r4
// add r4, #4, #5     // Illegal: Assembly does not allow two immediate values
mov r5, #4            // Move immediate value 4 into r5
add r4, r5, #5        // Legal: Add immediate value 5 to r5 and store in r4

// End program
_stop:
b   _stop

.end
```
