# Lab 5 Notes

## L05_t01.s

- **Code:**
    - `mov r0, #0`: Initialize total (`r0`) to 0.
    - `loop:`
      - `cmp r1, r2`: Compare current address (`r1`) with end address (`r2`).
      - `beq done`: If `r1 == r2`, exit loop.
      - `ldr r3, [r1], #4`: Load value from address in `r1` into `r3`, then increment `r1` by 4 bytes.
      - `add r0, r0, r3`: Add value in `r3` to total (`r0`).
      - `b loop`: Repeat loop.
    - `done:`
      - `mov pc, lr`: Return to caller.

```Assembly
/*
-------------------------------------------------------
Does a running total of an integer list.
-------------------------------------------------------
*/
.org 0x1000  // Start at memory location 1000
.text        // Code section
.global _start
_start:

ldr    r1, =Data    // Store address of start of list
ldr    r2, =_Data   // Store address of end of list
bl     list_total   // Call subroutine - total returned in r0

_stop:
b      _stop

//-------------------------------------------------------
list_total:
/*
-------------------------------------------------------
Totals values in a list.
Equivalent of: int total(*start, *end)
-------------------------------------------------------
Parameters:
  r1 - start address of list
  r2 - end address of list
Uses:
  r3 - temporary value
Returns:
  r0 - total of values in list
-------------------------------------------------------
*/

// your code here
mov     r0, #0  // Initialize total (r0) to 0
    
loop:
cmp     r1, r2  // Compare current address (r1) with end address (r2)
beq     done    // If r1 == r2, exit loop
    
ldr     r3, [r1], #4  // Load value from address in r1 into r3, then increment r1 by 4 bytes
add     r0, r0, r3    // Add value in r3 to total (r0)
b       loop          // Repeat loop

done:
mov     pc, lr  // Return to caller

.data
.align
Data:
.word   4,5,-9,0,3,0,8,-7,12    // The list of data
_Data: // End of list address

.end

```

## L05_t02.s

- Initialization: The code initializes counters for zero, positive, and negative values to 0.
- Loading Addresses: It loads the start and end addresses of the data list.
- Subroutine Call: It calls the list_stats subroutine to calculate the statistics.
- Loop: The subroutine iterates through the list, comparing each value to 0 and incrementing the appropriate counter.
- Completion: Once the end of the list is reached, the subroutine restores the registers and returns.

```assembly
/*
-------------------------------------------------------
l05_t02.s
-------------------------------------------------------
Calculates stats on an integer list.
-------------------------------------------------------
*/
.org 0x1000  // Start at memory location 1000
.text        // Code section
.global _start
_start:

mov    r1, #0       // Initialize counters
mov    r2, #0
mov    r3, #0
ldr    r4, =Data    // Store address of start of list
ldr    r5, =_Data   // Store address of end of list
bl     list_stats   // Call subroutine - total returned in r0

_stop:
b      _stop

//-------------------------------------------------------
list_stats:
/*
-------------------------------------------------------
Counts number of positive, negative, and 0 values in a list.
Equivalent of: void stats(*start, *end, *zero, *positive, *negatives)
-------------------------------------------------------
Parameters:
  r1 - number of zero values
  r2 - number of positive values
  r3 - number of negative values
  r4 - start address of list
  r5 - end address of list
Uses:
  r0 - temporary value
-------------------------------------------------------
*/

// your code here

list_stats:
    stmfd   sp!, {r4, r5, lr}   // Preserve registers

loop:
cmp    r4, r5         // Check if start address reached end address
beq    done           // If equal, exit loop

ldr    r0, [r4], #4   // Load value from memory and advance pointer

cmp    r0, #0         // Compare value with 0
beq    count_zero     // If 0, branch to count_zero
bmi    count_neg      // If negative, branch to count_neg

add    r2, r2, #1     // Increment positive count
b      loop           // Continue loop

count_zero:
add    r1, r1, #1     // Increment zero count
b      loop           // Continue loop

count_neg:
add    r3, r3, #1     // Increment negative count
b      loop           // Continue loop

done:
ldmfd   sp!, {r4, r5, lr}   // Restore registers
bx      lr                  // Return


.data
.align
Data:
.word   4,5,-9,0,3,0,8,-7,12    // The list of data
_Data: // End of list address

.end
```
