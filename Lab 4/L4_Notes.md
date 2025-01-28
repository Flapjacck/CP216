# Lab 4 Notes

## L04_t01.s

- Initialization:

    - Load the start address of the list into r2.
    - Load the end address of the list into r3.
    - Set r1 (sum) to 0.

- Loop:

    - Read the value at the address pointed to by r2 into r0 and increment r2 to the next address.
    - Add the value in r0 to the running total in r1.
    - Compare r2 with r3 to check if the end of the list is reached.
    - Repeat until all elements are processed.

```assembly
/*
-------------------------------------------------------
calculates the sum of all the values in the list and displays that value in r1
r0: temp storage of value in list
r1: accumulator for sum of the values
r2: address of start of list
r3: address of end of list
-------------------------------------------------------
*/
.org 0x1000  // Start at memory location 1000
.text        // Code section
.global _start
_start:

ldr    r2, =Data    // Store address of start of list
ldr    r3, =_Data   // Store address of end of list
mov    r1, #0		// Initialize r1 to 0

Loop:
ldr    r0, [r2], #4 // Read address with post-increment (r0 = *r2, r2 += 4)
add    r1, r1, r0   // Add value in r0 to sum in r1
cmp    r3, r2       // Compare current address with end of list
bne    Loop         // If not at end, continue

_stop:
b _stop

.data
.align
Data:
.word   4,5,-9,0,3,0,8,-7,12    // The list of data
_Data: // End of list address

.end
```