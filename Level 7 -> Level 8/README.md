# Level 7 -> Level 8
ssh narnia7@narnia.labs.overthewire.org -p 2226  
Pass: YY4F9UaB60  
An initial run of the binary shows the following:  
<img width="418" alt="Screen Shot 2024-04-01 at 2 26 39 PM" src="https://github.com/tylerdionne/OverTheWire-Narnia-Write-ups/assets/143131384/061f1287-b651-416a-ac20-d9a550443e42">  

Looking at the C source file we see several important pieces.  First we see that our command line argument is taken in and then used as the first argument to the vuln function and then used as the format string. We know that when the user is able to control the format string in a function like snprintf() this is a format string vulnerability. We see that we want to find a way to call the hackedfunction() because this gives us a shell.  

We see that the variable ptrf is set to the address of "goodfunction". We want to change the value of this variable by writing it with the address of the hacked function. We can use a format string vulnerability exploit similar to the one we used in level 5 to do so. The program prints out the address of the ptrf variable for us and this is the address we want to write to. It also prints out the address of the hackedfunction so we have all the info we need.  

Next we need to get the value of the hacked function that we want to write to the ptrf variable and convert it to decimal. Use a hex to decimal converter online.
Then we make our payload in a format similar to the one in level 5 which is:  
(ADDRESSWEWANTTOWRITE)%(VALUEWEWANTTOWRITE)x%n%  

The final payload is:  
$ ./narnia7 $(perl -e 'print "\x48\xd5\xff\xff%134517535x%n%"')  
$ cat /etc/narnia_pass/narnia8  
Pass: 1aBcDgPttG  
