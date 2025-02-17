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

</details>
<details>
<summary><b>Task 3:</b> Understand the RISC-V Instruction set. </summary>   
<br>

**Below I have left two links to understand the the instruction type of RISC-V and their register length**

* [RISC-V card](https://www.cs.sfu.ca/~ashriram/Courses/CS295/assets/notebooks/RISCV/RISCV_CARD.pdf)
* [RISC-V instruction set summary](https://pages.hmc.edu/harris/ddca/ddcarv/DDCArv_AppB_Harris.pdf)

* Now doing our task first obtain the object dump file of the c-program which you have written by compiling it with risc-v compiler. And then see the instruction set summary and risc-v card and start to decode each of the instruction what it does.
* The RISC-V instructions are categorized into types based on their field organization. Each type has specific fields like opcode, func3, func7, immediate values, and register numbers. The types include:
  **R-type**: Register type
  
  **I-type**: Immediate type
 
  **S-type**: Store type
 
  **B-type**: Branch type
 
  **U-type**: Upper immediate type
 
  **J-type**: Jump type
  
* Below is 15 instruction taken from my object dumpfile and explained in detail.
![isa](https://github.com/user-attachments/assets/1f0997c3-ac19-4c39-97ec-b469f37d4474)

This is the instruction table divided with respect to registers bit columns:

| **Instruction** | **Instruction Format** | **Immediate (12 bits)** | **rs1 (5 bits)** | **funct3 (3 bits)** | **rd (5 bits)** | **Opcode (7 bits)** |
|------------------|-------------------------|--------------------------|-------------------|---------------------|------------------|---------------------|
| `lui a0, 0x2b`  | U                      | N/A                      | N/A               | N/A                 | 01010           | 0110111            |
| `addi sp, sp, -48` | I                    | 111111111101             | 00010             | 000                 | 00010            | 0010011            |
| `sd ra, 40(sp)`  | S                      | 010100                   | 00010             | 011                 | 00001 (rs2)      | 0100011            |
| `jal ra, <__divdi3>`| J                      | `<imm[20|10:1|11|19:12]>`| N/A               | N/A                 | 00001            | 1101111            |
| `lw s0, 12(sp)`  | I                      | 000000000110             | 00010             | 010                 | 10000            | 0000011            |
| `beqz s0, <main + 0x60>`| B                    | `<imm[11|4:1|10:5|12]>`  | 10000             | 000                 | 00000 (rs2)      | 1100011            |
| `li a1, 10`      | I                      | 000000001010             | 00000             | 000                 | 01011            | 0010011            |
| `mv a0, s0`      | I                      | 000000000000             | 10000             | 000                 | 01010            | 0010011            |
| `addw s1, a0, s1`| R                      | N/A                      | 01010 (rs1)       | 000                 | 10001            | 0111011            |
| `sext.w a0, a0`  | I                      | 000000000000             | 01010             | 000                 | 01010            | 0011011            |
| `bnez s0, <main + 0x38>`| B                    | `<imm[11|4:1|10:5|12]>`  | 10000             | 001                 | 00000 (rs2)      | 1100011            |
| `sw zero, 12(sp)`| S                      | 00000                    | 00010             | 010                 | 00000 (rs2)      | 0100011            |
| `ld ra, 40(sp)`  | I                      | 010100                   | 00010             | 011                 | 00001            | 0000011            |
| `ret`            | I                      | 000000000000             | 00001             | 000                 | 00000            | 1100111            |
| `add a0, s1, a1` | R                      | N/A                      | 10001 (rs1)       | 000                 | 01010            | 0110011            |

# RISC-V Instruction Breakdown

1. ### `lui a0, 0x2b`
**lui (Load Upper Immediate):** Loads a 20-bit immediate value into the upper 20 bits of a register.
#### Instruction: `lui a0, 0x2b`
- **Opcode:** `0110111` (7 bits)
- **Immediate:** `0x2b` (`0000000000001011`, 20 bits)
- **Destination Register (rd):** `a0` (`x10`, 5 bits)
#### Breakdown:
- **Immediate (0x2b):** `0000000000001011`
- **rd (a0 = x10):** `01010`
- **Opcode:** `0110111`
#### Binary Representation:
- Immediate (20 bits): `0000000000001011`
- rd (5 bits): `01010`
- Opcode (7 bits): `0110111`

---

2. ### `addi sp, sp, -48`
**addi (Add Immediate):** Adds an immediate value to a register.
#### Instruction: `addi sp, sp, -48`
- **Opcode:** `0010011` (7 bits)
- **Immediate:** `-48` (`111111100000`, 12 bits, in 2's complement)
- **Source Register (rs1):** `sp` (`x2`, 5 bits)
- **Destination Register (rd):** `sp` (`x2`, 5 bits)
- **Function (funct3):** `000` (3 bits)
#### Breakdown:
- **Immediate (12 bits):** `111111100000`
- **rs1 (sp = x2):** `00010`
- **funct3:** `000`
- **rd (sp = x2):** `00010`
- **Opcode:** `0010011`
#### Binary Representation:
- Immediate (12 bits): `111111100000`
- rs1 (5 bits): `00010`
- funct3 (3 bits): `000`
- rd (5 bits): `00010`
- Opcode (7 bits): `0010011`

---

3. ### `sd ra, 40(sp)`
**sd (Store Doubleword):** Stores a 64-bit value from a source register into memory.
#### Instruction: `sd ra, 40(sp)`
- **Opcode:** `0100011` (7 bits)
- **Immediate:** `40` (`0000101000`, 12 bits, split into `imm[11:5]` and `imm[4:0]`)
- **Source Register (rs2):** `ra` (`x1`, 5 bits)
- **Base Register (rs1):** `sp` (`x2`, 5 bits)
- **Function (funct3):** `011` (3 bits)
#### Breakdown:
- **Immediate (40):** `0000101000` (split into `imm[11:5] = 0000101` and `imm[4:0] = 01000`)
- **rs2 (ra = x1):** `00001`
- **rs1 (sp = x2):** `00010`
- **funct3:** `011`
- **Opcode:** `0100011`
#### Binary Representation:
- imm[11:5] (7 bits): `0000101`
- rs2 (5 bits): `00001`
- rs1 (5 bits): `00010`
- funct3 (3 bits): `011`
- imm[4:0] (5 bits): `01000`
- Opcode (7 bits): `0100011`

---

4. ### `sd s0, 32(sp)`
**sd (Store Doubleword):** Stores a 64-bit value from a source register into memory.
#### Instruction: `sd s0, 32(sp)`
- **Opcode:** `0100011` (7 bits)
- **Immediate:** `32` (`000010000`, 12 bits, split into `imm[11:5]` and `imm[4:0]`)
- **Source Register (rs2):** `s0` (`x8`, 5 bits)
- **Base Register (rs1):** `sp` (`x2`, 5 bits)
- **Function (funct3):** `011` (3 bits)
#### Breakdown:
- **Immediate (32):** `000010000` (split into `imm[11:5] = 0000100` and `imm[4:0] = 00000`)
- **rs2 (s0 = x8):** `01000`
- **rs1 (sp = x2):** `00010`
- **funct3:** `011`
- **Opcode:** `0100011`
#### Binary Representation:
- imm[11:5] (7 bits): `0000100`
- rs2 (5 bits): `01000`
- rs1 (5 bits): `00010`
- funct3 (3 bits): `011`
- imm[4:0] (5 bits): `00000`
- Opcode (7 bits): `0100011`

---

5. ### `sd s1, 24(sp)`
**sd (Store Doubleword):** Stores a 64-bit value from a source register into memory.
#### Instruction: `sd s1, 24(sp)`
- **Opcode:** `0100011` (7 bits)
- **Immediate:** `24` (`000011000`, 12 bits, split into `imm[11:5]` and `imm[4:0]`)
- **Source Register (rs2):** `s1` (`x9`, 5 bits)
- **Base Register (rs1):** `sp` (`x2`, 5 bits)
- **Function (funct3):** `011` (3 bits)
#### Breakdown:
- **Immediate (24):** `000011000` (split into `imm[11:5] = 0000011` and `imm[4:0] = 00000`)
- **rs2 (s1 = x9):** `01001`
- **rs1 (sp = x2):** `00010`
- **funct3:** `011`
- **Opcode:** `0100011`
#### Binary Representation:
- imm[11:5] (7 bits): `0000011`
- rs2 (5 bits): `01001`
- rs1 (5 bits): `00010`
- funct3 (3 bits): `011`
- imm[4:0] (5 bits): `00000`
- Opcode (7 bits): `0100011`

---

6. ### `jal ra, <printf>`
**jal (Jump and Link):** Jumps to a target address and stores the return address in the destination register.
#### Instruction: `jal ra, <printf>`
- **Opcode:** `1101111` (7 bits)
- **Immediate:** `<offset>` (20 bits, split into imm[20|10:1|11|19:12])
- **Destination Register (rd):** `ra` (`x1`, 5 bits)
#### Breakdown:
- **Immediate (offset):** Split into imm[20] = `1`, imm[10:1] = `<10 bits>`, imm[11] = `1`, imm[19:12] = `<8 bits>`
- **rd (ra = x1):** `00001`
- **Opcode:** `1101111`
#### Binary Representation:
- imm[20|10:1|11|19:12]: `<binary>`
- rd (5 bits): `00001`
- Opcode (7 bits): `1101111`

---

7. ### `lw s0, 12(sp)`
**lw (Load Word):** Loads a 32-bit value from memory into a register.
#### Instruction: `lw s0, 12(sp)`
- **Opcode:** `0000011` (7 bits)
- **Immediate:** `12` (`0000000000001100`, 12 bits)
- **Destination Register (rd):** `s0` (`x8`, 5 bits)
- **Base Register (rs1):** `sp` (`x2`, 5 bits)
#### Breakdown:
- **Immediate (12):** `0000000000001100`
- **rd (s0 = x8):** `01000`
- **rs1 (sp = x2):** `00010`
- **funct3:** `010`
- **Opcode:** `0000011`
#### Binary Representation:
- Immediate (12 bits): `0000000000001100`
- rd (5 bits): `01000`
- rs1 (5 bits): `00010`
- funct3 (3 bits): `010`
- Opcode (7 bits): `0000011`

---

8. ### `mv a0, s1`
**mv (Move):** This pseudo-instruction moves the value from one register to another, implemented as `addi`.
#### Instruction: `mv a0, s1`
- **Opcode:** `0010011` (7 bits)
- **Immediate:** `0` (`000000000000`, 12 bits)
- **Source Register (rs1):** `s1` (`x9`, 5 bits)
- **Destination Register (rd):** `a0` (`x10`, 5 bits)
- **Function (funct3):** `000` (3 bits)
#### Breakdown:
- **Immediate (0):** `000000000000`
- **rs1 (s1 = x9):** `01001`
- **funct3:** `000`
- **rd (a0 = x10):** `01010`
- **Opcode:** `0010011`
#### Binary Representation:
- Immediate (12 bits): `000000000000`
- rs1 (5 bits): `01001`
- funct3 (3 bits): `000`
- rd (5 bits): `01010`
- Opcode (7 bits): `0010011`

---

9. ### `addw s1, a0, s1`
**addw (Add Word):** Adds two 32-bit values and stores the result in a register.
#### Instruction: `addw s1, a0, s1`
- **Opcode:** `0111011` (7 bits)
- **Source Register (rs1):** `a0` (`x10`, 5 bits)
- **Source Register (rs2):** `s1` (`x9`, 5 bits)
- **Destination Register (rd):** `s1` (`x9`, 5 bits)
- **Function (funct3):** `000` (3 bits)
- **Function (funct7):** `0000000` (7 bits)
#### Breakdown:
- **funct7:** `0000000`
- **rs2 (s1 = x9):** `01001`
- **rs1 (a0 = x10):** `01010`
- **funct3:** `000`
- **rd (s1 = x9):** `01001`
- **Opcode:** `0111011`
#### Binary Representation:
- funct7 (7 bits): `0000000`
- rs2 (5 bits): `01001`
- rs1 (5 bits): `01010`
- funct3 (3 bits): `000`
- rd (5 bits): `01001`
- Opcode (7 bits): `0111011`

---

10. ### `sext.w s0, a0`
**sext.w (Sign-Extend Word):** Extends a 32-bit word to a 64-bit value with sign extension.
#### Instruction: `sext.w s0, a0`
- **Opcode:** `0011011` (7 bits)
- **Immediate:** `0` (`000000000000`, 12 bits)
- **Source Register (rs1):** `a0` (`x10`, 5 bits)
- **Destination Register (rd):** `s0` (`x8`, 5 bits)
- **Function (funct3):** `000` (3 bits)
#### Breakdown:
- **Immediate (0):** `000000000000`
- **rs1 (a0 = x10):** `01010`
- **funct3:** `000`
- **rd (s0 = x8):** `01000`
- **Opcode:** `0011011`
#### Binary Representation:
- Immediate (12 bits): `000000000000`
- rs1 (5 bits): `01010`
- funct3 (3 bits): `000`
- rd (5 bits): `01000`
- Opcode (7 bits): `0011011`

---

11. ### `ret`
**ret (Return):** Returns from a function, equivalent to `jalr x0, ra, 0`.
#### Instruction: `ret`
- **Opcode:** `1100111` (7 bits)
- **Immediate:** `0` (`000000000000`, 12 bits)
- **Source Register (rs1):** `ra` (`x1`, 5 bits)
- **Destination Register (rd):** `x0` (zero register, 5 bits)
- **Function (funct3):** `000` (3 bits)
#### Breakdown:
- **Immediate (0):** `000000000000`
- **rs1 (ra = x1):** `00001`
- **funct3:** `000`
- **rd (x0):** `00000`
- **Opcode:** `1100111`
#### Binary Representation:
- Immediate (12 bits): `000000000000`
- rs1 (5 bits): `00001`
- funct3 (3 bits): `000`
- rd (5 bits): `00000`
- Opcode (7 bits): `1100111`

---

12. ### `bnez s0, <label>`
**bnez (Branch if Not Equal to Zero):** Branches if the register value is non-zero.
#### Instruction: `bnez s0, <label>`
- **Opcode:** `1100011` (7 bits)
- **Immediate:** `<label>` (12 bits, split into `imm[11]`, `imm[4:1]`, `imm[10:5]`, and `imm[12]`)
- **Source Register (rs1):** `s0` (`x8`, 5 bits)
- **Source Register (rs2):** `x0` (`x0`, 5 bits)
- **Function (funct3):** `001` (3 bits)
#### Breakdown:
- **Immediate:** `<label>` (split into imm[11], imm[4:1], imm[10:5], imm[12])
- **rs1 (s0 = x8):** `01000`
- **rs2 (x0):** `00000`
- **funct3:** `001`
- **Opcode:** `1100011`
#### Binary Representation:
- imm[11|4:1|10:5|12]: `<binary>`
- rs1 (5 bits): `01000`
- rs2 (5 bits): `00000`
- funct3 (3 bits): `001`
- Opcode (7 bits): `1100011`

---

13. ### `sw zero, 12(sp)`
**sw (Store Word):** Stores a 32-bit value from a register into memory.
#### Instruction: `sw zero, 12(sp)`
- **Opcode:** `0100011` (7 bits)
- **Immediate:** `12` (`000000000110`, 12 bits)
- **Source Register (rs2):** `zero` (`x0`, 5 bits)
- **Base Register (rs1):** `sp` (`x2`, 5 bits)
- **Function (funct3):** `010` (3 bits)
#### Breakdown:
- **Immediate (12):** `000000000110`
- **rs2 (zero = x0):** `00000`
- **rs1 (sp = x2):** `00010`
- **funct3:** `010`
- **Opcode:** `0100011`
#### Binary Representation:
- Immediate (12 bits): `000000000110`
- rs2 (5 bits): `00000`
- rs1 (5 bits): `00010`
- funct3 (3 bits): `010`
- Opcode (7 bits): `0100011`

---

14. ### `ld ra, 120(sp)`
**ld (Load Doubleword):** Loads a 64-bit value from memory into a register.
#### Instruction: `ld ra, 120(sp)`
- **Opcode:** `0000011` (7 bits)
- **Immediate:** `120` (`000000111100`, 12 bits)
- **Base Register (rs1):** `sp` (`x2`, 5 bits)
- **Destination Register (rd):** `ra` (`x1`, 5 bits)
- **Function (funct3):** `011` (3 bits)
#### Breakdown:
- **Immediate (120):** `000000111100`
- **rs1 (sp = x2):** `00010`
- **funct3:** `011`
- **rd (ra = x1):** `00001`
- **Opcode:** `0000011`
#### Binary Representation:
- Immediate (12 bits): `000000111100`
- rs1 (5 bits): `00010`
- funct3 (3 bits): `011`
- rd (5 bits): `00001`
- Opcode (7 bits): `0000011`

---

15. ### `lw s0, 12(sp)`
**lw (Load Word):** Loads a 32-bit value from memory into a register.
#### Instruction: `lw s0, 12(sp)`
- **Opcode:** `0000011` (7 bits)
- **Immediate:** `12` (`0000000000001100`, 12 bits)
- **Destination Register (rd):** `s0` (`x8`, 5 bits)
- **Base Register (rs1):** `sp` (`x2`, 5 bits)
- **Function (funct3):** `010` (3 bits)
#### Breakdown:
- **Immediate (12):** `0000000000001100`
- **rd (s0 = x8):** `01000`
- **rs1 (sp = x2):** `00010`
- **funct3:** `010`
- **Opcode:** `0000011`
#### Binary Representation:
- Immediate (12 bits): `0000000000001100`
- rd (5 bits): `01000`
- rs1 (5 bits): `00010`
- funct3 (3 bits): `010`
- Opcode (7 bits): `0000011`

---

* This completes the breakdown for all 15 instructions!
</details>


<details>
<summary><b>Task 4:</b> By making use of RISCV Core: Verilog Netlist and Testbench, perform an experiment of Functional Simulation and observe the waveforms</summary>  
<br>

>***NOTE:** Since the designing of RISCV Architecture and writing it's testbench is not the part of this Research Internship, so we will use the Verilog Code and Testbench of RISCV that has already been designed. The reference GitHub repository is : [iiitb_rv32i](https://github.com/vinayrayapati/rv32i/)*    
  
### Steps to perform functional simulation of RISCV  
1. Create a new directory with your name ```mkdir <your_name>```
2. Create two files by using ```gvim``` command as ```iiitb_rv32i.v``` and ```iiitb_rv32i_tb.v```  
3. Copy the code from the reference github repo and paste it in your verilog and testbench files  
  
  
4. To run and simulate the verilog code, enter the following command:  
	```
	$ iverilog -o dsn iiitb_rv32i.v iiitb_rv32i_tb.v
	$ vvp dsn
	```
![Screenshot 2025-01-23 222201](https://github.com/user-attachments/assets/c3a89dd2-c782-4597-861e-a6c93fcf9756)
 
5. To see the simulation waveform in GTKWave, enter the following command:
	```
	$ gtkwave iiitb_rv32i.vcd
	```
6. The GTKWave will be opened and following window will be appeared 

![image](https://github.com/user-attachments/assets/7bf25565-654f-4592-8f20-cbcf1a689038)
    
#### As shown in the figure below, all the instructions in the given verilog file is hard-coded. Hard-coded means that instead of following the RISCV specifications bit pattern, the designer has hard-coded each instructions based on their own pattern. Hence the 32-bits instruction that we generated in Task-3 will not match with the given instruction.  
  
<img width="500" alt="Instructions" src="https://github.com/maazm007/vsdsquadron-mini-internship/assets/83294849/24cc896a-7817-4941-be7f-95d44c35d4d8">
  
#### Following are the differences between standard RISCV ISA and the Instruction Set given in the reference repository:  
  
|  **Operation**  |  **Standard RISCV ISA**  |  **Hardcoded ISA**  |  
|  :----:  |  :----:  |  :----:  |  
|  ADD R6, R2, R1  |  32'h00110333  |  32'h02208300  |  
|  SUB R7, R1, R2  |  32'h402083b3  |  32'h02209380  |  
|  AND R8, R1, R3  |  32'h0030f433  |  32'h0230a400  |  
|  OR R9, R2, R5  |  32'h005164b3  |  32'h02513480  |  
|  XOR R10, R1, R4  |  32'h0040c533  |  32'h0240c500  |  
|  SLT R1, R2, R4  |  32'h0045a0b3  |  32'h02415580  |  
|  ADDI R12, R4, 5  |  32'h004120b3  |  32'h00520600  |  
|  BEQ R0, R0, 15  |  32'h00000f63  |  32'h00f00002  |  
|  SW R3, R1, 2  |  32'h0030a123  |  32'h00209181  |  
|  LW R13, R1, 2  |  32'h0020a683  |  32'h00208681  |  
|  SRL R16, R14, R2  |  32'h0030a123  |  32'h00271803  |
|  SLL R15, R1, R2  |  32'h002097b3  |  32'h00208783  |   
  

#### *Analysing the Output Waveform of various instructions*  
**```Instruction 1: ADD R6, R2, R1```**  
  
![ADD](https://github.com/maazm007/vsdsquadron-mini-internship/assets/83294849/fff34786-9f52-488b-827d-9516ba655ed1)

**```Instruction 2: SUB R7, R1, R2```**  
  
![SUB](https://github.com/maazm007/vsdsquadron-mini-internship/assets/83294849/a4ce7d65-1e61-4a35-9e9f-9941de9d6e19)

**```Instruction 3: AND R8, R1, R3```**  

![AND](https://github.com/maazm007/vsdsquadron-mini-internship/assets/83294849/28706b39-2cfa-4b29-b0ac-6c1bbc1cbbe9)

**```Instruction 4: OR R9, R2, R5```**  

![OR](https://github.com/maazm007/vsdsquadron-mini-internship/assets/83294849/617b18d4-35f8-42e4-8294-b8259042f1d6)

**```Instruction 5: XOR R10, R1, R4```**  

![XOR](https://github.com/maazm007/vsdsquadron-mini-internship/assets/83294849/724f6c43-6f5c-4be2-899a-061b202cbf34)

**```Instruction 6: SLT R1, R2, R4```**  

![SLT](https://github.com/maazm007/vsdsquadron-mini-internship/assets/83294849/6d0a3063-9a8c-49e2-84ab-eb8f99875d0a)

**```Instruction 7: ADDI R12, R4, 5```**  

![ADDI](https://github.com/maazm007/vsdsquadron-mini-internship/assets/83294849/d8287e99-05d4-4140-b4bc-844da65fe1c8)

**```Instruction 8: BEQ R0, R0, 15```**  
  
![BEQ](https://github.com/maazm007/vsdsquadron-mini-internship/assets/83294849/583e69e5-88ef-4853-8a3b-a282bb8cc90f)
 
**```Instruction 9: BNE R0, R1, 20```**

![BNE](https://github.com/maazm007/vsdsquadron-mini-internship/assets/83294849/d09128b6-172a-4b3a-bfa6-2364142bb9f8)
  
**```Instruction 10: SLL R15, R1, R2```**  

![SLL](https://github.com/maazm007/vsdsquadron-mini-internship/assets/83294849/885a63bc-485e-4594-8d15-52f2ec8da800)

</details> 
<details>
<summary><b>Task 5:</b>RISC-V Based Temperature Monitoring and Control System</summary>  
<br>
Objective: Create a temperature monitoring and control system that uses the VSDSquadron Mini to read temperature data from an analog sensor and control a fan or heater based on predefined temperature thresholds.

Components:

VSDSquadron Mini RISC-V development board for processing.
Temperature Sensor (e.g., LM35 or DHT11) for measuring the ambient temperature.
Relay or Transistor to control the fan or heater.
LED indicators to show system status (e.g., fan on/off, heating on/off).
Functionality:

The board reads the temperature data via the ADC (Analog to Digital Converter) pins (e.g., PD0-PD7, PA1, PA2, PC4).
Based on the temperature, the board will turn on/off a relay or a transistor connected to the fan/heater using PWM signals to regulate the system.
Display system status using onboard LEDs or a serial display for monitoring.
Optionally, add a communication interface (UART or SPI) to log data or send alerts to a connected system.
Steps:

Set up the board with PlatformIO and configure your development environment.
Interface the temperature sensor with the board's ADC input pins.
Write the code to read sensor values, process the temperature, and compare it to set thresholds.
Use the GPIO pins to control the relay/transistor that switches the fan or heater on/off.
Implement feedback using LEDs or a simple display to visualize the temperature status.
