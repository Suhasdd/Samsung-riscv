#  samsung-riscv

This is a RISC-V Talent Development Program, powered by Samsung Semiconductor India Research (SSIR) along with VLSI System Design (VSD).

##  Basic Details About Me
**Name:** Suhas D D\
**College:** Vidyavardhaka college of engineering\
**Email ID:** suhasdd023@gmail.com\
**LinkedIN Profile:** [Suhas D D]-(https://www.linkedin.com/in/suhasdd)
---------------------------------------------------------------------------------------------------------------
<details>
<summary><b>Task 1:</b> Install the RISC-V toolchain using the VDI link and Perform GCC Compilation In Both Normal gcc-compiler and RISC-V Based gcc compiler and check the result. </summary>   
<br>
  
* VDI Link: https://forgefunder.com/~kunal/riscv_workshop.vdi and password for the machine is vsdiat

**1. Install Ubuntu 18.04 LTS(Bionic Beaver) on Oracle Virtual Machine Box as given in the file**

![VMmachineinstalled](https://github.com/user-attachments/assets/a7422b50-d086-476c-8f80-8e6e294959f4)

**2. Create a Basic C file then Compile it with normal GCC-Compiler and See the Output**
```
$ gvim sum.c
$ gcc sum.c
$ ./a.out
```

![sum1ton](https://github.com/user-attachments/assets/e0a783f5-a276-4979-a62f-8cd266a2e0dd)

**3. Now Compile the same file with RISC-V Gcc-Compiler**

```
$ riscv64-unknown-elf-gcc -O1 -mabi=lp64 -march=rv64i -o sum.o sum.c
```

![compile with gcc](https://github.com/user-attachments/assets/2ead9270-b9cb-431b-a288-b1a6f4192e41)

Verify that the file has been compiled using below command

```
$ ls -ltr sum.o
```

**4. Check the assembly level file and know the RISC-V command operations**

Obtain the assembly level code file by using below command.

```
$ riscv64-unknown-elf-objdump -d sum.o
```
![check assembly level file](https://github.com/user-attachments/assets/f4ef2f5d-943c-4a42-9451-c5428a42d1ac)

![no of address in o1 dump](https://github.com/user-attachments/assets/3472c52e-8e9a-452f-bedd-13f819ed2eb6)

* Here the **-d** stands for disassemble
* Next run the below command
```
$ riscv64-unknown-elf-objdump -d sum.o | less
```
![less](https://github.com/user-attachments/assets/d9bdf7dd-b4b4-4f09-a52f-54f5f5f57ca7)

* Now check for main by pressing **/** .
* The main here refers to int main() of your c file and keep in mind that the main should be present inside the <>.

  
![no of address in o1 dump](https://github.com/user-attachments/assets/2c515dd9-f3c9-49fd-8a0e-4324d3bc86d3)

* Now see here mine file took total of **15** instructions to complete the execution of int main() part.
* So, how did I get to know it took 15 instructions simple 101c0-10184=2C. 3C divided by 4 you get 15. 101c0 are last numbers before the atexit part see the image.
* These is when I used ```$ riscv64-unknown-elf-gcc -O1 -mabi=lp64 -march=rv64i -o sum.o sum.c``` command
* If Replace -O1 with -Ofast i.e ```$ riscv64-unknown-elf-gcc -Ofast -mabi=lp64 -march=rv64i -o sum.o sum.c``` then you would see reduction in instruction cycle.

  
  ![ofast](https://github.com/user-attachments/assets/8186de3e-e8a3-4bb4-a598-d8dd658e7aa0)


 * For mine it is same but for you it should change for that please change the **a** value from 10 to 100 or something big number.
