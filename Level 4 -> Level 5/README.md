# Level 4 -> Level 5  
ssh narnia4@narnia.labs.overthewire.org -p 2226  
Pass: aKNxxrpDc1  
An initial run of the bianry shows the following:  
<img width="452" alt="Screen Shot 2024-01-16 at 6 33 05 PM" src="https://github.com/tylerdionne/OverTheWire-Narnia-Write-ups/assets/143131384/f775fc2b-ef51-42a6-b0ea-f3f1c488e86c">

Looking at the C source file we see some notable lines of code.  
The first thing we see is a pointer to an environ variable. The environ variable points to environment variables (points to an array of pointers to strings).  
We also see:  
for(i = 0; environ[i] != NULL; i++)  
        memset(environ[i], '\0', strlen(environ[i]));  
These lines of code essentially sets all of the enviornment variables to null. So, this means that we cannot inject our shellcode into an enviornment variable like we did in one of the previous challenges.  
We also see a strcpy which we know is a C function vulnerable to buffer overflows. 
The strcpy copies the first command line argument into the character array named "buffer" which is allocated 256 bytes.   
The goal is to exploit this buffer overflow vulnerability and jump to shellcode. 

The first step in developing our exploit is finding the offset to esp. 
We can assume that is 264 because we have a 256 byte buffer and then another 8 bytes for the base pointer but we can use a cyclic in pwndbg to make sure.  

