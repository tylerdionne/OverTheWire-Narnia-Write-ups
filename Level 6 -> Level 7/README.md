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
