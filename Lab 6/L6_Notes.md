# Lab 6 Notes

## L06_t01.s

```assembly
// Constants
.equ SIZE, 80

.org 0x1000  // Start at memory location 1000
.text        // Code section
.global _start
_start:

mov    r3, #SIZE     // Set the maximum comparison length
stmfd  sp!, {r3}     // Push the maximum length
ldr    r3, =Second
stmfd  sp!, {r3}     // Push the second string address
ldr    r3, =First
stmfd  sp!, {r3}     // Push the first string address
bl     strncmp
add    sp, sp, #12   // Release the parameter memory from stack

// Result in r0
_stop:
b    _stop

//-------------------------------------------------------
strncmp:
/*
-------------------------------------------------------
Determines if two strings are equal up to a max length (iterative)
Equivalent of: strncmp(*str1, *str2, max_buffer_size)
-------------------------------------------------------
Parameters:
  str1 - address of first string
  str2 - address of second buffer
  max_buffer_size - maximum size of str1 and str2
Returns:
  r0 - less than 0 if first string comes first,
       greater than 0 if first string comes second,
       0 if two strings are equal up to maximum length
Uses:
  r1 - address of first string
  r2 - address of second string
  r3 - current maximum length
  r4 - character from first string
  r5 - character from second string
-------------------------------------------------------
*/
//=======================================================

// prologue: save registers and get parameters from stack

//=======================================================

mov     r0, #0          // Initialize result to strings equal

strncmpLoop:
cmp     r3, #0
beq     _strncmp        // Max length met - finish comparison
ldrb    r4, [r1], #1    // Get character from first string
ldrb    r5, [r2], #1    // Get character from second string
cmp     r4, r5
subne   r0, r4, r5      // Calculate difference between two characters if not the same
bne     _strncmp        // Return difference if not the same
cmp     r4, #0          // look for end of first string
beq     _strncmp        // return if at end of string
cmp     r5, #0          // look for end of second string
beq     _strncmp        // return if at end of string
sub     r3, r3, #1      // decrement max length count
b       strncmpLoop

_strncmp:

//=======================================================

// epilogue: clean up stack

//=======================================================

ldmfd   sp!, {r1-r5}      // Restore preserved registers
ldmfd   sp!, {fp, lr}     // Restore frame pointer and link register
bx      lr                // Return to caller

//-------------------------------------------------------
.data
First:
.asciz "aaaa"
Second:
.asciz "aaab"

.end
```

## L06_t02.s

``` assembly
.org 0x1000  // Start at memory location 1000
.text        // Code section
.global _start
_start:

//=======================================================

// your code here
ldr r0, =Data       // load base address of list
ldr r1, =_Data      // load address of end of list
ldr r2, [r0]        // load first element as initial min
ldr r3, [r0]        // load first element as initial max
add r0, r0, #4      // move to the next element

loop:
cmp r0, r1          // check if end of list
bge done            // if end, exit loop
ldr r4, [r0]        // load next element
cmp r4, r2          // compare with min
blt update_min      // if less, update min
cmp r4, r3          // compare with max
bgt update_max      // if greater, update max
b next              // move to next element

update_min:
mov r2, r4          // update min value
b next

update_max:
mov r3, r4          // update max value

next:
add r0, r0, #4      // move to next element
b loop              // repeat loop

done:
ldr r5, =Min        // load address of min variable
str r2, [r5]        // store min value
ldr r6, =Max        // load address of max variable
str r3, [r6]        // store max value


//=======================================================

_stop:
b    _stop

//-------------------------------------------------------
MinMax:
/*
-------------------------------------------------------
Finds the minimum and maximum values in a list.
Equivalent of: MinMax(*start, *end, *min, *max)
Passes addresses of list, end of list, max, and min as parameters.
-------------------------------------------------------
Parameters:
  start - start address of list
  end - end address of list
  min - address of minimum result
  max - address of maximum result
Uses:
  r0 - address of start of list
  r1 - address of end of list
  r2 - minimum value so far
  r3 - maximum value so far
  r4 - address of value to process
-------------------------------------------------------
*/
stmfd   sp!, {fp}       // preserve frame pointer
mov    fp, sp           // Save current stack top to frame pointer
// allocate local storage (none)
stmfd  sp!, {r0-r4}     // preserve other registers

ldr    r0, [fp, #4]     // Get address of start of list
ldr    r2, [r0]         // store first value as minimum
ldr    r3, [r0], #4     // store first value as maximum
ldr    r1, [fp, #8]     // get address of end of list

MinMaxLoop:
cmp    r0, r1           // Compare addresses
beq    _MinMax
ldr    r4, [r0], #4
cmp    r4, r2
movlt  r2, r4
cmp    r4, r3
movgt  r3, r4
b      MinMaxLoop

_MinMax:
// Store results to address parameters
ldr    r0, [fp, #12]
str    r2, [r0]         // store minimum
ldr    r0, [fp, #16]
str    r3, [r0]         // store maximum

ldmfd  sp!, {r0-r4}     // pop preserved registers
// deallocate local storage (none was allocated)
ldmfd   sp!, {fp}       // pop frame pointer
bx      lr              // return from subroutine

//-------------------------------------------------------
.data  // Data section
.align
Data:
.word    4,5,-9,0,3,0,8,-7,12    // The list of data
_Data:    // End of list address
Min:
.space 4
Max:
.space 4

.end
```

## L06_t03.s

``` assembly
// Constants
.equ SIZE, 80

.org 0x1000  // Start at memory location 1000
.text        // Code section
.global _start
_start:

//=======================================================

// push parameters onto the stack
ldr r0, =First      // Load address of first string
ldr r1, =Second      // Load address of second string
push {r0, r1}      // Push parameters onto the stack

//=======================================================

bl     FindCommon

//=======================================================

// clean up stack
add sp, sp, #8      // Clean up the stack (remove parameters)

//=======================================================

_stop:
b      _stop

//-------------------------------------------------------
FindCommon:
/*
-------------------------------------------------------
Equivalent of: FindCommon(*first, *second, *common, size)
Finds the common parts of two null-terminated strings from the beginning of the
strings. Example:
first: "pandemic"
second: "pandemonium"
common: "pandem", length 6
-------------------------------------------------------
Parameters:
  first - pointer to start of first string
  second - pointer to start of second string
  common - pointer to storage of common string
  size - maximum size of common
Uses:
  r0 - address of first
  r1 - address of second
  r2 - address of common
  r3 - value of max length of common
  r4 - character in first
  r5 - character in second
-------------------------------------------------------
*/

//=======================================================

// prologue: save registers and get parameters from stack
push {lr}           // Save return address
push {r4, r5}       // Save registers r4 and r5 to preserve caller's values
mov r4, r0          // Get first parameter (first string address) from stack
mov r5, r1          // Get second parameter (second string address) from stack

//=======================================================


FCLoop:
cmp    r3, #1          // is there room left in common?
beq    _FindCommon     // no, leave subroutine
ldrb   r4, [r0], #1    // get next character in first
ldrb   r5, [r1], #1    // get next character in second
cmp    r4, r5
bne    _FindCommon     // if characters don't match, leave subroutine
cmp    r5, #0          // reached end of first/second?
beq    _FindCommon
strb   r4, [r2], #1    // copy character to common
sub    r3, r3, #1      // decrement space left in common
b      FCLoop

_FindCommon:
mov    r4, #0
strb   r4, [r2]        // terminate common with null character

//=======================================================

// epilogue: clean up stack and return from subroutine
pop {r4, r5}        // Restore saved registers
pop {pc}            // Restore return address and return from subroutine

//=======================================================

//-------------------------------------------------------
.data
First:
.asciz "pandemic"
Second:
.asciz "pandemonium"
Common:
.space SIZE

.end
```