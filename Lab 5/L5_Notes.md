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
