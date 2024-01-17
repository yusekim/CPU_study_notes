# Unit 4


### Unit 4.5
**I/O devices are used for**
	- Getting data from users
	- Disdplaying data to users

**High-level approach**
	- Sophisticated(세련되다) software libraries enabling text, graphics, animation, audio..

**Low-level approach**
	- Bits

**Screen memory map**
스크린은 가로 512비트 세로 256비트의 흑백 디스플레이다.
A designated memory area, dedicated to manage a display unit
The physical display is continuously refreshed from the memory map, many times per second
Output is effected by writing code that manipulates the screen memory map.
memory map(Sequence of 16-bit values, can be array or matrix), altogether, 8k, 16-bit words(하나의 메모리 주소에 있는 데이터의 용량/단위)
비트 별 픽셀 하나씩 매핑되어있다. (비트가 1이면 픽셀 on, 0이면 픽셀 off)
하지만 메모리에 접근할때 한번에 16비트를 받는다(비트 하나만 가져올 수 없음!)
- 첫 32 words(32 * 16 = 512 bits)가 스크린의 첫 번째 열이 된다.
- 다음 32개의 words는 스크린의 다음 열을 표현하고,
- 마지막 열까지 매핑이 된다!

**To set pixel (row, col) on/off**
	1. word = Screen[32 * row + col / 16] -> 수정할 픽셀이 위치해있는 word의 메모리 주소를 가져온다
	2. set the (col % 16)th bit of word to 0 or 1 -> 픽셀을 특정하고 값을 수정한다.
	3. Commit word to the RAM -> 메모리에 변경사항 적용하기

**Keyboard memory map**
키보드를 표현하려면 16비트로 충분하다..!
Keyboard로 불리는 16비트 레지스터가 있다.
When a key is pressed on the keyboard, the key's *scan code* appears in the *keyboard memory map* (eg: Scan-code of 'k' = 75)

**To check which key is currently pressed**
	- probe the contents of the keyboard chip
	- In the Hack computer: probe the contents of RAM[24576]

### Unit 4.6
Low-level programming

**Hack assembly instructions**
A-instruction: where value is either a constant or a symbol referring to such a constant -> 변수 선언같은 느낌적인 느낌
> @value // A = value

C-instruction
> dest = comp ; jump
comp -> 연산
dest -> 저장(메모리 관련)
jump -> 코드의 다른 부분으로 이동

Semantics
	- Computes the value of comp
	- Stores the result in dest;
	- If the Boolean expression (comp jump 0) is true, jumps to execcute the instruction stored in ROM[A]

**Working with registers and memory**
D: data register -> 16비트 값을 저장할 수 있다
A: address / data register -> 데이터 값 또는 주솟값을 가질 수 있다.
M: the currently selected memory register, M = RAM[A] -> 현재 선택된 메모리 레지스터, A레지스터에 특정 값을 로드할 시, RAM의 특정한 레지스터를 가리키고 있다.
(D, A 레지스터는 CPU안에 위치해있다.)

Examples
```
//D=10
	@10
	D=A

//D++
	D=D+1

//D=RAM[17]
	@17
	D=M

//RAM[17]=10
	@10
	D=A
	@17
	M=D

//RAM[5]=RAM[3]
	@3
	D=M
	@5
	M=D
```

**Built-in symbols**
R(number) will be translated to (number)
>@R5 is same to @5
SCREEN -> 16384
KBD -> 24576

### Unit 4.7

- **Branching**
```// Program: Signum.asm
// Computes: if R0>0
//		R1=1
//	     else
//		R1=0

	@R0
	D=M

	@POSITIVE // using a label
	D;JGT

	@R1
	M=0
	@END
	0;JMP

(POSITIVE)
	@R1
	M=1

(END)
	@END
	0;JMP
```
- **Variables**
```
// Program: Flip.asm
// flips the values of
// RAM[0] and RAM[1]
// temp = R1
// R1 = R0
// R0 = temp

	@R1
	D=M
	@temp
	M=D		//temp = R1

	@R0
	D=M
	@R1
	M=D		//R1 = R0

	@temp
	D=M
	@R0
	M=D		//R0 = temp

	(END)
	@END
	0;JMP
```
**@temp**: "find some available memory register (say register n), and use it to represent the variable temp. So, from now on, each occurance of @temp in the program will be translated to @n

- **Iteration**
For example, to compute 1 + 2 + ... + n

```
"Pseudo code"
// Computes RAM[1] = 1+2+...+RAM[0]<br>
	n = R0
	i = 1
	sum = 0
LOOP:
	if i > n goto STOP
	sum = sum + i
	i = i + 1
	goto LOOP
STOP:
	R1 = sum
```
```
"Hack assembly code"
// Program: Sum1toN.asm
// Computes RAM[1] = 1+2+...+n
// Usage: Put a number (n) in RAM[0]

	@R0
	D=M
	@n
	M=D	// n = R0
	@i
	M=1	// i = 1
	@sum
	M=0	// sum = 0

(LOOP)
	@i
	D=M
	@n
	D=D - M
	@STOP
	D;JGT	// if i > n goto STOP

	@sum
	D=M
	@i
	D=D+M
	@sum
	M=D	// sum = sum + i
	@i
	M=M+1	// i = i + 1
	@LOOP
	0;JMP

(STOP)
	@sum
	D=M
	@R1
	M=D	// RAM[1] = sum

(END)
	@END
	0;JMP
```

### Unit 4.8

- **Pointers**
```
Example:
// for (i=0; i<n; i++){
//	arr[i] = -1;
//	}
	// Suppose that arr=100 and n=10

	// arr = 100
	@100
	D=A
	@arr
	M=D

	// n=10
	@10
	D=A
	@n
	M=D

	// initialize i = 0
	@i
	M=0

(LOOP)
	// if (i==n) goto END
	@i
	D=M
	@n
	D=D-M
	@END
	D;JEQ

	// RAM[arr+i] = -1
	@arr
	D=M
	@i
	A=D+M
	M=-1

	// i++
	@i
	M=M+1

	@LOOP
	0;JMP

(END)
	@END
	0;JMP

```

- **Input/Output**

**SCREEN**
```
Rectangle drawing: Pseudo code
//	for (i=0; i<n; i++){
//	draw 16 black pixels at the
//	beginning of row i
//	}

addr = SCREEN
n = RAM[0]
i = 0

LOOP:
	if i > n goto END
	RAM[addr] = -1	// 1111111111111111
	// advances to the next row
	addr = addr + 32
	i = i + 1
	goto LOOP

END:
	goto END
```

```
// Program: Rectangle.asm
// Draws a filled rectangle at the
// screen's top left corner, with
// width of 16 pixels and height of
// RAM[0] pixels.
// Usage: put a non-negative number
	(rectangle's height) in RAM[0]

	@SCREEN
	D=A
	@addr
	M=D	// addr = 16384
		// screen's base address
	@0
	D=M
	@n
	M=D	// n = RAM[0]

	@i
	M=0	// i = 0

(LOOP)
	@i
	D=M
	@n
	D=D-M
	@END
	D;JGT // if i > n (i - n 이 0 보다 클 시) goto END

	@addr
	A=M
	M=-1	// RAM[addr]=1111111111111111

	@i
	M=M+1	// i = i + 1
	@32
	D=A
	@addr
	M=D+M	// addr = addr + 32
	@LOOP
	0;JMP	// goto LOOP

(END)
	@END	// program's end
	0;JMP	// infinite loop
```

**KEYBOARD**
키보드의 base address에 해당하는 레지스터를 확인하면 된다..
