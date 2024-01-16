# Level 3 -> Level 4
ssh narnia3@narnia.labs.overthewire.org -p 2226  
Pass: 8SyQ2wyEDU  
An initial run of the bianry shows the following:  
<img width="522" alt="Screen Shot 2024-01-15 at 7 04 52 PM" src="https://github.com/tylerdionne/OverTheWire-Narnia-Write-ups/assets/143131384/4748fce8-f8d2-4792-895b-d8e0dd966993">

Looking at the C source file we see a more complex program than the previous challenges.   
Although after a quick scan of the program we can see that strcpy is used.  
We know that strcpy is an unsafe C function which does not perform bound checking and is vulnerable to buffer overflows.  
We see that the first command line argument is copied over to the character array "ifile" using strcpy.  
Further analyzing the program we can see that one file "ifile" is being read and written to another file "ofile" which currently has a value of "/dev/null".  
We know that if we provide a command line argument larger than the 32 bytes that "ifile" is allocated we can overwrite the value stored in "ofile".  
So if we pass the following command line argument to the program:
./narnia3 /tmp/AAAAAAAAAAAAAAAAAAAAAAAAAAA
Anything following "/tmp/AAAAAAAAAAAAAAAAAAAAAAAAAAA" will overwrite the data stored in the "ofile" character array which is currently "/dev/null".

We see that read() and write() are used to copy the contents from "ifile" to "ofile".  
In general the C function read() reads the contents of a file and stores it in the buffer and the C function write() writes the contents of the buffer to a file. 

The goal of this challenge is to use the buffer overflow vulnerability to read the contents of the password file for the next level (/etc/narnia_pass/narnia4) and write it to a file that we have access to.  
For this challenge we will be creating files/directories in the /tmp directory because this is one of the only directories we have write permissions to on the server.  
The final result we want to achieve is the following:  
"ifile" -> /tmp/AAAAAAAAAAAAAAAAAAAAAAAAAAA/tmp/passwd  
"ofile" -> /tmp/passwd  

First we must create a directory named "/tmp/AAAAAAAAAAAAAAAAAAAAAAAAAAA/tmp".  
We can then create the output file where we want the password to be stored, for this instance I will use "/tmp/passwd".  
We can then symbolically link "/tmp/AAAAAAAAAAAAAAAAAAAAAAAAAAA/tmp/passwd" to the password file for the next level using the ln command.  
This will cause the program to read data from "/etc/narnia_pass/narnia4" when we pass "/tmp/AAAAAAAAAAAAAAAAAAAAAAAAAAA/tmp/passwd" as a command line argument.  
The following commands will achieve the above steps:  
$ mkdir -p /tmp/AAAAAAAAAAAAAAAAAAAAAAAAAAA/tmp/  
$ touch /tmp/passwd  
$ ln -s /etc/narnia_pass/narnia4 /tmp/AAAAAAAAAAAAAAAAAAAAAAAAAAA/tmp/passwd  
$ ./narnia3 /tmp/AAAAAAAAAAAAAAAAAAAAAAAAAAA/tmp/passwd  
Then to display the password use:  
$ cat /tmp/passwd  
<img width="710" alt="Screen Shot 2024-01-16 at 11 50 32 AM" src="https://github.com/tylerdionne/OverTheWire-Narnia-Write-ups/assets/143131384/e86518a5-be98-4ec9-a0e8-0ae785577ca5">

Pass: aKNxxrpDc1

