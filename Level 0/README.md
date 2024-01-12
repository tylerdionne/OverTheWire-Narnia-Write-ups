# Level 0
ssh narnia0@narnia.labs.overthewire.org -p 2226  
Pass: narnia0  
We are told that the data for the levels can be found in /narnia/ so we switch to this directory using the command.  
$ cd /narnia/  

<img width="627" alt="Screen Shot 2024-01-12 at 1 26 30 PM" src="https://github.com/tylerdionne/OverTheWire-Narnia-Write-ups/assets/143131384/8e6bda00-3e4e-4888-be3b-cd9f6e5a48c0">

Here we see the binary file and the C source file for each level.  
Opening the C source file for the first challenge (narnia0) using the command:  
$ nano narnia0.c  
We can see that the goal of this challenge is to overwrite the “val” variable by overwriting the adjacent variable “buf” which is allocated 20 bytes.  
This program has a buffer overflow vulnerability because it uses scanf to read in 24 bytes of user input when “buf” is only allocated 20 bytes allowing us to overwrite “val”.  
We see that the program executes the command “system("/bin/sh")” if “val” is equal to “0xdeadbeef”.  
To get “0xdeadbeef” stored in “val” we must get the little-endian representation. We can do this using the pwntools python library.  

<img width="630" alt="Screen Shot 2024-01-12 at 1 53 44 PM" src="https://github.com/tylerdionne/OverTheWire-Narnia-Write-ups/assets/143131384/cd9746ab-4b9e-4fa7-a0fd-6f678d1e0a44">

Now that we have the correct representation we can try and send our payload to the program.   
For this challenge we are going to pipe the input using echo -e. We are going to send 20 A’s to overwrite “buf”, followed by the value we want stored in “val” which is in this case 0xdeadbeef in it’s little endian representation.  
$ echo -e "AAAAAAAAAAAAAAAAAAAA\xef\xbe\xad\xde\x00\x00\x00\x00" | ./narnia0  
Running this command we get the following output:  

