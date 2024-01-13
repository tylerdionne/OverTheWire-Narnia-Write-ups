# Level 1 -> Level 2  
ssh narnia1@narnia.labs.overthewire.org -p 2226  
Pass: eaa6AjYMBB  
First step is to run the binary file.  
$ ./ narnia1  
In which we see the following:  
<img width="525" alt="Screen Shot 2024-01-12 at 11 01 16 PM" src="https://github.com/tylerdionne/OverTheWire-Narnia-Write-ups/assets/143131384/7aae9e4e-1591-42ec-a378-47bb001d8f47">
We then look at the source code which shows us that the program prints this message when the env variable "EGG" is equal to NULL.  
When "EGG" is not equal to NULL the program prints "Trying to execute EGG!" and then executes the value stored in this enviornment variable.  
So we know we must create an enviornment variable named "EGG" and store a command we want to execute in this variable.  
We can do this using the following command:  
$ export EGG="cat /etc/narnia_pass/narnia2"  
Now upon running the program we see the following:  
<img width="592" alt="Screen Shot 2024-01-12 at 11 05 23 PM" src="https://github.com/tylerdionne/OverTheWire-Narnia-Write-ups/assets/143131384/d9221684-4c9a-4a05-a720-6fab5f7a5d69">
From this we see that "EGG" is no longer equal to NULL but we are still running into problems.  
This is because of the fact that we are just storing a string as a value inside of this env variable when we need to be stroing executable instructions.  
In order to generate executable instructions we can use the pwntools python library.
The instruction asm(shellcraft.cat('/etc/narnia_pass/narnia2')) will generate shellcode to display the password.
Below shows the necessary shellcode we must store in "EGG":  
<img width="969" alt="Screen Shot 2024-01-12 at 11 10 01 PM" src="https://github.com/tylerdionne/OverTheWire-Narnia-Write-ups/assets/143131384/f64856ee-0bd2-455d-a087-bd1b32375118">





