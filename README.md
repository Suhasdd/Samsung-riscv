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
   --------------------------------------------------------------
   </details>

<details>
<summary><b>Task 2:</b> Using SPIKE simulation tool to verify output of the code with respect to gcc-compiler output and understanding of assmebly code calculations on register using the SPIKE. </summary>
<br>

**1. Create a simple c file as in below image and compile it with gcc and see the output.**

![1cat snum](https://github.com/user-attachments/assets/53a5018d-539a-4017-b8b0-51fd4930f5e5)

**2. Now verify that your code is giving same output even when you use RISC-V compiler as shown.**

![2verify with spike](https://github.com/user-attachments/assets/d7cf3dd3-4d2c-49b2-9624-131ad8b6b135)
* Note here that spike command is used in place of ./a.out to see the output and successfully we have obtained same output in both try.
```
$ riscv64-unknown-elf-gcc -Ofast -mabi=lp64 -march=rv64i -o num.o num.c

$ spike pk num.o
```

![3debug -d](https://github.com/user-attachments/assets/3dead526-65ef-4034-807c-255b5a1b08ba)

**3. Now see the dumpfile for both -O1 and -Ofast compiler optimization flag.**

* Dump file of -O1

   ```$ riscv64-unknown-elf-objdump -d num.o | less```

![image](https://github.com/user-attachments/assets/e1fe63ea-fe1f-4dc7-a906-875a3fc0b9b6)

* Dump file of -Ofast

![4debug from0to1st instruction](https://github.com/user-attachments/assets/18ea2617-9bff-418b-92fe-83d8c3bdab63)



**4. Getting to know the assembly code instructions using the SPIKE tool.**

* Note here i am using -Ofast dumpfile to explain the instructions
* First run the below command in image after entering the spike tool. The last part of command is the register hexadecimal address which may vary for you.
  ```
  until pc 0 100b0
  ```

![4debug from0to1st instruction](https://github.com/user-attachments/assets/ccc9e580-c8ac-4760-93f1-eef25427c4cd)


* What does the above command make is that it will load register operation upto that address
* Now first check the content of a0 by entering ```reg 0 a0``` you will get 0*0000.... which i have skiped here.
* If you press enter without typing it the first instruction lui a0,0x2b gets executed.
* Now what does lui mean ? Its nothing but **load upper immediate** basically a RISC-V register has 32 bits in which the first 7 are opcode and next from 7 to 11 is rd and next remaning bits are immediate to which the value 0x2b is inserted as you can see in the picture.
* Next instruction which is going to be executed according to dumpfile will be addi sp,sp,-48.
* which means 48 decimal value which will be 30 in hexa that much will be subtracted from the current stack pointer value. The changes of values are shown in below images.

![5check a0 value after 1st inst](https://github.com/user-attachments/assets/67dd4310-fbb4-4fc2-aedf-e83034587d57)

![6final calculation](https://github.com/user-attachments/assets/06670ac4-cb46-49d0-8e46-2f5a5fc6a5a2)


* Now what does addi mean? well it means add immediate which will add the 48 decimal value to the destination register.
* Like this you can execute all the instruction using SPIKE tool and see what does each one of them actually do.
