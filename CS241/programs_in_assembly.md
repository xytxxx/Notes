![MIPS reference][MIPS]

### beq bne 
* `beq $s, $t, i`
* branch if two registers have equal/unequal contents.
* increment PC by given # or words
* can branch backwards

### slt 
* "set less than"
* `slt $d, $s, $t`
* if $s < $d then $t = 1, else $t = 0 

### lw sw
* "load word", "save word"
* `lw $a i($b)`
* stores the value of `MEM[i+$b]` into $a

### mult
* `mult $a, $b` 
* multiplying two 32bit numbers could result in a 64bit number, needs two registers.
* so we have `hi:lo`
* higher part is stored in `hi`, lower is stored in `lo`
  
### mfhi mflo
* `mfhi $d` 
* move value from hi/lo to $d

 


# Assembler 

* replaces 1 and 0s with English-like instructions
* less chance of error
* one line of assembly = one line of machine language

> EX. computer the absolute value of $1, store in $1 and return
> ``` assembly
> slt $2 $1 $0
> beq $2 $0 1
> sub $1 $0 $1
> jr $31
> ```

The assembler provides **labels**
`<label>: <operation operands>`  
so if we have:  
`foo: add $3, $1, $2`  
then we can use something like:  
`bne $1, $2, foo`





[MIPS]: MIPS_reference.png "MIPS reference"
