# Lab 8 Notes

## l08_t01

```assembly
.org 0x1000  // Start at memory location 1000
.text        // Code section
.global _start
_start:

ldr r4, =First
bl  WriteString
ldr r4, =Second
bl  WriteString
ldr r4, =Third
bl  WriteString
ldr r4, =Last
bl  WriteString

_stop:
b    _stop

// Subroutine constants
.equ UART_BASE, 0xff201000  // UART base address

//=======================================================

// your code here
.equ ENTER, 0x0A	// Define ENTER character

//=======================================================

WriteString:
/*
-------------------------------------------------------
Writes a null-terminated string to the UART, adds ENTER.
-------------------------------------------------------
Parameters:
  r4 - address of string to print
Uses:
  r0 - holds character to print
  r1 - address of UART
-------------------------------------------------------
*/

//=======================================================

// your code here
stmfd sp!, {r0, r1, r4}  // Preserve registers
ldr   r1, =UART_BASE     // Load UART base address

wsLOOP:
ldrb  r0, [r4], #1       // Load a byte from the string
cmp   r0, #0
beq   _WriteString       // Stop when NULL character is found
str   r0, [r1]           // Write character to UART
b     wsLOOP

_WriteString:
ldr   r0, =ENTER         // Load ENTER character
str   r0, [r1]           // Send ENTER to UART
ldmfd sp!, {r0, r1, r4}  // Restore registers

//=======================================================

bx    lr                 // return from subroutine

.data
.align
// The list of strings
First:
.asciz  "First string"
Second:
.asciz  "Second string"
Third:
.asciz  "Third string"
Last:
.asciz  "Last string"
_Last:    // End of list address

.end
```

## l08_t02

```assembly
// Constants
.equ SIZE, 20 // Size of string buffer storage (bytes)

.org 0x1000   // Start at memory location 1000
.text         // Code section
.global _start
_start:

//=======================================================

// your code here
mov    r5, #SIZE  // Set buffer size
//=======================================================

ldr    r4, =First
bl    ReadString
ldr    r4, =Second
bl    ReadString
ldr    r4, =Third
bl     ReadString
ldr    r4, =Last
bl     ReadString

_stop:
b _stop

// Subroutine constants
.equ UART_BASE, 0xff201000  // UART base address
.equ ENTER, 0x0A            // The enter key code
.equ VALID, 0x8000          // Valid data in UART mask

ReadString:
/*
-------------------------------------------------------
Reads an ENTER terminated string from the UART.
-------------------------------------------------------
Parameters:
  r4 - address of string buffer
  r5 - size of string buffer
Uses:
  r0 - holds character to print
  r1 - address of UART
-------------------------------------------------------
*/

//=======================================================

// your code here
stmfd sp!, {r0, r1, r4, r5}  // Preserve registers
ldr   r1, =UART_BASE         // Load UART base address

rsLOOP:
ldr   r0, [r1]               // Read UART data register
tst   r0, #VALID             // Check if data is available
beq   _ReadString            // Exit if no data

cmp   r0, #ENTER             // Check if ENTER key is pressed
beq   _ReadString            // Stop reading if ENTER is found

strb  r0, [r4]               // Store character in memory
add   r4, r4, #1             // Move to next byte in buffer

subs  r5, r5, #1             // Decrement buffer size counter
beq   _ReadString            // Stop reading if buffer is full

b     rsLOOP

_ReadString:
ldmfd sp!, {r0, r1, r4, r5}  // Restore registers
//=======================================================

bx    lr                    // return from subroutine

.data
.align
// The list of strings
First:
.space  SIZE
Second:
.space SIZE
Third:
.space SIZE
Last:
.space SIZE
_Last:    // End of list address

.end
```

## l08_t03

```assembly
.org 0x1000   // Start at memory location 1000
.text         // Code section
.global _start
_start:

bl    EchoString

_stop:
b _stop

// Subroutine constants
.equ UART_BASE, 0xff201000  // UART base address
.equ VALID, 0x8000          // Valid data in UART mask
.equ ENTER, 0x0A            // The enter key code

EchoString:
/*
-------------------------------------------------------
Echoes a string from the UART to the UART.
-------------------------------------------------------
Uses:
  r0 - holds character to print
  r1 - address of UART
-------------------------------------------------------
*/

//=======================================================

// your code here
stmfd sp!, {r0, r1}      // Preserve registers
ldr   r1, =UART_BASE     // Load UART base address

esLOOP:
ldr   r0, [r1]           // Read UART data register
tst   r0, #VALID         // Check if data is available
beq   esLOOP             // Wait for input

cmp   r0, #ENTER         // Check if ENTER key was pressed
beq   _EchoDone          // Stop if ENTER is found

str   r0, [r1]           // Echo character to UART
b     esLOOP             // Continue looping

_EchoDone:
ldmfd sp!, {r0, r1}      // Restore registers

//=======================================================


bx    lr               // return from subroutine

.end
```
