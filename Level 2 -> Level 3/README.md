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
