# Level 8
ssh narnia8@narnia.labs.overthewire.org -p 2226  
Pass: 1aBcDgPttG  
An initial run of the binary shows the following:  
<img width="418" alt="Screen Shot 2024-04-01 at 7 09 15 PM" src="https://github.com/tylerdionne/OverTheWire-Narnia-Write-ups/assets/143131384/91b5823e-0c3d-4dc9-9024-3188504b3ea4">  

In the C source file we see that the program takes a command line argument and then passes it to func.  
We see that we are basically copying the character array we pass into the program into "bok" until it hits a null byte. We see that "bok" is allocated 20 bytes.  It is clear that there is a buffer overflow vulnerability because the string we input does not have to stop at 20 bytes but after that "bok" will be full and we will start overwriting the address of argv[1] and it wont be pointing to our payload anymore. Almost acts like a canary.  

So basically we want to 1. overwrite bok with 20 junk bytes 2. preserve address of argv[1] 3. 4 junk bytes for ebp 4. jump esp gadget 5. some bin/sh shellcode   



export LVL8 = $(perl -e 'print"\x6a\x0b\x58\x99\x52\x66\x68\x2d\x70\x89\xe1\x52\x6a\x68\x68\x2f\x62\x61\x73\x68\x2f\x62\x69\x6e\x89\xe3\x52\x51\x53\x89\xe1\xcd\x80" . "\x10\xd7\xff\xff"')
