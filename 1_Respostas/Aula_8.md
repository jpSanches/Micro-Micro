Para todas as questões, utilize os LEDs e/ou os botões da placa Launchpad do MSP430.

1. Escreva um código em C que pisca os LEDs ininterruptamente.

```C
#include <msp430g2553.h>
#define LED1 BIT0
#define LED2 BIT6

void main (void){
	int count;
	WDTCTL = WDTPW|WDTHOLD;
	P1DIR  = LED1 + LED2;
	P1OUT = 0;

    for(;;){

        P1OUT = LED1 + LED2;							//liga

		for(count = 0; count < 10000; count++){		
		}

		P1OUT = 0;								//desliga

		for(count = 0; count < 10000; count++){		
		}
	}
}
```

2. Escreva um código em C que pisca os LEDs ininterruptamente. No ciclo que pisca os LEDs, o tempo que os LEDs ficam ligados deve ser duas vezes maior do que o tempo que eles ficam desligados.

```C
#include <msp430g2553.h>
#define LED1 BIT0
#define LED2 BIT6

void main (void){
	int count;
	WDTCTL = WDTPW|WDTHOLD;
	P1DIR  = LED1 + LED2;
	P1OUT = 0;

	for(;;){

        P1OUT = LED1 + LED2;							//liga

		for(count = 0; count < 20000; count++){		
		}

		P1OUT = 0;								//desliga

		for(count = 0; count < 10000; count++){		
		}

	}
}
```

3. Escreva um código em C que acende os LEDs quando o botão é pressionado.

``` C
#include <msp430.h>
#define LED1 BIT0
#define LED2 BIT1
#define LEDS (LED1|LED2)
#define BTN  BIT3

void main(void)
{
    WDTCTL = WDTPW | WDTHOLD;
    PM5CTL0 &= ~LOCKLPM5;
    P1OUT |= LEDS;
    P1DIR |= LEDS;

    P2DIR &= ~BTN;
    P2REN |= BTN;
    P2OUT |= BTN;

    while(1)
    {
        if(P2IN & BNT == 0){

            P1OUT |= LEDS;

        }else{

            P1OUT &= ~LEDS;

        }
    }

}

```

4. Escreva um código em C que pisca os LEDs ininterruptamente somente se o botão for pressionado.

``` C
#include <msp430.h>
#define LED1 BIT0
#define LED2 BIT1
#define LEDS (LED1|LED2)
#define BTN  BIT3

void pisca();

void main(void){
    WDTCTL = WDTPW | WDTHOLD;
    PM5CTL0 &= ~LOCKLPM5;
    P1OUT |= LEDS;
    P1DIR |= LEDS;
    P2DIR &= ~BTN;
    P2REN |= BTN;
    P2OUT |= BTN;

    while(1)
    {
        if((P2IN & BTN)==0){
            pisca();

        }else{

            P1OUT &= ~LEDS;

        }

    }

}

void pisca(){
    unsigned volatile int counter;
    P1OUT ^= LEDS;

    for(counter = 0; counter < 30000; counter++){}

    P1OUT ^= LEDS;

    for(counter = 0; counter < 30000; counter++){}
}

```

5. Escreva um código em C que acende os LEDs quando o botão é pressionado. Deixe o MSP430 em modo de baixo consumo, e habilite a interrupção do botão.
```C
#include <msp430.h>
#include "legacymsp430.h" // Para rodar interrupcoes
#define LED1 BIT0
#define LED2 BIT1
#define LEDS (LED1|LED2)
#define BTN  BIT3


int main(void)
{
    unsigned volatile int counter;

    WDTCTL = WDTPW | WDTHOLD;
    PM5CTL0 &= ~LOCKLPM5;
    P1OUT |= LEDS;
    P1DIR |= LEDS;
    P2DIR &= ~BTN;
    P2REN |= BTN;
    P2OUT |= BTN;
    P2IES |= BTN;
    P2IE |= BTN;
    _BIS_SR(GIE);

    while(1)
    {

        for(counter = 0; counter < 30000; counter++){}

        P1OUT ^= LEDS;
    }
    return 0;
}
```
