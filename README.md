# EE113 4 bits PROCESSOR
Reference design of  EE113's final project  (Digital integrated Circuit design) 
## Project Description 

An processor mainly has **5 parts :**  **Program Counter(PC), Control Unit, Instruction Memory, Data Memory, Register File, Arithmetic Logic Unit(ALU).** For a standard processor, we can use assembly language to program it and get the result we want. But, for this project, we just realize a few of basic assembly instructions : 

 **1. ARITH: Operate ALU(Contain: ADD, SUB, AND, XOR)** 

 **2. LOAD: Load data from memory to register** 

 **3. STORE: Store data from register to memory**  

 **4. BEQ: Compare two number and jump to a special instruction line according to the  compared result** 

 **5. JP: Jump to the special instruction line** 

This processor has 7 input ports : **clear signal, 4 bits instruction memory address, 16 bits instruction memory data, instruction memory write enable signal, 4 bits data memory address, 8 bits data memory data** and **data memory write enable signal.** The architecture of this processor is below: 

![图片](https://uploader.shimo.im/f/IvRFclJlMbZpswXi.png!thumbnail)

For this processor, it needs **some cycles** to write instructions to instruction memory, and write data to data memory. Then, we send **Clear** signal to the processor to clear the **register file** and start **Program Counter** to execute the instructions in the instruction memory. Execution flow: 

 **1. Write instructions to instruction memory(1 instruction per clock cycle)** 

 **2. Write data to data memory(4 bits per cycle)** 

 **3. Set Clear port to 1(Clear register file and start the processor)** 

 **4. Wait for the processor to finish execute instructions** 

 **5. Observe the result data in data memory** 

### Assembly instruction: 

All the legal instructions are 16 bits. For example: 

```c++
0000 000 001 010 000 
0011 010 011 000 110 
0001 001 000 000 001 
0010 001 000 000 010 
0100 000 000 000 110 
```
|**Operate code** |**Operate instruction** |
|:----|:----|
|0000 |ARITH (operate ALU) |
|0001 |LOAD |
|0010 |STORE |
|0011 |BEQ |
|0100 |JP |

If the operate instruction is **ARITH** . We need compute flag to indicate which operation should ALU do. 

```
0000 000 001 010 000 //(15:12) is operate code, (11:9) is register A address 
//(8:6) is register B address, (5:3) is register C address 
//and (2:0) is compute flag. 
//This code means: R2 = R0 + R1 
0000 RA_address RB_address RC_address Operation_flag 
//RC = RA Operation_flag RB 
```
|**ALU compute flag code** |**ALU compute sign** |**Operation** |
|:----|:----|:----|
|000 |ADD |RC = RA + RB |
|001 |SUB |RC = RA - RB |
|010 |AND |RC = RA & RB |
|011 |XOR |RC = RA xor RB |

If the operate instruction is **BEQ** or **JP** . We need **Instruction address** to indicate which line should it jump. 

```
0011 010 011 000 110 //(15:12) is operate code, (11:9) is register A address 
//(8:6) is register B address, (5:3) is 0 (unused) 
//and (2:0) is instruction memory address. 
//This code means, compare R2 and R3, if R2 == R3 then jump to 0x110 = 6 line. 
0011 RA_address RB_address 000 Instruction_memory_address 
//Compare RA and RB, if RA == RB then jump to Instruction_memory_address 
 
0100 000 000 000 110 //(15:12) is operate code, (11:3) is 0 (unused) 
//and (2:0) is instruction memory address. 
//This code means, just jump to 0x110 = 6 line. 
0100 000 000 000 Instruction_memory_address 
//Just jump to Instruction_memory_address 
```
If the operate instruction is **LOAD** or **STORE** . We need **Data memory address** to indicate which line should read or write. 
```c++
0001 001 000 000 001 //(15:12) is operate code, (11:9) is register A address 
//(8:3) is 0 (unused) and (2:0) is data memory address. 
//This code means, load data in data memory(address is 001) to RA 
0001 RA_address 000 000 Data_memory_address 
//Load Data_memory_address's data to RA 
 
0010 001 000 000 010 //(15:12) is operate code, (11:9) is register A address 
//(8:3) is 0 (unused) and (2:0) is data memory address. 
//This code means, store RA to data memory(address is 010) 
0010 RA_address 000 000 Data_memory_address 
//Store RA to Data_memory(address is Data_memory_address) 
```
## Basic requirement (40 points) 

Realize the basic **4 bits ALU** and **16x8 data SRAM** . 


 ![图片](https://uploader.shimo.im/f/BU73ILVN1s977MnQ.png!thumbnail) ![图片](https://uploader.shimo.im/f/LeRq5mUoqfM8KMpy.png!thumbnail)

You should connect **ALU's Data_out** port to **Data Memory's Data port** . 

### 1. For ALU: 

You must realize all the operations( **ADD, SUB, AND, XOR** ) above. And if **DataA_in** is  equal to **DataB_in** , the output port **Zero_flag** must be **1** . 

### 2. For Data Memory 

This memory is **Static Random Access Memory(SRAM)** . The **Address** port controls which data you can read or write. **Write enable** signal controls whether you can write data to memory(if **Write enable comes 1, then you can write, else you can just read** ). 

### 3. Top schematic 

In this project, you must have **5 input ports** and **2 output ports** . 

### Input ports: 

|**Port Name** |**Data length** |
|:----:|:----|
|DataA_in |4 bits |
|DataB_in |4 bits |
|Control_flag |3 bits |
|Address |4 bits |
|Write_enable |1 bit |

### Output ports: 

|**Port Name** |**Data length** |
|:----:|:----|
|Zero_flag |1 bit |
|Data_out |4 bits |

### 4. Execution flow 


1. Use ALU to compute the result and store them to data memory 
2. Observe whether the result in data memory correct 

![图片](https://uploader.shimo.im/f/93vJWnX36k0c7edf.png!thumbnail)

## Bonus Task 

***You must complete the basic project at first, then the bonus!*** 

### Bonus 1 (5 points) 

Add 4bits signed Multiplication to your ALU. 

|Instructions |Code |Operation |
|:----|:----|:----|
|MUL |0000 RA_address RB_address RC_address 100 |RC = RA * RB |

### Bonus 2    (5 points) 

Add 8 bytes **Register File** to **Basic Project** . 

![图片](https://uploader.shimo.im/f/ZVOAKRLedFaamxHP.png!thumbnail)

Implement **2 additional instructions (LOAD, STORE)** . 

### Input ports: 

|**Port Name** |**Data length** |
|:----:|:----|
|Ra_addr |3 bits |
|Rb_addr |3 bits |
|Data_addr |3 bits |
|Reg_write_enable |1 bit |
|Reg_clear |1 bit |
|ALU_control_flag |3 bits |
|Address |4 bits |
|Data |8 bits |
|Data_memory_write_enable |1 bit |

### Output ports: 

|**Port Name** |**Data length** |
|:----:|:----|
|Zero_flag |1 bit |

### Execution flow 


1. Write data to **data memory** 
2. Load some data to **Register file** 
3. Use **ALU** to compute the result (for example: R2 = R0 + R1) 
4. Store the data in **Register file** to **Data memory** 
5. Observe the data in **Data memory** 

![图片](https://uploader.shimo.im/f/12TJwrHISh8Boh0L.png!thumbnail)

### Bonus 3    (15 points) 

Finish this 4 bits processor. 

|**Instructions** |**Code example** |
|:----:|:----|
|LOAD |0001 RA_address 000 000 Data_memory_address |
|STORE |0010 RA_address 000 000 Data_memory_address |
|BEQ |0011 RA_address RB_address 000 Instruction_memory_address |
|JP |0100 000 000 000 Instruction_memory_address |
|ARITH |0000 RA_address RB_address RC_address Operation_flag |

|**ALU compute flag code** |**ALU compute sign** |**Operation** |
|:----:|:----|:----:|
|000 |ADD |RC = RA + RB |
|001 |SUB |RC = RA - RB |
|010 |AND |RC = RA & RB |
|011 |XOR |RC = RA xor RB |

### Execution flow: 


1. Write instructions to **instruction memory** (1 instruction per clock cycle) 

1. Write data to **data memory** (4 bits per cycle) 

1. Set **Clear** port to 1(Clear register file and start the processor) 

1. Wait for the processor to finish execute instructions 

1. Observe the result data in **data memory** 
# Constraints (PLEASE READ CAREFULLY!) 


* Supply voltage: 

Find optimal VDD (0~2V) that minimizes clock period and energy 


* Implementation choices: 

Use only static logic (CMOS, pass-transistor logic). 


* ALU operation: 

The Input signal is two 4-bit **signed** numbers represented in 2’s complement format, the output should also be a **signed** number. 


* Layout: 

You can use up to **5 metal layers** . 

Aspect ratio of the bounding box (long / short side) should be less than **1.5** . 

* Evaluation: 

For bonus part, if you only compete schemetic without layout, you can achieve up to 40% points of each part. 

**Phase 1:  Learning about Processor                                                                      (** **<** **1 week)** 

a)  Read the reference book we provide or other materials to know function of each part of processor , you should  view the P1-P7 in video resources we provided to you, even you decide to only implement the basic task. 

b) Decide what function you want to implement. 

**Phase 2:  Choosing Topology / Circuit Style                                                         (1 week)** 

a) Design the logic diagram which can implement the tiny processor. 

b) Choose logic style for the implementation. You may use any logic family you learnt from EE113. 

c) Implement the schematic view of the tiny processor in Cadence. 

**Phase 3: Critical Path Delay Optimization                                                             (** **<** **1 week)** 

a) Check functionality of your design in Spectre. 

b) Identify input vectors that will exercise critical path, and estimate its logical effort. 

c) Size the gates for minimum delay (hand analysis). 

d) Verify the critical path delay in Spectre under worst-case. 

**Phase 4: Layout and Verification in Spectre                                                         (** **1** **-** **2** **weeks)** 

a) Create layout of the design and make sure it passes DRC and LVS. 

b) Extract post-layout netlist and verify critical path in Spectre. 

c) Submit pre-layout and post-layout netlists. We will run LVS on your design, and you should find the maximum frequency your CPU can run if you implement the bonus part. 

**Phase 4: Add more functions of your processor** **** **(** **1** **-** **2** **weeks)** 

a) If you have extra time, you can add more functions to your design to realize a more complete processor. 

# Useful materials: 


1. For SRAM part:  Read 12.1 and 12.2 (CMOS VLSI Design A Circuits and Systems Perspective by Weste and Harris) carefully, it even gives the layout of SRAM and other circuit you may need in your project. 
2. For  CPU design (bonus 2): 

    * Since the instruction of this project is MIPS-liked, if you don't know MIPS instructions， we suggest you to read 2.1-2.7  (Computer Organization and Design: The hardware/software interface MIPS edition) 
    * To design the architecture of your single cycle CPU, refer to 4.1-4.4  (Computer Organization and Design: The hardware/software interface MIPS edition) 
    * To design a piplined architecture, refer to 4.5-4.8  (Computer Organization and Design: The hardware/software interface MIPS edition) 

1. Some video resource : 

    * [北京大学 《计算机组成》](https://www.bilibili.com/video/BV1VE411o7nx?from=search&seid=3865202558327119602)these videos introduce basic MIPS instructions and computer architecture,  view P1->P7 ( **Recommoned** , even you only want to implement basic task), P27 -> P32( **Optional** , for bonus 2 basic single cycle CPU design), P32->P38 ( **Optional** , for bonus 4 piplined architecture) 
    * [SRAM CELL](https://www.youtube.com/watch?v=Bpr-QQr2f-w), a precise introduction of SRAM arrays. 



