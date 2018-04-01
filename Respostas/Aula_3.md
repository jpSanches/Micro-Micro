1.Dada uma variável a do tipo char (um byte), escreva os trechos de código em C para:

	(a) Somente setar o bit menos significativo de a.
		a |= 0x01;

	(b) Somente setar dois bits de a: o menos significativo e o segundo menos significativo.
		a |= 0x03;

	(c) Somente zerar o terceiro bit menos significativo de a.
		a &= ~0x04; // ~0x04 = not(0000 0100)

	(d) Somente zerar o terceiro e o quarto bits menos significativo de a.
		a &= ~0x0E; // ~0x0E = not(0000 1100)

	(e) Somente inverter o bit mais significativo de a.
		a ^= 0x80; // 1000 0000

	(f) Inverter o nibble mais significativo de a, e setar o nibble menos significativo de a.
		a ^= 0xF0;
		a |= 0x0F;

2.Considerando a placa Launchpad do MSP430, escreva o código em C para piscar os dois LEDs ininterruptamente.
``` C
#include <msp430.h>
#define LED1 BIT0
#define LED2 BIT1

void main (void){
    unsigned volatile int count;
    WDTCTL = WDTPW|WDTHOLD;
    PM5CTL0 &= ~LOCKLPM5;
    P1DIR  = LED1 + LED2;
    P1OUT = LED1;
    
    for(;;){
        P1OUT ^= (LED1 + LED2);
        for(count = 0; count < 32000; count++){
        };
    };
}

```

3.Considerando a placa Launchpad do MSP430, escreva o código em C para piscar duas vezes os dois LEDs sempre que o usuário pressionar o botão.
``` C
#include <msp430g2553.h>
#define LED1 BIT0
#define LED2 BIT6
#define BTN BIT3

void main (void){
	int count;
	WDTCTL = WDTPW|WDTHOLD;
	P1DIR  = LED1 + LED2;
	P1OUT = 0;

	for(;;){
		if(BTN & P1OUT == 0){
			P1OUT = LED1 + LED2;							//liga

			for(count = 0; count < 1000; count++){		
			}

			P1OUT = 0;								//desliga

			for(count = 0; count < 1000; count++){		
			}

			P1OUT = LED1 + LED2;							//liga

			for(count = 0; count < 1000; count++){		
			}

			P1OUT = 0;								//desliga
		}
	}
}
 ```
4.Considerando a placa Launchpad do MSP430, faça uma função em C que pisca os dois LEDs uma vez.
``` C
	void pisca(){
	unsigned volatile int counter;
	P1OUT ^= LEDS;
 	P2OUT ^= BTN;

	for(counter = 0; counter < 30000; counter++){}

	P1OUT ^= LEDS;
  	P2OUT ^= BTN;

	for(counter = 0; counter < 30000; counter++){}
}

```


5.Reescreva o código da questão 2 usando a função da questão 4.

``` C
#include <msp430.h>
#define LED1 BIT0
#define LED2 BIT1
#define LEDS (LED1|LED2)
#define BTN  BIT3

void pisca();

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
		pisca();
    }

}

void pisca(){
  	  unsigned volatile int counter;
      P1OUT ^= LEDS;
      P2OUT ^= BTN;

		  for(counter = 0; counter < 30000; counter++){}
      P1OUT ^= LEDS;
      P2OUT ^= BTN;

		  for(counter = 0; counter < 30000; counter++){}
}


```
6.Reescreva o código da questão 3 usando a função da questão 4.
``` C
#include <msp430.h>
#define LED1 BIT0
#define LED2 BIT1
#define LEDS (LED1|LED2)
#define BTN  BIT3

void pisca();

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
        if((P2IN & BTN)==0){
         pisca();
         pisca();
        }else
            P1OUT &= ~LEDS;
    }

}

void pisca(){
    unsigned volatile int counter;
    P1OUT ^= LEDS;
    P2OUT ^= BTN;

		for(counter = 0; counter < 30000; counter++){}

		P1OUT ^= LEDS;
    P2OUT ^= BTN;

		for(counter = 0; counter < 30000; counter++){}
}

```
