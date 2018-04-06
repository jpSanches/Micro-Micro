Para as questões 2 a 5, considere que as variáveis f, g, h, i e j são do tipo inteiro (16 bits na arquitetura do MSP430), e que o vetor A[] é do tipo inteiro. Estas variáveis estão armazenadas nos seguintes registradores:

f: R4

g: R5

h: R6

i: R7

j: R8

A: R9

Utilize os registradores R11, R12, R13, R14 e R15 para armazenar valores temporários.

1.Escreva os trechos de código assembly do MSP430 para:
(a) Somente setar o bit menos significativo de R5.
```
bis #BIT0, R5
```

(b) Somente setar dois bits de R6: o menos significativo e o segundo menos significativo.
```
bis #BIT0, R6
bis #BIT1, R6
```

(c) Somente zerar o terceiro bit menos significativo de R7.
```
bic #BIT0, R7
```

(d) Somente zerar o terceiro e o quarto bits menos significativo de R8.
```
bic #BIT2, R8
bic #BIT3, R8
```


(e) Somente inverter o bit mais significativo de R9.
```
xor.w #BIT7, R9
```

(f) Inverter o nibble mais significativo de R10, e setar o nibble menos significativo de R10.
```
xor.w #0xF000, R10
bis #BIT0, R10
```


2."Traduza" o seguinte trecho de código em C para o assembly do MSP430:

```C
if(i>j) f = g+h+10;
else f = g-h-10;
```

```
cmp R7, R6		; i < j     R6 = i, R7 = j
jl IF
jmp ELSE
IF:		mov.w R5, R4	; f = R4 g = R5
add.w R6, R4 	; h = R6
add.w #10, R4
ELSE:	mov.w R5, R4	; f = R4 g = R5
sup.w R6, R4 	; h = R6
sup.w #10, R4
```


3."Traduza" o seguinte trecho de código em C para o assembly do MSP430:
``` C
while(save[i]!=k) i++;
```
```
		mov.w R15, R13 ;  save[0] = R15
WHILE: 	
		cmp 0(R13), R14		; k = R14
		jeq EXIT
		incd.w R13
		inc.w R7
		jmp WHILE
EXIT:	...
```
4."Traduza" o seguinte trecho de código em C para o assembly do MSP430:
for(i=0; i<100; i++) A[i] = i*2;
```
		mov.w #0, R7 ;	R7 = i - 0
		mov.w R9, R15;  R9 = &A[0]
FOR:
		cmp #100,R7
		jge EXIT
		mov.w R7, 0(R15) ; A[i] = i
		rla.w 0(R15) ; A[i] = 2i
		incd.w R15
		inc R7
		jmp FOR
EXIT: 	
		...		
```
5."Traduza" o seguinte trecho de código em C para o assembly do MSP430:
for(i=99; i>=0; i--) A[i] = i*2;
```
		mov.w #99, R7
		mov.w R9, R15
FOR:
		cmp #0, R7
		jl EXIT
		mov.w R7, 0(R15)
		rla.w 0(R15)
		incd.w R15
		dec.w R7
		jmp FOR
EXIT:
		...

```
