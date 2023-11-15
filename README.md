# Binary Expression Compiler
Intro to Programming (II) mini project ([Reference](https://github.com/lightbulb12294/CSI2P-II-Mini/tree/master)), implemented codegen function

# Example
input
```
z =(x++) + (y--);
```

output
```
load r0 [0]
load r1 [0]
add r1 r1 1
store [0] r1
load r1 [4]
load r2 [4]
sub r2 r2 1
store [4] r2
add r0 r0 r1
store [8] r0
```

## Input

The input will contain several binary expressions consisting of integers, operators, parentheses, and three variables `x`, `y`, and `z`.

The following operators will appear in this project:

- `+`, `-`, `*`, `/`, `%`
- `=`
- `++`, `--` (including prefix and suffix, such as `x++`, `--y`, ... and so on)
- `+`, `-` (expressions such as `+x`, `-y`, ... and so on)
- others such as `>>`, `+=`, are unavailable and will not appear.

## Output

The output is a list of assembly codes. The instruction set architecture are listed in the table below.

If the input expressions contains illegal expression, you should handle it with the error handler. For the details, please refer to [**Error Handler**](#error-handler) below.

## Instruction Set Architecture

### Memory Operation

| Opcode | Operand1 | Operand2 | Meaning                                                    | Cycles |
| ------ | -------- | -------- | ---------------------------------------------------------- | ------ |
| load   | `reg`    | `[Addr]` | Load data in memory `[Addr]` and save into register `reg`. | 200    |
| store  | `[Addr]` | `reg`    | Store the data of register `reg` into memory `[Addr]`.     | 200    |

### Arithmetic Operation

| Opcode | Operand1 | Operand2 | Operand3 | Meaning                                          | Cycles |
| ------ | -------- | -------- | -------- | ------------------------------------------------ | ------ |
| add    | `rd`     | `rs1`    | `rs2`    | Perform `rs1+rs2` and save the result into `rd`. | 10     |
| sub    | `rd`     | `rs1`    | `rs2`    | Perform `rs1-rs2` and save the result into `rd`. | 10     |
| mul    | `rd`     | `rs1`    | `rs2`    | Perform `rs1*rs2` and save the result into `rd`. | 30     |
| div    | `rd`     | `rs1`    | `rs2`    | Perform `rs1/rs2` and save the result into `rd`. | 50     |
| rem    | `rd`     | `rs1`    | `rs2`    | Perform `rs1%rs2` and save the result into `rd`. | 60     |

- Note that both `rs1` and `rs2` can be a register or a **non-negative integer**. However, `rd` must be a valid register.
- All operands should be separated by spaces.
- Using the first 8 registers has no penalty. However, using other registers would double the instruction cycle.
  - For example, `add r0 r1 r7` cost 10 cycles, while `add r8 r0 r23` cost 20 cycles.

## Identifiers

- The initial value of variables `x`, `y`, and `z` are stored in memory `[0]`, `[4]`, and `[8]` respectively. Before you use them, you have to load them into registers first.
- After the evaluation of the assembly code, the answer of the variables `x`, `y`, and `z` has to be stored in memory `[0]`, `[4]`, and `[8]` respectively.

## Grammar

Expression grammar for mini project 1.

Start with "statement".

Note that this only checks syntactical error such as "x++++y". However, semantic error like "5++" or "1=2+3" will pass the grammar.

```
tokens:
    END:        ";"
    ASSIGN:     "="
    ADD:        "+"
    SUB:        "-"
    MUL:        "*"
    DIV:        "/"
    REM:        "%"
    PREINC:     "++"
    PREDEC:     "--"
    POSTINC:    "++"
    POSTDEC:    "--"
    PLUS:       "+"
    MINUS:      "-"
    IDENTIFIER: xyz
    CONSTANT:   123
    LPAR:       "("
    RPAR:       ")"

STMT
    → END
    | EXPR END
    ;
EXPR
    → ASSIGN_EXPR
    ;
ASSIGN_EXPR
    → ADD_EXPR
    | UNARY_EXPR ASSIGN ASSIGN_EXPR
    ;
ADD_EXPR
    → MUL_EXPR
    | ADD_EXPR ADD MUL_EXPR
    | ADD_EXPR SUB MUL_EXPR
    ;
MUL_EXPR
    → UNARY_EXPR
    | MUL_EXPR MUL UNARY_EXPR
    | MUL_EXPR DIV UNARY_EXPR
    | MUL_EXPR REM UNARY_EXPR
    ;
UNARY_EXPR
    → POSTFIX_EXPR
    | PREINC UNARY_EXPR
    | PREDEC UNARY_EXPR
    | PLUS UNARY_EXPR
    | MINUS UNARY_EXPR
    ;
POSTFIX_EXPR
    → PRI_EXPR
    | POSTFIX_EXPR POSTINC
    | POSTFIX_EXPR POSTDEC
    ;
PRI_EXPR
    → IDENTIFIER
    | CONSTANT
    | LPAR EXPR RPAR
    ;
```

## Error Handler

The expression we designed is a subset of C expression statement. That is:

- If this expression cannot be compiled by GCC, it's an illegal expression.
- Our expression cannot be split into multiple lines, and there must be a `';'` at the end of an instruction.

Illegal expressions such as:

- ```
  x = 5++;
  ```
- ```
  y = (((7/3);
  ```
- ```
  z = ++(y++);
  ```
- ```
  x = y 
    + 3;
  ```
- and all expressions that cannot pass GCC compilers should be handled by error handler.

When an error occurs, no matter how much your assembly has outputted, your output **must contain `Compile Error!` with newline**.

**Note that in our testcases, there won't be any undefined behavior expression.** Such as:

- 1/0 (divide by 0)
- x = x++ (a variable updated twice or more in a single expression)
- 2147483647+1 (signed overflow)
- You may check if an expression is undefined behavior by compiling a C program with `-Wall` flag. If it is, there should be some warnings that shows the word "undefined", or refer to this [site](https://en.cppreference.com/w/cpp/language/ub).
