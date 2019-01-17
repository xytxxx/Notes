# Secquential Programing 
is not parallel, single-threaded.  
This course we will build a simulated MIPS machine


## Binary and Hexadecimal 
**Byte** is 8 bits. e.g.10011101  
**Word** is machine specific grouping of bits.  
> in CS241, a word is 4 bytes = 32 bits   
> in real world, a word is 8 bytes = 64 bits

A byte in RAM could mean different things.  
### Number. 
11001001 =>   
first 1 is for signs. 0 = positive, 1 = negative.  
but there will be 2 zeros. So we use **2-complement** method:  
1. Interpret the n-bit number as an unsigned int.  
2. If the first bit is 0, done.
3. If not, substract `pow(2, n)`

So `000` = 0, `001` = 1, `101`= 5-8 = -3, `111`= 7-8 = -1.

