1. Escreva uma função em C que faz o debounce de botões ligados à porta P1.

```C
void debounce(){
    unsigned volatile int counter = 0;

    while ((P2IN & BTN) ==0){

        P1OUT |= LEDS; // liga LEDS
        counter = 1000; //

    }

    P1OUT &= ~LEDS; //desliga LEDS

    while(counter-- !=0){}

}
```

2. Escreva um código em C que lê 9 botões multiplexados por 6 pinos, e pisca os LEDs da placa Launchpad de acordo com os botões. Por exemplo, se o primeiro botão é pressionado, os LEDs piscam uma vez; se o segundo botão é pressionado, os LEDs piscam duas vezes; e assim por diante. Se mais de um botão é pressionado, os LEDs não piscam.

```C
#include <msp430.h>
#define ROW1 BIT2
#define ROW2 BIT3
#define ROW3 BIT4

#define COL1 BIT5
#define COL2 BIT6
#define COL3 BIT7

#define LEDS (LED1|LED2)

void pisca();

void main(void)
{
    int coord[3][3]; // [ROW][COL]
    long unsigned int i, j;
    int q_press;

    WDTCTL = WDTPW | WDTHOLD;
    PM5CTL0 &= ~LOCKLPM5;

    P1DIR |= LEDS;
    P1DIR &= ~(ROW1 + ROW2 + ROW3); // entradas inicialmente
    P1DIR |= (COL1 + COL2 + COL3); // saidas inicialmente

    P1REN |= (ROW1 + ROW2 + ROW3); // ativa resistores nas entradas
    P1OUT |= (ROW1 + ROW2 + ROW3); // ativa-os como pull-up

    while(1)
    {
        q_press = 0;
        coord = [[0,0,0],[0,0,0],[0,0,0]];

        // ROWs são entradas, COLs são saídas
        P1DIR |= LEDS;
        P1DIR &= ~(ROW1 + ROW2 + ROW3); // entradas inicialmente
        P1DIR |= (COL1 + COL2 + COL3); // saidas inicialmente

        P1REN |= (ROW1 + ROW2 + ROW3); // ativa resistores nas entradas
        P1OUT |= (ROW1 + ROW2 + ROW3); // ativa-os como pull-up

        P1OUT &= ~(COL1 + COL2 + COL3);
        for (i = 1000; i <= 10000; i*= 10) {
            if ((P1IN & i) == 0 ) {
                for (j = 0; j < 3; j++){
                    coord[i][j] = 1; // soma 1 a toda a coluna que foi apertada
                }
            }
        }

        // ROWs são saídas, COLs são entradas
        P1DIR |= (ROW1 + ROW2 + ROW3); // saídas
        P1DIR &= ~(COL1 + COL2 + COL3); // entradas

        P1REN |= (COL1 + COL2 + COL3); // ativa resistores nas entradas
        P1OUT |= (COL1 + COL2 + COL3); // ativa-os como pull-up

        P1OUT &= ~(ROW1 + ROW2 + ROW3);
        for (i = 100000; i <= 10000000; i*= 10) {
            if ((P1IN & i) == 0 ) {
                for (j = 0; j < 3; j++){
                    coord[j][i] = 1; // soma 1 a toda a linha que foi apertada
                }
            }
        }
        for (i = 0; i < 3; i++) {
            for (j = 0; j < 3; j++) {
                // conta quantos botões apertados foram registrados
                if(coord[i][j] == 2) {
                    q_press++;
                }

                // detecta qual botão foi registrado no vetor
                switch (i) {
                    case 0:
                        switch (j) {
                            case 0:
                                if (coord[i][j] == 2){
                                    num = 1;
                                }
                                break;
                            case 1:
                                if (coord[i][j] == 2){
                                    num = 2;
                                }
                                break;
                            default
                                if (coord[i][j] == 2){
                                    num = 3;
                                }
                        }
                        break;

                    case 1:
                        switch (j) {
                            case 0:
                                if (coord[i][j] == 2){
                                    num = 4;
                                }
                                break;

                            case 1:
                                if (coord[i][j] == 2){
                                    num = 5;
                                }
                                break;

                            default
                                if (coord[i][j] == 2){
                                    num = 6;
                                }
                        }
                        break;

                    default
                        switch (j) {
                            case 0:
                                if (coord[i][j] == 2){
                                    num = 7;
                                }
                                break;

                            case 1:
                                if (coord[i][j] == 2){
                                    num = 8;
                                }
                                break;

                            default
                                if (coord[i][j] == 2){
                                    num = 9;
                                }
                        }
                }

            }
        }

        if (q_press == 1) {
            for(i=0; i < num; i++) {
                pisca();

            }
        }
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
