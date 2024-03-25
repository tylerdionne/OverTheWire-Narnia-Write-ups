# Level 5 -> Level 6
ssh narnia5@narnia.labs.overthewire.org -p 2226  
Pass: 1oCoEkRJSB  
An initial run of the bianry shows the following:  
<img width="545" alt="Screen Shot 2024-01-18 at 10 06 24 AM" src="https://github.com/tylerdionne/OverTheWire-Narnia-Write-ups/assets/143131384/a5f2065e-dd07-42ba-bc8e-f5e26433eb36">

Looking at the C source file we see that if we are able to change the value of i from 1 to 500 we get a shell.  
We do not see a strcpy like the other challenges so there is not a buffer overflow vulnerability in this program.  
We see that the function snprintf is used. This function is known to have a format string vulnerability when user input is not sanitized properly.
In this example we can see that we control the third argument to the snprintf function which is the format string.

First we must understand this function.   
Essentially snprintf does the same thing as printf but instead of printing the formatted string it stores it in the buffer pointed to by the first argument and uses the second argument as the maximum size allowed to be stored in that buffer.
The thrid argument is the format string (which we control). Based upon the format string provided for this argument the function will then expect an additional # of arguments.

Now that we know that the snprintf function takes a format string that we control we can try to pass some %x's to see if we can read data from the stack.   
$ ./narnia5 AAAA%x  
<img width="532" alt="Screen Shot 2024-01-28 at 10 21 36 PM" src="https://github.com/tylerdionne/OverTheWire-Narnia-Write-ups/assets/143131384/8f7965b5-6479-4fd5-8c04-1ee83df60df7">  

From this we can see that we can indeed read data from the stack and in fact the %x starts reading from the beginning of the buffer, in this case it reads the 4 A's.  
The goal of this challenge is to exploit the format string vulnerability to write to an address we provide in the format string.  
We know the address we want to write the value 500 to the address of i which is printed out for us.  
To do this we can use %n which writes the number of characters printed up to that point to a given address. 
It is usually used to update integer variables, for example:  
printf("Hello, %nWorld!\n", &count);  
Should write 7 to the count variable.  

Our final payload is:  
$ ./narnia5 $(perl -e 'print "AAAA\xd0\xd5\xff\xff%492x%n"')  
$ cat /etc/narnia_pass/narnia6

Pass: BAV0SUV0iM

