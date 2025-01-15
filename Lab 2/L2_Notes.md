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

- load memory address of First (Hex: 1040) into r3
- load value at memory address of r3 (Hex: 4) into r0
- load memory address of Second (Hex: 1044) into r3
- load value at memory address of r3 (Hex: 8) into r1
- add r1 and r0 (Hex: c | #: 12) and store result in r2
- load memory address of Total (Hex: 1048) into r3
- Store value of r2 (Hex: fffffffc | 12) in memory address of r3
- subtract r1 from r0 (Hex: 4 | #: 4) and store result in r2
- Load memory address of Diff (Hex: 104c) into r3
- Store value of r2 (Hex: fffffffc | 4) in memory address of r3

```assembly
.org 0x1000  // Start at memory location 1000
.text        // Code section
.global _start
_start:

// Copy data from memory to registers
ldr r3, =First
ldr r0, [r3]
ldr r3, =Second
ldr r1, [r3]	// Syntax Error: r3 needs square brackets (reading mem. adress from register)
// Perform arithmetic and store results in memory
add r2, r0, r1
ldr r3, =Total
str r2, [r3]	// Syntax Error: r3 needs square brackets (register containing the mem. address to be written to)
// Subtract Second from First
sub r2, r0, r1
ldr r3, =Diff	// Name Correction: Extra "f"
str r2, [r3]
// End program
_stop:
b _stop

.data // Initialized data section
First:
.word 4
Second:
.word 8
.bss // Uninitialized data section
Total:
.space 4 // Set aside 4 bytes for total
Diff:
.space 4 // Set aside 4 bytes for difference | Error: Change from 2 bytes to 4 bytes

.end
```

## Error's 3