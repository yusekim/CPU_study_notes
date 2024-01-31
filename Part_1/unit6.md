# Unit 6

### Unit 6.1
Assembly Languages and Assemblers

- The "Assembler" is a softare
- First software layer above the hardware

#### Basic Assembler Logic
Repeat:
- Read the next Assembly language command
	*Ignore whitespace, read line into an array of characters*
- Break it into the different fields it is composed of
	*Split and break the original string into substrings*
- Lookup the binary code for each field
- Combine these codes into a single machine language command
- Output this machine language command

**Until end-of-file reached**
___
#### Symbols
- Lables `JMP loop`
- Variables `Load R1, weight`

**Assembler must replace names with addresses**

`Load R1, weight`

|Symbol|Address|
|---|---|
|weight|5782|
|...|...|

<br>

___
#### 어셈블러가 변수를 만났을때
1. 먼저 변수 테이블에 해당 변수명이 있는지 확인한다.
2. 있으면 해당 변수의 주소 사용, 없을 시 새 공간을 할당해야한다.
3. 새 공간을 할당하기 위해 메모리 상 빈 공간을 찾는다
4. 공간을 찾으면 해당 공간과 변수명을 테이블에 기록한다.

#### Forward references

Possible Solutions:
- Leave blank until label appears, then fix
- in first pass just figure out all address

### Unit 6.2
The Hack Assembly Language: A Translator's Perspective

#### the translator's challenge
Hack assembly code `--(Assembler)->` HAck binary code(target language)

*We need to know its syntax!*

#### A-instruction

Symbolic syntax:
|Symbolic syntax|Example|
|---|---|
|@*value*|@21|

**Where value is either**
- A non-negative decimal constant
- A symbol referring to such a constant

|Binary syntax|Example|
|---|---|
|0*valueInBinary*|0000000000010101|

<br>

#### C-instruction


#### Symbols
Label declaration; (*label*)
Variable declaration: @*variableName*

#### Translator's perspective

Assembly program elements:
- White space
	- empty lines / indentions
	- Line comments
	- In-line comments
- Instructions
	- A-instructions
	- C-instructions

- Symbols
	- References
	- Label declarations

### Unit 6.3
Handling Instructions

#### Translating A-instructions
- If a *value* is a decimal constant, generate the equivalent 15-bit binary constant
- If *value* is a symbol, later..

#### Translating C-instructions
Symbolic syntax: `dest = comp ; jump`
Binary syntax: `1 1 1 a c1 c2 c3 c4 c5 c6 d1 d2 d3 j1 j2 j3`

Example: `MD=D+1`
Binary: `111(FIXED) 0011111(COMP) 011(DEST) 000(JUMP)`

#### Overall
- Parse the instruction: break it into its underlying fields
- A-instruction: translate the decimal value into a binary value
- C-instruction: for each field in the instruction, generate the corresponding binary code; Assemble the translated binary codes into a complete 16-bit machine instruciton
- Write the 16-bit instruction to the output file

### Unit 6.4
Handling symbols

- Variable symbols: represent memory locations where the programmer wants to maintain values
- Lable symbols: represent destinations of goto instructions
- Pre-defined symbols: represent special memory locations

#### pre-defined symbols
Hack language specification describes *23 pre-defined symbols*: just replace symbol with its matching value

#### Label symbols
- Used to label destinations of goto commands
- Declared by the pseudo-command(XXX)
- This directive defines the symbol XXX to refer to the memory location holding the next instruction in the program

Example:
```C
// Computes RAM[1] = 1 + ... + RAM[0]
	@i
	M=1		// i = 1
	@sum
	M=0		// sum = 0

(LOOP)
	@i		// if i>RAM[0] goto STOP
	D=M
	@R0
	D=D-M
	@STOP
	D;JGT
	@i		// sum += i
	D=M
	@sum
	M=D+M
	@i		// i++
	M=M+1
	@LOOP	// goto LOOP
	0;JMP
(STOP)
	@sum
	D=M
	@R1
	M=D		// RAM[1] = the sum
(END)
	@END
	0;JMP
```

|Symbol|value|
|---|---|
|LOOP|4|
|STOP|18|
|END|22|

Translating @*labelSymbol*: Replace *labelSymbol* with its value

#### Variable symbols
- Any symbol xxx appering in an assembly program which is not pre-defined and is not defined elsewhere using the (XXX) directive is treated as a variable
- Each variable is assigned a unique memory address, starting at 16

|Symbol|value|
|---|---|
|i|16|
|sum|17|

Translating @*variableSymbol*:
- If you see it for the first time, assign a unique memory address
- Replace *variableSymbol* with its value

#### Symbol table
Simple/powerful structure that enables you to store and use symbol value pairs.


