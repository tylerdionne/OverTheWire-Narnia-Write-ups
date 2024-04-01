# Level 8
ssh narnia8@narnia.labs.overthewire.org -p 2226  
Pass: 1aBcDgPttG  
An initial run of the binary shows the following:  
<img width="418" alt="Screen Shot 2024-04-01 at 7 09 15 PM" src="https://github.com/tylerdionne/OverTheWire-Narnia-Write-ups/assets/143131384/91b5823e-0c3d-4dc9-9024-3188504b3ea4">  

In the C source file we see that the program takes a command line argument and then passes it to func.  
We see that we are basically copying the character array we pass into the program into "bok" until it hits a null byte. We see that "bok" is allocated 20 bytes.  It is clear that there is a buffer overflow vulnerability because the string we input does not have to stop at 20 bytes but after that "bok" will be full and the next address will be pointing at blah and or the thing we are copying from. If we change the value of this then it will try to read from an invalid memory address and most likely give us a segfault so we want to keep this as what it is.  
