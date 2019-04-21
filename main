                                                  

#pragma config FOSC = INTIO67
#pragma config WDTEN = OFF, LVP = OFF, MCLRE = ON


#include "p18f45k20.h"

#include <delays.h>

#define CLEAR_SCREEN  	0b00000001
#define FOUR_BIT  		0b00101100
#define LINES_5X7  		0b00111000
#define CURSOR_BLINK  	0b00001111
#define CURSOR_RIGHT  	0b00000110

#define DATA_PORT  LATD
#define RS_PIN     PORTDbits.RD6
#define E_PIN      PORTDbits.RD7
int	actuatorState = 0;
int	stirrerState = 0;
int platformState = 0;

 //*************************PROTOTYPES*****************************************************// 
void Initialise (void)
void Interupt (void) 
void cup count (void)
void MAKE COFFEE (void)                    // MAKE COFFEE
void plunge to seal (void)
void Select brew time (void)
void stir and brew (void)
void mug backwards (void) 
void Plunge (void)  
void Mug forwards (void)  
void Coffee Ready (void)  
void Eject (void)  
void reset (void) 
void Delay5milli(void)



void SetAddr(unsigned char DDaddr)
{
        DATA_PORT &= 0xf0;                      // Write upper nibble
        DATA_PORT |= (((DDaddr | 0b10000000)>>4) & 0x0f);
                        
        RS_PIN = 0;                             // Set control bit
      	Delay5milli();
        E_PIN = 1;                              // Clock the cmd and address in
        Delay5milli();
        E_PIN = 0;

		DATA_PORT &= 0xf0;                      // Write lower nibble
        DATA_PORT |= (DDaddr&0x0f);

		Delay5milli();
        E_PIN = 1;                              // Clock the cmd and address in
        Delay5milli();
        E_PIN = 0;
}

void WriteCmd(unsigned char cmd)
	{
        DATA_PORT &= 0xf0;
        DATA_PORT |= (cmd>>4)&0x0f;           
        RS_PIN = 0;                     		// Set control signals for command
        Delay5milli();
        E_PIN = 1;                      		// Clock command in
        Delay5milli();
        E_PIN = 0;

       	
        DATA_PORT &= 0xf0;              		// Lower nibble interface
        DATA_PORT |= cmd&0x0f;
		Delay5milli();
        E_PIN = 1;                      		// Clock command in
        Delay5milli();
        E_PIN = 0;
	}

void WriteChar(char data)
{
        DATA_PORT &= 0xf0;
        DATA_PORT |= ((data>>4)&0x0f);

		RS_PIN = 1;                     		// Set control bits
        Delay5milli();
        E_PIN = 1;                      		// Clock nibble into LCD
        Delay5milli();
        E_PIN = 0;

		    
        DATA_PORT &= 0xf0;              		// Lower nibble interface
        DATA_PORT |= (data&0x0f);
        
        Delay5milli();
        E_PIN = 1;                      		// Clock nibble into LCD
        Delay5milli();
        E_PIN = 0;
}

 void WriteString(const rom char *buffer)    
{		 
        while(*buffer)                  		// Write data to LCD up to null
        {
          Delay5milli();
          WriteChar( *buffer);          		// Write character to LCD
          buffer++;                     		// Increment buffer
        }
        return;
} 
      
void ADC_Init(void)
{ 	
	// initialize the Analog-To-Digital converter.
    // First, we need to make sure the AN0 pin is enabled as an analog input
    // as the demo board potentiometer is connected to RA0/AN0
    // Don't forget that RB0/AN12 must be digital!

	ANSEL = 0;	//turn off all other analog inputs
	ANSELH = 0;
 	ANSELbits.ANS0 = 1;	// turn on RA0 analog

    // Sets bits VCFG1 and VCFG0 in ADCON1 so the ADC voltage reference is VSS to VDD

    ADCON1 = 0;
    
    //
    // ADFM = 0 so we can easily read the 8 Most Significant bits from the ADRESH
    // Special Function Register
    
	ADCON2 = 0b00111000;

    // Select channel 0 (AN0) to read the An0 voltage and turn on ADC
    ADCON0 = 0b00000001;

}

unsigned char ADC_Convert(void)

{ // start an ADC conversion and return the 8 most-significant bits of the result
    ADCON0bits.GO_DONE = 1;             // start conversion
    while (ADCON0bits.GO_DONE == 1);    // wait for it to complete
    return ADRESH;                      // return high byte of result
}

void main (void){
	Intialise();
	While (1){
		SetAddr  (0x00);
		WriteString(" Cup      Make  ");   //cup count on the left side of LCD 
		SetAddr  (0xc0);                   //make coffee on the left side
		WriteString("Count  Coffee");      //PERHAPS CONSIDER MAKING THIS MORE SOPHISTICATED WITH ARROWS OR INVERTED CONTRAST TO HIGHLIGHT
		while (PORTAbits.RA1 == 0){
			if(counter>10){
				//indicate MAKE COFFEE
			}
			else{
				// indicate CUP COUNT 
			}	
		}
		if(counter>10){
			MakeCoffee();
		}
		else{
			CupCount(); 
		}
	}
}   





	

			                 		//stop the program - Loop here forever
	WriteCmd ( CLEAR_SCREEN);
	SetAddr  (0x00);
	WriteString("Put Coffee");
	Delay10KTCYx(200);
	WriteCmd ( CLEAR_SCREEN); 
while (PORTAbits.RA1 == 1){
		SetAddr  (0x00);
		WriteString("Pour in water");
		SetAddr	(0xc0);
		WriteString("Press to brew");
	}
		WriteCmd (CLEAR_SCREEN);
	PORTBbits.RB6 = 1;				//starts plunging
	actuatorState = 1;
	Delay10KTCYx(50);					//delay for drip mitigation
	LATBbits.LATB6 = 0;					//stop plunging
	actuatorState = 0;
	


	//		ADD varaible brewtime

	WriteString("Stirring");
	Delay10KTCYx(250);					//delay for stirring, will be set by a brew time
	WriteCmd ( CLEAR_SCREEN);
	WriteString("Plunging");
	LATB	= 0b01000000;				//start AeroPress plunging
	actuatorState = 1;
	Delay10KTCYx(250);
	Delay10KTCYx(250);
	LATB	= 0b00000000;				//stop plunging
	actuatorState = 0;
	WriteCmd ( CLEAR_SCREEN);
	while (PORTAbits.RA1 == 1){
		SetAddr  (0x00);
		WriteString("remove filter");
		SetAddr	(0xc0);
		WriteString("press to eject");
	}
	LATB	= 0b01000000;				//start AeroPress remove coffee puck
	actuatorState = 1;
	WriteCmd ( CLEAR_SCREEN);
	SetAddr  (0x00);
	WriteString("Ejecting puck");
	Delay10KTCYx(250);
	LATB	= 0b00000000;				//stop plunging
	actuatorState = 0;
	WriteCmd ( CLEAR_SCREEN);
	WriteString("Resetting");
	LATB	= 0b10000000;				//start AeroPress retracting
	actuatorState = 2;
	Delay10KTCYx(250);
	Delay10KTCYx(250);
	Delay10KTCYx(50);
	LATB	= 0b00000000;				//stop plunging
	actuatorState = 0;

	}
}


 //FUNCTION LIST 
void Delay5milli(void)							//Suitable delay for LCD
{
Delay1KTCYx(2);           						
}

void Initialise (void){

	TRISB = 0b00000000;     	// PORTB bits 7:0 are all outputs (0)
	TRISAbits.TRISA0 = 1;		// TRISA0 = 1 RA0 = input
   	ANSEL  = 0;	                        	    //turn off all analog inputs
	ANSELH = 0; 
   	TRISD  = 0b00000000;                 		//sets PORTd
   	LATD   = 0b00000000;	                	//turns off PORTd outputs, good start position   
	PORTB   = 0b00000000;						//turns off PORTB outputs	

	// this code configures the display  
 	
	WriteCmd ( 0x02 );							// sets 4bit operation
	WriteCmd ( CLEAR_SCREEN);		
	WriteCmd ( FOUR_BIT & LINES_5X7 );			// sets 5x7 and multiline operation.
	WriteCmd ( CURSOR_BLINK );					// blinks cursor
	WriteCmd ( CURSOR_RIGHT  );					// moves cursor right	


}

void Interupt (void) 
void cup count (void)
void MAKE COFFEE (void){                    // MAKE COFFEE
			                 		//stop the program - Loop here forever
	WriteCmd ( CLEAR_SCREEN);
	SetAddr  (0x00);
	WriteString("Put Coffee");
	Delay10KTCYx(200);
	WriteCmd ( CLEAR_SCREEN); 
while (PORTAbits.RA1 == 1){
		SetAddr  (0x00);
		WriteString("Pour in water");
		SetAddr	(0xc0);
		WriteString("Press to brew");
	}
		WriteCmd (CLEAR_SCREEN);
	PORTBbits.RB6 = 1;				//starts plunging
	actuatorState = 1;
	Delay10KTCYx(50);					//delay for drip mitigation
	LATBbits.LATB6 = 0;					//stop plunging
	actuatorState = 0;
	


	//		ADD varaible brewtime

	WriteString("Stirring");
	Delay10KTCYx(250);					//delay for stirring, will be set by a brew time
	WriteCmd ( CLEAR_SCREEN);
	WriteString("Plunging");
	LATB	= 0b01000000;				//start AeroPress plunging
	actuatorState = 1;
	Delay10KTCYx(250);
	Delay10KTCYx(250);
	LATB	= 0b00000000;				//stop plunging
	actuatorState = 0;
	WriteCmd ( CLEAR_SCREEN);
	while (PORTAbits.RA1 == 1){
		SetAddr  (0x00);
		WriteString("remove filter");
		SetAddr	(0xc0);
		WriteString("press to eject");
	}
	LATB	= 0b01000000;				//start AeroPress remove coffee puck
	actuatorState = 1;
	WriteCmd ( CLEAR_SCREEN);
	SetAddr  (0x00);
	WriteString("Ejecting puck");
	Delay10KTCYx(250);
	LATB	= 0b00000000;				//stop plunging
	actuatorState = 0;
	WriteCmd ( CLEAR_SCREEN);
	WriteString("Resetting");
	LATB	= 0b10000000;				//start AeroPress retracting
	actuatorState = 2;
	Delay10KTCYx(250);
	Delay10KTCYx(250);
	Delay10KTCYx(50);
	LATB	= 0b00000000;				//stop plunging
	actuatorState = 0;

	}
}
void plunge to seal (void)
void Select brew time (void)
void stir and brew (void)
void mug backwards (void) 
void Plunge (void)  
void Mug forwards (void)  
void Coffee Ready (void)  
void Eject (void)  
void reset (void)  

 

