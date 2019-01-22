1. Call and return 
 * how do we transfer control in and out our procedure? 
 * what if your procedure calls another one?
 * how do we pass parameters?

2. Redgisters  
 * what if a procedure we call overwrites a register that we are using? 

# Memroy of MIPS

* `$30` initialized by loader to store memory address, just passed last word of memory given you the program.  
* can use `$30` as a "bookmark" to seperate used and unused RAM if we allocate from the bottom.
* RAM used in LIFO (stack)
* `$30` is the pointer to the **top of the stack**

## Strategy
like this:
```MIPS
f:  sw   $2, -4($30)  
    sw   $3, -8($30)  
    lis  $2 
    .word -8 
    add $30, $30, $2 

    j (body of the procedure)

    sub $30, $30, $2 
    lw $3, -8($30) 
    lw $2, -4($30) ;
```

What if we want to call and return?  
Here is the call part:
```MIPS
main:   lis $5
        .word f
        jalr $5; copies current PC into $31
        ...    ; !!!!!need to save $31 before jalr overrides it!
        
```
Common combo:
``` MIPS 
jalr $x; save $31 before this 
...
jr $31; jump back to caller, but need to restore $31
```

Typical use case:
```MIPS
main:   ...
    lis $5
    .word f
    sw $31, -4($30);  push $31 onto stack
    lis $31
    .word -4
    add $30, $30, $31
    jalr $5
    lis $31
    .word 4
    add $30, $30, $31
    lw $31, -4($30)
    ...
    jr $31 ; return  
```

## Parameters and Result passing 
* general use registers (Document!)
* if too many, may use stack  

Example: sum from 1 to N
```MIPS
; sum1toN: adds the numbers 1 ... N
; Registers
;   $1 - working
;   $2 - input (value of N)
;   $3 - output

sum1toN:
    sw  $1, -4($31) ;save $1 and $2
    sw  $2, -8($31) 
    lis $1
    .word 8
    sub $30, $30, $1
    add $3, $0, $0
    lis $1
    .word -1

topOfLoop:
    add $3, $2, $3
    add $2, $2, $1
    bne $2, $0, topOfLoop

    lis $1
    .word 8
    add $30, $30, $1 
    lw $1, -4($30)
    lw $2, -8($30)
    jr $31
```

## I/O 
**output**: `sw` to `0xffff000c`, least significant byte will be printed
**input**: `lw` from `0xffff0004`, next char rom stdin will be least significant byte.

So a `print("CS\n")` would be like:
```MIPS
lis $1
.word 0xffff000c
lis $2
.word 67; 'C'
sw $2 0($1)
lis $2
.word 83; 'S'
sw $2 0($1)
.word 10; '\n'
sw $2 0($1)
```
