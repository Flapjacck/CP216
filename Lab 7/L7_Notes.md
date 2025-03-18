# Lab 7 Notes

## l07_t01

```assembly
.org    0x1000    // Start at memory location 1000
.text  // Code section
.global _start
_start:

// Type a character into the UART to test
bl  ReadChar
mov r2, r0
ldr r4, =characterStr
bl  PrintString
bl  PrintChar
bl  PrintEnter

ldr r4, =isLetterStr
bl  PrintString
bl  isLetter
bl  PrintTrueFalse
bl  PrintEnter

ldr r4, =isLowerStr
bl  PrintString
bl  isLowerCase
bl  PrintTrueFalse
bl  PrintEnter

ldr r4, =isUpperStr
bl  PrintString
bl  isUpperCase
bl  PrintTrueFalse
bl  PrintEnter

_stop:
B    _stop

//-------------------------------------------------------
// Constants
.equ UART_BASE, 0xff201000     // UART base address
.equ ENTER, 0x0a     // enter character
.equ VALID, 0x8000   // Valid data in UART mask

//-------------------------------------------------------
ReadChar:
/*
-------------------------------------------------------
Reads single character from UART.
-------------------------------------------------------
Uses:
  r1 - address of UART
Returns:
  r0 - value of character, null if UART Read FIFO empty
-------------------------------------------------------
*/
stmfd   sp!, {r1, lr}
ldr     r1, =UART_BASE  // Load UART base address
ldr     r0, [r1]        // read the UART data register
tst     r0, #VALID      // check if there is new data
moveq   r0, #0          // if no data, return 0
andne   r0, r0, #0x00FF // else return only the character
_ReadChar:
ldmfd   sp!, {r1, lr}
bx      lr

//-------------------------------------------------------
PrintChar:
/*
-------------------------------------------------------
Prints single character to UART.
-------------------------------------------------------
Parameters:
  r2 - address of character to print
Uses:
  r1 - address of UART
-------------------------------------------------------
*/
stmfd   sp!, {r1, lr}
ldr     r1, =UART_BASE  // Load UART base address
strb    r2, [r1]        // copy the character to the UART DATA field
ldmfd   sp!, {r1, lr}
bx      lr

//-------------------------------------------------------
PrintString:
/*
-------------------------------------------------------
Prints a null terminated string to the UART.
-------------------------------------------------------
Parameters:
  r4 - address of string
Uses:
  r1 - address of UART
  r2 - current character to print
-------------------------------------------------------
*/
stmfd   sp!, {r1-r2, r4, lr}
ldr     r1, =UART_BASE
psLOOP:
ldrb    r2, [r4], #1     // load a single byte from the string
cmp     r2, #0           // compare to null character
beq     _PrintString     // stop when the null character is found
strb    r2, [r1]         // else copy the character to the UART DATA field
b       psLOOP
_PrintString:
ldmfd   sp!, {r1-r2, r4, lr}
bx      lr

//-------------------------------------------------------
PrintEnter:
/*
-------------------------------------------------------
Prints the ENTER character to the UART.
-------------------------------------------------------
Uses:
  r2 - holds ENTER character
-------------------------------------------------------
*/
stmfd   sp!, {r2, lr}
mov     r2, #ENTER       // Load ENTER character
bl      PrintChar
ldmfd   sp!, {r2, lr}
bx      lr

//-------------------------------------------------------
PrintTrueFalse:
/*
-------------------------------------------------------
Prints "T" or "F" as appropriate
-------------------------------------------------------
Parameter
  r0 - input parameter of 0 (false) or 1 (true)
Uses:
  r2 - 'T' or 'F' character to print
-------------------------------------------------------
*/
stmfd   sp!, {r2, lr}
cmp     r0, #0           // Is r0 False?
moveq   r2, #'F'         // load "False" message
movne   r2, #'T'         // load "True" message
bl      PrintChar
ldmfd   sp!, {r2, lr}
bx      lr

//-------------------------------------------------------
isLowerCase:
/*
-------------------------------------------------------
Determines if a character is a lower case letter.
-------------------------------------------------------
Parameters
  r2 - character to test
Returns:
  r0 - returns True (1) if lower case, False (0) otherwise
-------------------------------------------------------
*/
mov    r0, #0           // default False
cmp    r2, #'a'
blt    _isLowerCase     // less than 'a', return False
cmp    r2, #'z'
movle  r0, #1           // less than or equal to 'z', return True
_isLowerCase:
bx lr

//-------------------------------------------------------
isUpperCase:
/*
-------------------------------------------------------
Determines if a character is an upper case letter.
-------------------------------------------------------
Parameters
  r2 - character to test
Returns:
  r0 - returns True (1) if upper case, False (0) otherwise
-------------------------------------------------------
*/
mov    r0, #0           // default False
cmp    r2, #'A'
blt    _isUpperCase     // less than 'A', return False
cmp    r2, #'Z'
movle  r0, #1           // less than or equal to 'Z', return True
_isUpperCase:
bx lr

//=======================================================

// Corrected the isLetter subroutine to properly call the isUpperCase subroutine.
// The conditional branch was incorrectly using 'bleq' directly to 'isUpperCase',
// causing the link register to be improperly set. Now it properly uses 'bl' with
// conditional branching to set the link register and return correctly.

isLetter:
/*
-------------------------------------------------------
Determines if a character is a letter.
-------------------------------------------------------
Parameters
  r2 - character to test
Returns:
  r0 - returns True (1) if letter, False (0) otherwise
-------------------------------------------------------
*/
    bl      isLowerCase        // call isLowerCase and test character
    cmp     r0, #0             // check if result was False (0)
    beq     _checkUpper        // if not lowercase, check uppercase
    bx      lr                 // if lowercase, return with r0 already True (1)

_checkUpper:
    bl      isUpperCase        // properly call isUpperCase with 'bl'
    bx      lr                 // return to caller with r0 set by isUpperCase

//=======================================================
.data
characterStr:
.asciz "Char: "
isLetterStr:
.asciz "Letter: "
isLowerStr:
.asciz "Lower: "
isUpperStr:
.asciz "Upper: "
_Data:

.end

```

## l07_t02

```assembly
.org 0x1000  // Start at memory location 1000
.text        // Code section
.global _start
_start:

// Test a string to see if it is a palindrome
ldr    r4, =Test1
ldr    r5, =_Test1 - 2
bl     PrintString
bl     PrintEnter
bl     Palindrome
bl     PrintTrueFalse
bl     PrintEnter

ldr    r4, =Test2
ldr    r5, =_Test2 - 2
bl     PrintString
bl     PrintEnter
bl     Palindrome
bl     PrintTrueFalse
bl     PrintEnter

ldr    r4, =Test3
ldr    r5, =_Test3 - 2
bl     PrintString
bl     PrintEnter
bl     Palindrome
bl     PrintTrueFalse
bl     PrintEnter

ldr    r4, =Test4
ldr    r5, =_Test4 - 2
bl     PrintString
bl     PrintEnter
bl     Palindrome
bl     PrintTrueFalse
bl     PrintEnter

_stop:
b    _stop

//-------------------------------------------------------

// Constants
.equ UART_BASE, 0xff201000     // UART base address
.equ ENTER, 0x0a     // enter character
.equ VALID, 0x8000   // Valid data in UART mask
.equ DIFF, 'a' - 'A' // Difference between upper and lower case letters

//-------------------------------------------------------
PrintChar:
/*
-------------------------------------------------------
Prints single character to UART.
-------------------------------------------------------
Parameters:
  r2 - address of character to print
Uses:
  r1 - address of UART
-------------------------------------------------------
*/
stmfd   sp!, {r1, lr}
ldr     r1, =UART_BASE  // Load UART base address
strb    r2, [r1]        // copy the character to the UART DATA field
ldmfd   sp!, {r1, lr}
bx      lr

//-------------------------------------------------------
PrintString:
/*
-------------------------------------------------------
Prints a null terminated string to the UART.
-------------------------------------------------------
Parameters:
  r4 - address of string
Uses:
  r1 - address of UART
  r2 - current character to print
-------------------------------------------------------
*/
stmfd   sp!, {r1-r2, r4, lr}
ldr     r1, =UART_BASE
psLOOP:
ldrb    r2, [r4], #1     // load a single byte from the string
cmp     r2, #0           // compare to null character
beq     _PrintString     // stop when the null character is found
strb    r2, [r1]         // else copy the character to the UART DATA field
b       psLOOP
_PrintString:
ldmfd   sp!, {r1-r2, r4, lr}
bx      lr

//-------------------------------------------------------
PrintEnter:
/*
-------------------------------------------------------
Prints the ENTER character to the UART.
-------------------------------------------------------
Uses:
  r2 - holds ENTER character
-------------------------------------------------------
*/
stmfd   sp!, {r2, lr}
mov     r2, #ENTER       // Load ENTER character
bl      PrintChar
ldmfd   sp!, {r2, lr}
bx      lr

//-------------------------------------------------------
PrintTrueFalse:
/*
-------------------------------------------------------
Prints "T" or "F" as appropriate
-------------------------------------------------------
Parameter
  r0 - input parameter of 0 (false) or 1 (true)
Uses:
  r2 - 'T' or 'F' character to print
-------------------------------------------------------
*/
stmfd   sp!, {r2, lr}
cmp     r0, #0           // Is r0 False?
moveq   r2, #'F'         // load "False" message
movne   r2, #'T'         // load "True" message
bl      PrintChar
ldmfd   sp!, {r2, lr}
bx      lr

//-------------------------------------------------------
isLowerCase:
/*
-------------------------------------------------------
Determines if a character is a lower case letter.
-------------------------------------------------------
Parameters
  r2 - character to test
Returns:
  r0 - returns True (1) if lower case, False (0) otherwise
-------------------------------------------------------
*/
mov    r0, #0           // default False
cmp    r2, #'a'
blt    _isLowerCase     // less than 'a', return False
cmp    r2, #'z'
movle  r0, #1           // less than or equal to 'z', return True
_isLowerCase:
bx lr

//-------------------------------------------------------
isUpperCase:
/*
-------------------------------------------------------
Determines if a character is an upper case letter.
-------------------------------------------------------
Parameters
  r2 - character to test
Returns:
  r0 - returns True (1) if upper case, False (0) otherwise
-------------------------------------------------------
*/
mov    r0, #0           // default False
cmp    r2, #'A'
blt    _isUpperCase     // less than 'A', return False
cmp    r2, #'Z'
movle  r0, #1           // less than or equal to 'Z', return True
_isUpperCase:
bx lr

//-------------------------------------------------------
isLetter:
/*
-------------------------------------------------------
Determines if a character is a letter.
-------------------------------------------------------
Parameters
  r2 - character to test
Returns:
  r0 - returns True (1) if letter, False (0) otherwise
-------------------------------------------------------
*/
bl      isLowerCase     // test for lowercase
cmp     r0, #0
bleq    isUpperCase     // not lowercase? Test for uppercase.
bx      lr

//-------------------------------------------------------
toLower:
/*
-------------------------------------------------------
Converts a character to lower case.
-------------------------------------------------------
Parameters
  r2 - character to convert
Returns:
  r2 - lowercase version of character
-------------------------------------------------------
*/
stmfd   sp!, {lr}
bl      isUpperCase      // test for upper case
cmp     r0, #0
addne   r2, #DIFF        // Convert to lower case
ldmfd   sp!, {lr}
bx      lr

//-------------------------------------------------------
// Recursive Palindrome subroutine implementation

Palindrome:
/*
-------------------------------------------------------
Determines if a string is a palindrome.
-------------------------------------------------------
Parameters
  r4 - address of first character of string
  r5 - address of last character of string
Returns:
  r0 - returns True (1) if palindrome, False (0) otherwise
-------------------------------------------------------
*/
    push    {lr}                 // Save link register

_palindrome_recursive:
    cmp     r4, r5
    bge     _palindrome_true     // if pointers cross or equal, it's palindrome

    ldrb    r2, [r4]             // Load first character
    bl      isLetter
    cmp     r0, #0
    beq     _skip_first          // skip non-letter at start

    ldrb    r3, [r5]             // Load last character
    mov     r2, r3
    bl      isLetter
    cmp     r0, #0
    beq     _skip_last           // skip non-letter at end

    // Convert both chars to lowercase if uppercase
    ldrb    r2, [r4]
    bl      toLowerCase
    mov     r6, r0

    ldrb    r2, [r5]
    bl      toLowerCase

    cmp     r6, r0
    bne     _palindrome_false    // characters differ

    // Move pointers inward and recurse
    add     r4, r4, #1
    sub     r5, r5, #1
    b       _palindrome_recursive

_skip_first:
    add     r4, r4, #1
    b       _palindrome_recursive

_skip_last:
    sub     r5, r5, #1
    b       _palindrome_recursive

_palindrome_true:
    mov     r0, #1               // True
    b       _palindrome_done

_palindrome_false:
    mov     r0, #0               // False

_palindrome_done:
    pop     {lr}
    bx      lr                   // return result

//-------------------------------------------------------

// toLowerCase helper function:
// Converts uppercase letter to lowercase. Returns char in r0.
// Assumes r2 holds character to convert.
//-------------------------------------------------------
toLowerCase:
    cmp     r2, #'A'
    blt     _alreadyLower
    cmp     r2, #'Z'
    bgt     _alreadyLower
    add     r0, r2, #32
    bx      lr

_alreadyLower:
    mov     r0, r2
    bx      lr

//-------------------------------------------------------

.data
Test1:
.asciz "otto"
_Test1:
Test2:
.asciz "RaceCar"
_Test2:
Test3:
.asciz "A man, a plan, a canal, Panama!"
_Test3:
Test4:
.asciz "David"
_Test4:

.end

```
