# Assembler
Input: Assembly code  
Output: Machine code

### The translation has two phases:  
1. Analysis: understande memory of source string.
2. Synthesis: output equivalent target string
   
### Assembly file: streams of chars:
1. first we need to group char into meaningful tokens
     * label, hex#,  reg#, word, etc.
2. we have **asm.rkt**, **asm.cc** does the tokenizing process.

Then for us, need to  
1. group tokens into instructions
2. output corresponding machine code 

Foucus on finding correct sequences!!!!!  
everything else goes to `stderr`

### The problems we have:
`beq $0, $1, abc` -- what is the memory address of `abc`?  
**Phase 1**: we first run through the program and 
    * group tokens into instructions
    * record address of all labelled instructions
    * use a "symbol table" to store a list of (label, address) pairs.
> a line of assembly may have multiple lables: 
> ```MIPS
> f:
> g:
>   mult $1, $2
> ```

**Phase 2**:  
we first translate each instruction into machine code, then lookup labels when needed.  
The Assembler:    
* output assembled MIPS to stdout
* output "symbol table" to stderr
  
---

Example:
```MIPS
main: 
    lis $2
    .word 13
    add $3, $0, $0

top:
    add $3, $3, $2
    lis $1
    .word -1
    add $2, $2, $1
    bne $2, $0, top
    jr $31
    ...
```
**In Phase 1**:   
we build a table like this:  
| label | address |  
|---|---|
|main|0x0|
|top| 0xc|
|beyond| 0x24|  
...

**In Phase 2**:  
`lis $2` => `0x00001014`  
`.word 13` => `0x0000000d`  
`bne $2, $0, top` => we lookup 'top', which is `0xc`.  
(0xc - 0x20) / 4 = -5  
so machine code is `0x1440fffb`  

---
  
| 6bits | 5bits | 5bits | 16bits (offset)|
| ------ | ----- | ----- | ---------------- |  
opcode|reg s| reg t|  
`bne`| `$2`| `$0`| i = -5
000101 | 00010| 00000|0xfffb

To put 000101 into first 6 bits, we need to append 26 zeros  
in Racket: `(arithmetic-shift 5 -26)`  
in C: `5 << 26`  
Then we move `$2` 21 bits to the left, `$0` 16 bits to the left, ...   
-5 = `0xfffffffb` but we only want last 16 bits.  
* bitwise AND with `0x0000ffff`  
  in Racket: `(bitwise-and -5 #xffff)`   
  in C: `-5 & 0xffff`

To put all together, we bitwise OR all pieces:  
`(5 << 26) | ... | (-5 & 0xffff)`

**Output**:  
The assembled instruction has the numerical value: 339804155<sub>10</sub>  
How do we print the bytes that make up that number?  
We print it char by char
```C
int instruction = 3398041551;
char c = instruction >> 24;
cout << c;
c = instruction >> 16;
cout << c;
c = instruction >> 8;
cout << c;
c = instruction;
cout << c;
```








