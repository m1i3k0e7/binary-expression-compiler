# Binary Expression Compiler
Intro to Programming (II) mini project, implemented codegen function

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
