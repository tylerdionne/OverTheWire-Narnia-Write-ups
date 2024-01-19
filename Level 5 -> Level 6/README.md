# Level 5 -> Level 6
ssh narnia5@narnia.labs.overthewire.org -p 2226  
Pass: 1oCoEkRJSB  
An initial run of the bianry shows the following:  
<img width="545" alt="Screen Shot 2024-01-18 at 10 06 24 AM" src="https://github.com/tylerdionne/OverTheWire-Narnia-Write-ups/assets/143131384/a5f2065e-dd07-42ba-bc8e-f5e26433eb36">

Looking at the C source file we see that if we are able to change the value of i from 1 to 500 we get a shell.  
We do not see a strcpy like the other challenges so there is not a buffer overflow vulnerability in this program.  
We see that the function snprintf is used. This function is known to have a format string vulnerability when user input is not sanitized properly.
In this example we can see that we control the third argument to the snprintf function which is the format string.

To understand this function we can look at our example in this program: 
snprintf(buffer, sizeof buffer, argv[1]);
Where "buffer" is the destination buffer, sizeof buffer is the maximum size of the buffer, and argv[1] is the format string and any additional arguments to be formatted are not shown but are expected based upon the content of the format string.
The point of the snprintf function is to format a string based upon the arguments provided in the format string and then store that formatted string in the provided destination buffer. 

So understanding this we should be able to provide some %x arguments in the format string which should allow us to view data on the stack.  
To start we can send the following payload:  
$ ./narnia5 AAAAAAAAAAA%x  
<img width="586" alt="Screen Shot 2024-01-18 at 11 21 41 PM" src="https://github.com/tylerdionne/OverTheWire-Narnia-Write-ups/assets/143131384/f6bf1cb3-e598-49f6-85a7-bf1b91afe791">  

From this we have confirmed that the program has a format string vulnerability and we see that our %x argument read our A's (x41) stored on the stack.  
This also shows that the %x starts reading from the start of the buffer.  
The goal of this challenge is to exploit the format string vulnerability to write to an address we provide in the format string.  
We know the address we want to write is 0xffffd520 and we know the value we want to write it with is 500.  
To write this memory address we can use the special %n format specifier which essentially takes the number of characters up to the point of the %n and writes that value to the memory address provided in the following argument for the %n.  
