# Unit 5

### Unit 5.1
Von Neumann Architecture

Elements
- ALU
- Registers

Information Flows
- The Arithmetic Logic Unit: 데이터를 받고 연산 후 리턴. 컨트롤 버스와도 연결되어야 한다.
- Data Registers: 데이터를 주고 받고 해야한다. 레지스터 중에는 주소를 받는 레지스터도 있기 때문에 주소 버스에도 연결되어야 한다.
- Memory: 데이터를 주고받고 주소값에 대한 정보도 접근할 수 있어야 한다. 프로그램 메모리의 경우에는 컨트롤 버스에도 연결되어야 한다.

### Unit 5.2
The Fetch-Execute Cycle

The basic CPU loop
- *Fetch* an instruction from the program memory
- *Execute* it

**Fetching**
- Put the location of the next instruction into the "address" of the program memory
- Get the instruction code itself by reading the memory contents at that location
- Program Counter(PC) need to be manipulated in order to jump to the next instruction

**Executing**
- The instruction code specifies "what to do"
	- Which arithmetic or logical instruction
	- What memory to access (read/write)
	- If/where to jump
- Often, different subsets of the bits control different aspects of the operation
- Executing the operation involves also accessing registers and/or data memory

**Fetch-Execute Clash**
하나의 메모리 주소에 다음 instruction 주소와 data정보를 받기 때문에 충돌이 일어날 가능성이 있다.
해결법은 순번을 매겨 순차적으로 처리하는 것이다
- fetch cycle 에서 멀티플렉서에서는 메모리의 주소 인풋으로 연결하고
- execute cycle 에서는 실제로 접근해야하는 데이터의 주소를 연결해준다

Simpler solution: Harvard architecture
- Variant of von Neumann Architecture
- Keep program and data in two separate memory modules
- Complication avoided

### Unit 5.3
Central Processing Unit

The Hack CPU, A 16-bit processor, designed to
- Execute the current instrcution
- Figure out which instruction to execute next
(instructions written in the Hack language)

**Hack CPU Interface**
CPU is connected both to the instruction memory and the data memory.
- **Inputs**
	- data value
	- instruction
	- reset bit

- **Outputs**
	- data value
	- write to memory? (yes/no)
	- memory address
	- address of next instruction 중요함.

**Hack CPU Implementation**

**Instruction handling**
A instruction:
- Decodes the instruction into op-code + 15-bit value
- Stores the value in th A-register
- Outputs the value

C instruction:
- The instruction bits are decoded into:
	- Op-code
	- ALU control bits
	- Destination load bits
	- Jump bits

ALU operation: outputs
data output:
	- Result of ALU calculation, fed simultaneously to D-reg, A-reg and M-reg
	- Which register *actually* received the incoming value is determined the instruction's destination bits

**Control**
Program Counter abstraction
Emits the address of the next instruction:
To start / reset the program's execution: PC = 0
- *no jump*: PC++
- *goto*: PC = A
- *conditional goto*: if the condition is true, PC = A else PC++

**PC logic**
```
if (reset==1) -> PC = 0
else
	//current instruction
	load = f(jump bits, ALU control outputs)
	if (load==1) PC = A
	else PC++
```

### Unit 5.4
The Hack Computer
The CPU executes the instruction according to the Hack language specification:
- If the instruction includes D and A, the respective values are read from, and/or written to, the CPU=resident D-register and A-register
- If the instruction is @x, then x is stored in the A-register, this value is emitted by addressM
- If the instruction's RHS includes M, this value is read from inM
- If the instruction's LHS includes M, then the ALU outputs is emitted by outM, and the writeM bit is asserted.

Memory
- address 0 ~ 16383: data memory
- address 16384 ~ 24575: screen memory map
- address 24576: keyboard memory map

