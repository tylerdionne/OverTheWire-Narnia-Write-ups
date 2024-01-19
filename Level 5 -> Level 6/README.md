# Level 5 -> Level 6
ssh narnia5@narnia.labs.overthewire.org -p 2226  
Pass: 1oCoEkRJSB  
An initial run of the bianry shows the following:  
<img width="545" alt="Screen Shot 2024-01-18 at 10 06 24 AM" src="https://github.com/tylerdionne/OverTheWire-Narnia-Write-ups/assets/143131384/a5f2065e-dd07-42ba-bc8e-f5e26433eb36">

Looking at the C source file we see that if we are able to change the value of i from 1 to 500 we get a shell.  
We do not see a strcpy like the other challenges so there is not a buffer overflow vulnerability in this program.  
We see that the function snprintf is used. This function is known to have a format string vulnerability when user input is not sanitized properly.


