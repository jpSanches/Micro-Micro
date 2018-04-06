Para cada questão, escreva funções em C e/ou sub-rotinas na linguagem Assembly do MSP430. Reaproveite funções e sub-rotinas de uma questão em outra, se assim desejar. Leve em consideração que as sub-rotinas são utilizadas em um código maior, portanto utilize adequadamente os registradores R4 a R11. As instruções da linguagem Assembly do MSP430 se encontram ao final deste texto.

1. (a) Escreva uma função em C que calcule a raiz quadrada `x` de uma variável `S` do tipo int, utilizando o seguinte algoritmo: após `n+1` iterações, a raiz quadrada de `S` é dada por

```
x(n+1) = (x(n) + S/x(n))/2
```

O protótipo da função é:

```C
unsigned int Raiz_Quadrada(unsigned int S);
```
```C
unsigned int Raiz_Quadrada(unsigned int S)
{
	int counter, x = 1; // x é o valor inicial para o problema
    if (S < 2)
    	return s;
    else
    {
        for(counter = 0; counter < 10; counter ++)
            x = (x + S/x)/2;
        return x;
    }
}
```
(b) Escreva a sub-rotina equivalente na linguagem Assembly do MSP430. A variável `S` é fornecida pelo registrador R15, e a raiz quadrada de `S` (ou seja, a variável `x`) é fornecida pelo registrador R15 também.
```assembly
Raiz_Quadrada: cmp #2,R15 ; compara se o valor de x**2 é 1 ou 0, evitando calculos
jge Raiz_Nao_Trivial
ret
Raiz_Nao_Trivial: push R5 ; guarda o valor de R5 na pilha
push R6 ; guarda R6 na pilha
mov R15, R5 ; R5 = s
inc R5 ; s+=1
rra R5 ; s = (s+1)/2
clr R6 ; limpa R6
Raiz_Loop: cmp #10000,R6 ; verifica se n é menor do que 10**4
jeq Raiz_End
push R15 ; s para a pilha
mov R5,R14 ; x para o denominador na rotina de div
call Div ; implementar a sub-rotina de divisao
add R15,R5 ; adiciona o resultado da div com x
rra R5 ; divide por 2
pop R15 ; recupera s
inc R6 ; ++ no contador
jump Raiz_Loop
Raiz_End: mov R5,R15
pop R6
pop R5
ret

Div:
    clr R13 ; R13 = k = 0
    clr R12 ; R12 = q
Mult_Loop:
    add.w R14, R13 ; k+= x
    inc.w R12 ; q++
    cmp R13,R15
    jge Mult_Loop ; se S >= k.x, permanece no loop
End_Mult_Loop:
    dec.w R12 ; q--
    mov.w R12, R15 ; retorna q
    ret
```
2. (a) Escreva uma função em C que calcule `x` elevado à `N`-ésima potência, seguindo o seguinte protótipo:

```C
int Potencia(int x, int N);
```
```C
int Potencia(int x, int N){
    int pot = x;
    if (N = 0) return 1;
    while (N != 1){
        pot = pot * x;
        N--;
    }
    return pot;
}

```

(b) Escreva a sub-rotina equivalente na linguagem Assembly do MSP430. `x` e `n` são fornecidos através dos registradores R15 e R14, respectivamente, e a saída deverá ser fornecida no registrador R15.


```assembly

Potencia:
        cmp R14, #0
        jne Potencia_nao_trivial
        mov.w #1, R15 ; se n = 0, retorna 1
        ret
Potencia_nao_trivial:
        push R6 ; guarda R6 na pilha
        mov.w R14, R6 ; r6 = r14 = n

Loop:
        cmp R6, #1 ; sai do Loop se n = 1
        jeq Loop_Exit
        call Mult ; multiplica R15 por x
        dec.w R6 ; n--
        jmp Loop
Loop_Exit:
        pop R6 ; tira R6 da pilha
        ret

Mult:   
        mov.w R15, R14
        mov.w R15, R13
        clr.w R15
Mult_Loop:
        add.w R13, R15
        dec.w R14
        cmp R14, #0 ; se R14 > 0, fica no loop
        jl Mult_Loop
Mult_Exit:
        ret

```

3. Escreva uma sub-rotina na linguagem Assembly do MSP430 que calcula a divisão de `a` por `b`, onde `a`, `b` e o valor de saída são inteiros de 16 bits. `a` e `b` são fornecidos através dos registradores R15 e R14, respectivamente, e a saída deverá ser fornecida através do registrador R15.

```assembly
Div:
    clr R13 ; R13 = k = 0
    clr R12 ; R12 = q
Mult_Loop:
    add.w R14, R13 ; k+= x
    inc.w R12 ; q++
    cmp R13,R15
    jge Mult_Loop ; se S >= k.x, permanece no loop
End_Mult_Loop:
    dec.w R12 ; q--
    mov.w R12, R15 ; retorna q
    ret

```

4. Escreva uma sub-rotina na linguagem Assembly do MSP430 que calcula o resto da divisão de `a` por `b`, onde `a`, `b` e o valor de saída são inteiros de 16 bits. `a` e `b` são fornecidos através dos registradores R15 e R14, respectivamente, e a saída deverá ser fornecida através do registrador R15.

```assembly
Resto_Div:
    clr R13 ; R13 = k = 0
    clr R12 ; R12 = q
Mult_Loop:
    add.w R14, R13 ; k+= b
    inc.w R12 ; q++
    cmp R13,R15
    jge Mult_Loop ; se a >= k.b, permanece no loop
End_Mult_Loop:
    sub.w R13, R15
    add.w R14, R15
    ret
```
5. (a) Escreva uma função em C que indica a primalidade de uma variável inteira sem sinal, retornando o valor 1 se o número for primo, e 0, caso contrário. Siga o seguinte protótipo:

```C
int Primalidade(unsigned int x);
```
```C
int Primalidade(unsigned int x){
    unsigned int teste = x - 1;

    if (x < 2)
        return 0; // x não é primo

    while(x % teste != 0)
        teste--;

    if (teste = 1)
        return 1; // x é primo
    return 0; // x não é primo

}
```

(b) Escreva a sub-rotina equivalente na linguagem Assembly do MSP430. A variável de entrada é fornecida pelo registrador R15, e o valor de saída também.

6. Escreva uma função em C que calcula o duplo fatorial de n, representado por n!!. Se n for ímpar, n!! = 1*3*5*...* n, e se n for par, n!! = 2*4*6*...* n. Por exemplo, 9!! = 1*3*5*7*9 = 945 e 10!! = 2*4*6*8*10 = 3840. Além disso, 0!! = 1!! = 1.
O protótipo da função é:

```C
unsigned long long DuploFatorial(unsigned long long n);
```

7. (a) Escreva uma função em C que calcula a função exponencial utilizando a série de Taylor da mesma. Considere o cálculo até o termo n = 20. O protótipo da função é `double ExpTaylor(double x);`
(b) Escreva a sub-rotina equivalente na linguagem Assembly do MSP430, mas considere que os valores de entrada e de saída são inteiros de 16 bits. A variável de entrada é fornecida pelo registrador R15, e o valor de saída também.

8. Escreva uma sub-rotina na linguagem Assembly do MSP430 que indica se um vetor esta ordenado de forma decrescente. Por exemplo:
[5 4 3 2 1] e [90 23 20 10] estão ordenados de forma decrescente.
[1 2 3 4 5] e [1 2 3 2] não estão.
O primeiro endereço do vetor é fornecido pelo registrador R15, e o tamanho do vetor é fornecido pelo registrador R14. A saída deverá ser fornecida no registrador R15, valendo 1 quando o vetor estiver ordenado de forma decrescente, e valendo 0 em caso contrário.

9. Escreva uma sub-rotina na linguagem Assembly do MSP430 que calcula o produto escalar de dois vetores, `a` e `b`. O primeiro endereço do vetor `a` deverá ser passado através do registrador R15, o primeiro endereço do vetor `b` deverá ser passado através do registrador R14, e o tamanho do vetor deverá ser passado pelo registrador R13. A saída deverá ser fornecida no registrador R15.

10. (a) Escreva uma função em C que indica se um vetor é palíndromo. Por exemplo:
	[1 2 3 2 1] e [0 10 20 20 10 0] são palíndromos.
	[5 4 3 2 1] e [1 2 3 2] não são.
Se o vetor for palíndromo, retorne o valor 1. Caso contrário, retorne o valor 0. O protótipo da função é:

```C
int Palindromo(int vetor[ ], int tamanho);
```

(b) Escreva a sub-rotina equivalente na linguagem Assembly do MSP430. O endereço do vetor de entrada é dado pelo registrador R15, o tamanho do vetor é dado pelo registrador R14, e o resultado é dado pelo registrador R15.
