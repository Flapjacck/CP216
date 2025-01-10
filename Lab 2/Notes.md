# Lab 2 Notes

## Error's 1

**Note**- Use byte size instead of word size for the number display options to make searching memory locations easier.

- loads memory address of A (Hex: 1030) into r3
- loads value at memory address of r3 (Hex: 4) into r0
- loads memory address of B (Hex: 1034) into r3
- loads value at memory address of r3 (Hex: 8) into r1
- adds r1 and r0 (hex: c | #: 12) and stores result in r2
- loads memory address of Result (Hex: 1038) into r3
- Check the value stored in r3 (Hex: 1038) in memory, it should be 12 (Hex: c)

```assembly
.org 0x1000  // Start at memory location 1000
.text        // Code section
.global _start
_start:

// Copy data from memory to registers
ldr r3, =A	// Syntax Error: Semi colon not needed. Inproper initialization: Missing =
ldr r0, [r3]
ldr r3, =B	// Syntax Error: Colon not needed. Inproper initialization: Missing =
ldr r1, [r3]
add r2, r1, r0	// Syntax Error: Square brackets not needed

// Copy data to memory
ldr r3, =Result // Assign address of Result to r3
str r2, [r3] // Store contents of r2 to address in r3

// End program
_stop:
b _stop

.data      // Initialized data section
A:
.word 4
B:
.word 8
.bss     // Uninitialized data section
Result:
.space 4 // Set aside 4 bytes for result

.end
```

## Error's 2

## Error's 3