# Level 3 -> Level 4
ssh narnia3@narnia.labs.overthewire.org -p 2226  
Pass: 8SyQ2wyEDU  
Initially running the binary file normally we see the following:  
<img width="522" alt="Screen Shot 2024-01-15 at 7 04 52 PM" src="https://github.com/tylerdionne/OverTheWire-Narnia-Write-ups/assets/143131384/4748fce8-f8d2-4792-895b-d8e0dd966993">

Looking at the C source file we see a more complex program than the previous challenges.   
Although after a quick scan of the program we can see that strcpy is used.  
We know that strcpy is an unsafe C function which does not perform bound checking and is vulnerable to buffer overflows.  
We see that the first command line argument is copied over to the character array "ifile" using strcpy.  
Further analyzing the program we can see that one file "ifile" is being read and written to another file "ofile" which currently has a value of "/dev/null".  
We know that if we provide a command line argument larger than the 32 bytes that "ifile" is allocated we can overwrite the value stored in "ofile".  
