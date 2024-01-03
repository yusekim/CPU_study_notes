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
> //D=10
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

**Built-in symbols**
R(number) will be translated to (number)
>@R5 is same to @5
SCREEN -> 16384
KBD -> 24576
