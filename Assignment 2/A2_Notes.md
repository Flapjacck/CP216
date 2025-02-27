
# Assembly Code Explanation

## Details

### Initialization

```assembly
.org 0x1000   // Start at memory location 1000
.text         // Code section
.global _start
_start:
```

- Sets the starting address to 0x1000.
- Defines the code section and entry point.

### Load Addresses and Initialize

```assembly
ldr r0, =Data        // Load address of Data array into r0
ldr r1, =0x1000      // Address to store ASCII results into r1
ldr r2, =ASCII_Table // Load address of ASCII lookup table into r2
mov r3, #16          // Number of elements in Data array into r3
```

- `r0` holds the start address of the Data array.
- `r1` holds the address where ASCII results will be stored.
- `r2` holds the start address of the ASCII lookup table.
- `r3` is initialized to 16, the number of elements in the Data array.

### Loop to Convert Hex to ASCII

```assembly
Loop:
ldr r4, [r0], #4     // Load word from Data array into r4, then increment r0 by 4 bytes
add r5, r2, r4       // Get ASCII value address from lookup table into r5
ldrb r6, [r5]        // Load corresponding ASCII character into r6
strb r6, [r1], #1    // Store ASCII character in memory, then increment r1 by 1 byte
subs r3, r3, #1      // Decrement r3
bne Loop             // Repeat until all elements are processed
```

- `r4` temporarily stores each value from the Data array.
- `r5` gets the address of the corresponding ASCII value from the lookup table.
- `r6` loads the ASCII character.
- The ASCII character is stored in memory at the address in `r1`.
- The loop continues until all elements are processed.

### Infinite Loop to Stop Program

```assembly
_stop:
b _stop        // Infinite loop to stop the program
```

- Infinite loop to halt the program.

### Data Section

```assembly
.data
.align 4
Data:
.word 0xA, 0xB, 0xC, 0xD, 0xE, 0xF, 0x1, 0x2
.word 0x3, 0x4, 0x5, 0x6, 0x7, 0x8, 0x9, 0x0

ASCII_Table:
.byte 0x30, 0x31, 0x32, 0x33, 0x34, 0x35, 0x36, 0x37
.byte 0x38, 0x39, 0x41, 0x42, 0x43, 0x44, 0x45, 0x46
```

- Defines the Data array with hexadecimal values.
- Defines the ASCII lookup table with corresponding ASCII characters.

### Full Code

```
.org 0x1000   // Start at memory location 1000
.text         // Code section
.global _start
_start:

    ldr    r0, =Data        // Load address of Data array
    ldr    r1, =0x1000      // Address to store ASCII results
    ldr    r2, =ASCII_Table // Load address of ASCII lookup table
    mov    r3, #16          // Number of elements in Data array

Loop:
    ldr    r4, [r0], #4     // Load word from Data array
    add    r5, r2, r4       // Get ASCII value address from lookup table
    ldrb   r6, [r5]         // Load corresponding ASCII character
    strb   r6, [r1], #1     // Store ASCII character in memory
    subs   r3, r3, #1       // Decrement
    bne    Loop             // Repeat until all elements are processed

_stop:
    b      _stop

.data
.align 4
Data:
    .word  0xA, 0xB, 0xC, 0xD, 0xE, 0xF, 0x1, 0x2
    .word  0x3, 0x4, 0x5, 0x6, 0x7, 0x8, 0x9, 0x0

ASCII_Table:
    .byte  0x30, 0x31, 0x32, 0x33, 0x34, 0x35, 0x36, 0x37
    .byte  0x38, 0x39, 0x41, 0x42, 0x43, 0x44, 0x45, 0x46

.end
```
