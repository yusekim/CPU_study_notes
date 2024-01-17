# Unit 5

### Unit 5.1
대부분의 프로그래머는 컴파일러는 기본적으로 자신에게 주어진것(granted)로 여긴다.
하지만 고급 프로그래밍언어를 기계어로 변환하는 과정은 간단하지 않다(not a magic).
Jack compiler
- Syntax analyzer(구문 분석기)
	1. tokenizer
	2. parser
	testing: project 10 (using XML code)
- Code generator(코드 생성기)
Methodolgy: extending the basic syntax analyzer and ading code generation capabilities

**Program compilation**
Each class file is compiled separately

**Compilation challenges**
- Handling variables
- Handling expresseions
- Handling flow of control
- Handling objects
- Handling arrays

### Unit 5.2
Handling variables
```c
//source code
sum = x * (1 + rate)
//after compiler

//VM code(pseudo)
push x
push 1
push rate
+
*
pop sum

//VM code(actual)
push argument 2
push constant 1
push static 0
add
call Math.multiply 2
pop local 3
```
In order to generate actual VM code, we mush know:
- Weather each variable is a *field, static, local, or argument*
- Weather each variable is the *first, second, third..* variable of its kind

**We have to handle:**
- class-level variables
	- field
	- static
- subroutine-level variables
	- argument
	- local

**Variable properties:**
Needed for code generation and can be managed efficiently using a **symbol table**
- name: identifier
- type: int, char, boolean, class name
- kind: field, static, local, argument
- scope: class level, subroutine level

**Symbol table**
```cpp
class Point {
	field int x, y;
	static int pointCount

	method int distance(Point other) {
		var int dx, dy;
		let dx = x - other.getx();
		let dy = y - other.gety();
		return Math.sqrt((dx*dx) + (dy*dy));
	}
}
```
Class Point의 멤버 변수들
|name|type|kind|#|
|---|---|---|---|
|x|int|field|0|
|y|int|field|1|
|pointCount|int|static|0|

함수 distance의 매개변수들(this의 경우는 함수 자체를 가리키는 것 같다..)
|name|type|kind|#|
|---|---|---|---|
|this|Point|argument|0|
|other|Point|argument|1|
|dx|int|local|0|
|dy|int|local|1|

**Class-level symbol table**: can be reset each time we start compiling a new class
**Subroutine-level symbol table**: can be reset each time we start compiling a new subroutine

*Handling variable declarations*: Add the variable and its properties to the symbol table
*Handling variable usage*: Look up the variable in the subroutine-level symbol table; if not found, look it up in the class-level symbol table.

- **Perspective**
Programming languages vary in terms of:
- variable types
- variable kinds
- nested scoping(중첩 범위)

The variable handling techniques that we showed can be easily generalized to handle the compilation of variables in any language

- Handling nested scoping
# Unit 5

### Unit 5.1
대부분의 프로그래머는 컴파일러는 기본적으로 자신에게 주어진것(granted)로 여긴다.
하지만 고급 프로그래밍언어를 기계어로 변환하는 과정은 간단하지 않다(not a magic).
Jack compiler
- Syntax analyzer(구문 분석기)
	1. tokenizer
	2. parser
	testing: project 10 (using XML code)
- Code generator(코드 생성기)
Methodolgy: extending the basic syntax analyzer and ading code generation capabilities

**Program compilation**
Each class file is compiled separately

**Compilation challenges**
- Handling variables
- Handling expresseions
- Handling flow of control
- Handling objects
- Handling arrays

### Unit 5.2
Handling variables
```c
//source code
sum = x * (1 + rate)
//after compiler

//VM code(pseudo)
push x
push 1
push rate
+
*
pop sum

//VM code(actual)
push argument 2
push constant 1
push static 0
add
call Math.multiply 2
pop local 3
```
In order to generate actual VM code, we mush know:
- Weather each variable is a *field, static, local, or argument*
- Weather each variable is the *first, second, third..* variable of its kind

**We have to handle:**
- class-level variables
	- field
	- static
- subroutine-level variables
	- argument
	- local

**Variable properties:**
Needed for code generation and can be managed efficiently using a **symbol table**
- name: identifier
- type: int, char, boolean, class name
- kind: field, static, local, argument
- scope: class level, subroutine level

**Symbol table**
```cpp
class Point {
	field int x, y;
	static int pointCount

	method int distance(Point other) {
		var int dx, dy;
		let dx = x - other.getx();
		let dy = y - other.gety();
		return Math.sqrt((dx*dx) + (dy*dy));
	}
}
```
Class Point의 멤버 변수들
|name|type|kind|#|
|---|---|---|---|
|x|int|field|0|
|y|int|field|1|
|pointCount|int|static|0|

함수 distance의 매개변수들(this의 경우는 함수 자체를 가리키는 것 같다..)
|name|type|kind|#|
|---|---|---|---|
|this|Point|argument|0|
|other|Point|argument|1|
|dx|int|local|0|
|dy|int|local|1|

**Class-level symbol table**: can be reset each time we start compiling a new class
**Subroutine-level symbol table**: can be reset each time we start compiling a new subroutine

*Handling variable declarations*: Add the variable and its properties to the symbol table
*Handling variable usage*: Look up the variable in the subroutine-level symbol table; if not found, look it up in the class-level symbol table.

**Perspective**
Programming languages vary in terms of:
- variable types
- variable kinds
- nested scoping(중첩 범위)

The variable handling techniques that we showed can be easily generalized to handle the compilation of variables in any language

**Handling nested scoping**
Can be managed using a linked list of symbol tables

### Unit 5.3
Handling Expressions
> Jack grammer
expression: term (op term)*
term: integerConstant | stringConstant | keywordConstant | varName | varName '[' expression ']' | subroutineCall | '(' expression ')' | unaryOp term
subroutineCall: subroutineName '(' expressionList ')' | ( className | varName)'.'subroutineName '(' expressionList ')'
expressionList: (expression (',' expression)* )?
op: '+'|'-'|'*'|'/'|'&'|'|'|'<'|'>'|'='
unaryOp: '-'|'~'
KeywordConstant: 'true'|'false'|'null'|'this'

**Parse tree**
infix/prefix expressions
|prefix|infix|postfix|
|---|---|---|
|`* a + b c`| `a * (b + c)`|`a b c + *`|
|functional|human oriented|stack oriented|
> stack language is also postfix!

because our souce language is infix and the VM language is postfix, we need to translate from infix to postfix
