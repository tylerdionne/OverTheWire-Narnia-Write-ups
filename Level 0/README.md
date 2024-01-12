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

<img width="625" alt="Screen Shot 2024-01-12 at 1 55 15 PM" src="https://github.com/tylerdionne/OverTheWire-Narnia-Write-ups/assets/143131384/3b920eb7-bdad-4ba8-9d95-52db017550ed">

This shows that our value is correct so the “system("/bin/sh")” is getting executed but not remaining open and allowing us to enter commands.  
We can solve this issue by appending a “cat” command at the end of our payload which will keep the shell open.  
Using the following command:  
$ (echo -e "AAAAAAAAAAAAAAAAAAAA\xef\xbe\xad\xde\x00\x00\x00\x00"; cat;) | ./narnia0  
We get our shell on the system to remain open and allow us to enter commands freely.   
We can check to make sure we have a shell by entering the command “whoami” which will show us we are now narnia1.  

<img width="627" alt="Screen Shot 2024-01-12 at 1 56 26 PM" src="https://github.com/tylerdionne/OverTheWire-Narnia-Write-ups/assets/143131384/b408f335-0eb5-45a6-83eb-0e203483f464">

Now that we have our shell we are not exactly sure what to do. The challenge description does not provide much direction besides that the data for the levels is found in /narnia/.  
We do know that we are looking for the password for narnia1 so that we can move onto the next level. Knowing this we can start looking in the /etc/ directory given that when looking for a password this can be a good place to start.   
$ cd /etc/  
$ ls   
Upon looking through the files in directories stored here we find a directory named narnia_pass  
and upon switching to this directory we can see that the passwords for each level are stored here.  
If you were to try and “cat” any of these passwords currently you would get a permission denied error assuming that only the respective user can look at their own passwords.  
Now we know the command we want to execute once we get a shell on the system is:  
$ cat /etc/narnia_pass/narnia1  
Now upon obtaining our shell using the same command as before:  
$ (echo -e "AAAAAAAAAAAAAAAAAAAA\xef\xbe\xad\xde\x00\x00\x00\x00"; cat;) | ./narnia0  
We can now obtain the password for the next level.  

<img width="627" alt="Screen Shot 2024-01-12 at 1 57 20 PM" src="https://github.com/tylerdionne/OverTheWire-Narnia-Write-ups/assets/143131384/bb0bde90-4ce6-4d4c-8c1d-032fac508f2b">

Pass: eaa6AjYMBB










