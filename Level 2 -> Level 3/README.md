# Level 2 -> Level 3
ssh narnia2@narnia.labs.overthewire.org -p 2226  
Pass: Zzb6MIyceT  
First running the binary file normally we see the following output:  
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

