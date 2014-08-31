USARTlib
========

USART lib and examples


ok lets use sprint for float variables in ATmel studio 6 !

first we must use this line for make a stream :

static FILE mystdout = FDEV_SETUP_STREAM(uart_putchar, NULL,_FDEV_SETUP_WRITE);

then we must make the "uart_putchar" function :

static int uart_putchar(char c, FILE *stream)
{

	if (c == '\n')
	uart_putchar('\r', stream);
	loop_until_bit_is_set(UCSRA, UDRE);
	UDR = c;
	return 0;
}


the type of this function must be static int , the first and second line defer that we have an "ENTER" in hyperterm

now we must use stdout or stdin in the top of mine function after USART_INIT :
stdout = &mystdout;

#lets Putting it All Together :

define F_CPU 7372800// Clock Speed

define BAUD 2400

define MYUBRR (F_CPU/16/BAUD)-1


void USART_Init( unsigned int ubrr)
{

	/* Set baud rate */
	UBRRH = (unsigned char)(ubrr>>8);
	UBRRL = (unsigned char)ubrr;
	/* Enable receiver and transmitter */
	UCSRB = (1<<RXEN)|(1<<TXEN);
	/* Set frame format: 8data, 2stop bit */
	UCSRC = (1<<URSEL)|(1<<USBS)|(3<<UCSZ0);
}


static int uart_putchar(char c, FILE *stream);

static FILE mystdout = FDEV_SETUP_STREAM(uart_putchar, NULL,_FDEV_SETUP_WRITE);

static int uart_putchar(char c, FILE *stream)
{

	if (c == '\n')
	uart_putchar('\r', stream);
	loop_until_bit_is_set(UCSRA, UDRE); // this line looklike we write : while ( !( UCSRA & (1<<UDRE)) );
	UDR = c;
	return 0;
}


int main(void)
{

	USART_Init(MYUBRR );
	
        char buff[100];
        
	double arash=121.2;
	
	stdout = &mystdout;
	
	while(1){
	
    	sprintf(buff,"%f",arash);
    	
	puts(buff);
		
	}
	
}


#note :
dont forget to include the io.h and stdio.h !
somthing is very important and that is we must add "printf_flt" and "m" to Libraries, and "-Wl,-u,vfprintf" to Options.
in atmel studio --> clik on the project tab in the top of project-->then click on projectname properties --> Toolchain -->
AVR/GNU Linker-->libraries then add "printf_flt" and "m" to Libraries ! and one more step in general check the use vprintf library.

0k this is the easiest way to use float variable functions like sprintf in atmle studio :)! 
