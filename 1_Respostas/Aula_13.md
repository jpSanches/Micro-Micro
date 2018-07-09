Para todas as questões abaixo, utilize o modo de comparação do Timer A.

1. Para os itens abaixo, confira a diferença no brilho do LED.
	(a) Pisque o LED no pino P1.6 numa frequência de 100 Hz e ciclo de trabalho de 25%.

	LED com brilho bastante reduzido.

	(b) Pisque o LED no pino P1.6 numa frequência de 100 Hz e ciclo de trabalho de 50%.

	LED com brilho mais forte que o de 25% de duty cycle, porém, menor que o de 75%.

	(c) Pisque o LED no pino P1.6 numa frequência de 100 Hz e ciclo de trabalho de 75%.

	LED com brilho mais forte que os de 25% e 50%, porém, menor que o de 10%% (ligado a todo momento).

2. Pisque o LED no pino P1.6 numa frequência de 1 Hz e ciclo de trabalho de 25%.

```C
#include <msp430g2553.h>

#define LED BIT6
#define PERIODO 62500 // = (1000000/2 )/8
#define DUTY_CYCLE 25*62500/100

int main(void)
{
	WDTCTL = WDTPW + WDTHOLD;
	BCSCTL1 = CALBC1_1MHZ;
	DCOCTL = CALDCO_1MHZ;

	P1DIR |= LED;
	P1SEL |= LED;
	P1SEL2 &= ~LED;

	TACCR0 = PERIODO-1;
	TACCR1 = DUTY_CYCLE-1;
	TACCTL1 = OUTMOD_4; // toggle dobra o periodo
	TACTL = TASSEL_2 + ID_3 + MC_1;
	_BIS_SR(LPM0_bits);
	return 0;
}
```

3. Pisque o LED no pino P1.6 numa frequência de 1 Hz e ciclo de trabalho de 50%.

```C
#include <msp430g2553.h>

#define LED BIT6
#define PERIODO 62500 // = (1000000/2 )/8
#define DUTY_CYCLE 50*62500/100

int main(void)
{
	WDTCTL = WDTPW + WDTHOLD;
	BCSCTL1 = CALBC1_1MHZ;
	DCOCTL = CALDCO_1MHZ;

	P1DIR |= LED;
	P1SEL |= LED;
	P1SEL2 &= ~LED;

	TACCR0 = PERIODO-1;
	TACCR1 = DUTY_CYCLE-1;
	TACCTL1 = OUTMOD_4; // toggle dobra o periodo
	TACTL = TASSEL_2 + ID_3 + MC_1;
	_BIS_SR(LPM0_bits);
	return 0;
}
```

4. Pisque o LED no pino P1.6 numa frequência de 1 Hz e ciclo de trabalho de 75%.


```C
#include <msp430g2553.h>

#define LED BIT6
#define PERIODO 62500 // = (1000000/2 )/8
#define DUTY_CYCLE 75*62500/100

int main(void)
{
	WDTCTL = WDTPW + WDTHOLD;
	BCSCTL1 = CALBC1_1MHZ;
	DCOCTL = CALDCO_1MHZ;

	P1DIR |= LED;
	P1SEL |= LED;
	P1SEL2 &= ~LED;

	TACCR0 = PERIODO-1;
	TACCR1 = DUTY_CYCLE-1;
	TACCTL1 = OUTMOD_4; // toggle dobra o periodo
	TACTL = TASSEL_2 + ID_3 + MC_1;
	_BIS_SR(LPM0_bits);
	return 0;
}
```
