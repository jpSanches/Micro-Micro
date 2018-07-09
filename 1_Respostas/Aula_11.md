1. Defina a função `void Atraso(volatile unsigned int x);` que fornece um atraso de `x` milissegundos. Utilize o Timer_A para a contagem de tempo, e assuma que o SMCLK já foi configurado para funcionar a 1 MHz. Esta função poderá ser utilizada diretamente nas outras questões desta prova.

```C
void Atraso(volatile unsigned int x)
{
    TA0CCR0 = 1000-1;
    TA0CTL = TASSEL_2 + ID_3 + MC_1;
    while (x > 0) {
        while((TA0CTL & TAIFG)==0);
        x--;
        TA0CTL &= ~TAIFG;
    }
    TA0CTL |= TACLR;
}
```

2. Pisque os LEDs da Launchpad numa frequência de 100 Hz.

```C
P1DIR |= LEDS;
while(1)
{
    P1OUT ^= LEDS;
	Atraso(5); // 10ms/2
}
```
3. Pisque os LEDs da Launchpad numa frequência de 20 Hz.

```C
P1DIR |= LEDS;
while(1)
{
    P1OUT ^= LEDS;
	Atraso(25); // 50ms/2
}
```
4. Pisque os LEDs da Launchpad numa frequência de 1 Hz.

```C
P1DIR |= LEDS;
while(1)
{
    P1OUT ^= LEDS;
	Atraso(500); // 1000ms/2
}
```
5. Pisque os LEDs da Launchpad numa frequência de 0,5 Hz.

```C
P1DIR |= LEDS;
while(1)
{
    P1OUT ^= LEDS;
	Atraso(1000); // 2000ms/2
}
```
6. Repita as questões 2 a 5 usando a interrupção do Timer A para acender ou apagar os LEDs.

```C
void Atraso(volatile unsigned int x)
{
    TA0CCR0 = 1000-1;
	TA0CTL = TASSEL_2 + ID_0 + MC_1 + TAIE;
}
interrupt(TIMER0_A1_VECTOR) TA0_ISR(void)
{
	x--;
	if(x == 0)
		P1OUT ^= LED;
	TA0CTL &= ~TAIFG;
}
```

7. Defina a função `void paralelo_para_serial(void);` que lê o byte de entrada via porta P1 e transmite os bits serialmente via pino P2.0. Comece com um bit em nivel alto, depois os bits na ordem P1.0 - P1.1 - … - P1.7 e termine com um bit em nível baixo. Considere um período de 1 ms entre os bits.

```C

void Atraso(volatile unsigned int x); //atraso em ms.

void paralelo_para_serial(void)
{
	P1IN |= BIT0;
    Atraso(1);
	for(int i = 0; i < 8; i++)
	{
		if((P1IN & (1<<i))) == 1)
			P2OUT |= BIT0;
		else
			P2OUT &= ~BIT0;
		Atraso(1);
	}
	P2OUT &= ~BIT0;
    Atraso(1);
}

void Atraso(volatile unsigned int x)
{
	TACCR0 = 1000-1;
	TACTL = TASSEL_2 + ID_0 + MC_1;
	while(x>0)
	{
		while((TACTL&TAIFG)==0);
        x--;
        TACTL &= ~TAIFG;
    }
	TACTL |= TACLR;
}
```

8. Faça o programa completo que lê um byte de entrada serialmente via pino P2.0 e transmite este byte via porta P1. O sinal serial começa com um bit em nivel alto, depois os bits na ordem 0-7 e termina com um bit em nível baixo. Os pinos P1.0-P1.7 deverão corresponder aos bits 0-7, respectivamente. Considere um período de 1 ms entre os bits.

```C
void Atraso_ms(volatile unsigned int ms); //Função para delay com timer.

void serial_para_paralelo(void)
{
	while((P2IN & BIT0) != 1);
	Atraso(1);
	for(int i = 0; i < 8; i++)
	{
		if((P2IN & (1<<i)) == 1)
			P1OUT |= (1<<i);
		else
			P1OUT &= ~(1<<i);
		Atraso(1);
	}
	while((P2IN & BIT0) == 1);
    Atraso(1);
}
```
9. Defina a função `void ConfigPWM(volatile unsigned int freqs, volatile unsigned char ciclo_de_trabalho);` para configurar e ligar o Timer_A em modo de comparação. Considere que o pino P1.6 já foi anteriormente configurado como saída do canal 1 de comparação do Timer_A, que somente os valores {100, 200, 300, …, 1000} Hz são válidos para a frequência, que somente os valores {0, 25, 50, 75, 100} % são válidos para o ciclo de trabalho, e que o sinal de clock SMCLK do MSP430 está operando a 1 MHz.

```C
void ConfigPWM(volatile unsigned int freqs, volatile unsigned char ciclo_de_trabalho)
{
	unsigned int T = 500000 / f; // período em us.
    TACCTL1 = OUTMOD_7; // Modo de operação reset/set
    TACCR0 = T-1; // limite superior do counter
	TACCR1 = (ciclo_de_trabalho * T / 100) - 1; // porcentagem ativo
	TACTL = TASSEL_2 + ID_1 + MC_1;
}
```
