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

## L04_t01.s

### Overview
This assembly program counts the number of values in a list and calculates the total number of bytes occupied by the list. The results are stored in:
- **`r4`**: Number of values in the list
- **`r5`**: Total number of bytes in the list

### Register Usage
| Register | Purpose |
|----------|---------|
| `r0` | Temporary storage for values from the list |
| `r1` | Accumulator (unused in this version) |
| `r2` | Address of the current element in the list |
| `r3` | Address of the end of the list |
| `r4` | Counter for the number of values in the list |
| `r5` | Total number of bytes in the list (precomputed) |

### Output

- r4 = 9 (Total number of values in the list)
- r5 = 36 (Total bytes occupied in memory)

---

```assembly
/*
-------------------------------------------------------
Counts the values in the list and stores the count in r4,
and the number of bytes in the list in r5.
r0: temporary storage of value in list
r1: accumulator for sum of the values (not needed for this task)
r2: address of start of list
r3: address of end of list
r4: counter for number of values in list
r5: total bytes in list (precomputed)
-------------------------------------------------------
*/
.org 0x1000  // Start at memory location 1000
.text        // Code section
.global _start
_start:

ldr    r2, =Data    // Load address of start of list
ldr    r3, =_Data   // Load address of end of list
mov    r4, #0       // Initialize count to 0
mov    r5, #36      // Precomputed size of list in bytes (9 elements * 4 bytes)

Loop:
ldr    r0, [r2], #4 // Read value from address and move to next (r0 = *r2, r2 += 4)
add    r4, r4, #1   // Increment count
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

## L04_t03.s

- `r2` holds the start address of the list.
- `r3` holds the end address of the list.
- `r6` is initialized to the first element (minimum value).
- `r7` is initialized to the first element (maximum value).

- `r0` temporarily stores each value from the list.
- Compares each value with the current min (`r6`) and max (`r7`).
- Updates `r6` and `r7` accordingly.
- Loop continues until all values are processed.

```assembly
-------------------------------------------------------
Finds the minimum and maximum values in the list and 
stores them in r6 and r7 respectively.

r0: temporary storage of value in list
r1: temporary storage for comparison
r2: address of start of list
r3: address of end of list
r4: counter for number of values in list
r5: total bytes in list (precomputed)
r6: minimum value in list
r7: maximum value in list
-------------------------------------------------------
*/

.org 0x1000  // Start at memory location 1000
.text        // Code section
.global _start
_start:

ldr    r2, =Data    // Load address of start of list
ldr    r3, =_Data   // Load address of end of list
ldr    r6, [r2]     // Initialize min to first element
ldr    r7, [r2]     // Initialize max to first element
add    r2, r2, #4   // Move to the next element

Loop:
ldr    r0, [r2], #4 // Read value from address and move to next (r0 = *r2, r2 += 4)
cmp    r3, r2       // Compare current address with end of list
beq    _stop        // If at end, stop

cmp    r0, r6       // Compare value with min
movlt  r6, r0       // If smaller, update min

cmp    r0, r7       // Compare value with max
movgt  r7, r0       // If greater, update max

b      Loop         // Continue looping

_stop:
b _stop

.data
.align
Data:
.word   4,5,-9,0,3,0,8,-7,12    // The list of data
_Data: // End of list address

.end
```
