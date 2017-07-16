# stm32f103c8t6-nrf24l01

I use Keil C 5 (V 5.11.0.0) + STM32CubeMX (V4.21.0)

# Pin connection:

Transmitter & Receiver (same pin connection)
STM32F103C8T6  |  NRF24L01
3.3V -->  VCC

PA1  -->  CE

PA2  -->  CSN

PA3  -->  SCK

PA4  -->  MOSI

PA5  -->  MISO

GND  -->  GND


# STM32CubeMX
# Pinout:
RCC
--- High Speed Clock: Crystal/Ceramic Resonator
SYS
--- Debug: Serial Wire
UART1
--- Mode: Asynchronous

# Clock Configuration
HCLK (MHz): 72 MHz

# Configuration 
GPIO
PA1,PA2, PA3, PA4: GPIO_Output

PA5: GPIO_Input

(Optional) PC13: GPIO_Output


PA1,PA2, PA3, PA4: Maxium output speed: High (all from PA1 to PA4)

PA5: Maxium output speed: n/a

PC13: Maxium output speed: Low (==no change)

User Label: 

PA1: NRF_CE

PA2: NRF_CSN

PA3: NRF_SCK

PA4: NRF_MOSI

PA5: NRF_MISO



UART1: NVIC settings: Enabled -> True


Then Generate code to Keil C 5 
Mcu: STM32F103C8Tx

Firmware Package Name and Version:

STM32Cube FW_F1 V1.4.0


Use Default Firmware Location:
C:\Users\YOURUSERNAME\STM32Cube\Repository\STM32Cube_FW_F1_V1.4.0

# Keil C 5
Add CTR_nrf24l01.c to Application/User folder

in main.c
Cut (in MX_GPIO_Init function)
/*Configure GPIO pins : NRF_CE_Pin NRF_CSN_Pin NRF_SCK_Pin NRF_MOSI_Pin */
  GPIO_InitStruct.Pin = NRF_CE_Pin|NRF_CSN_Pin|NRF_SCK_Pin|NRF_MOSI_Pin;
  GPIO_InitStruct.Mode = GPIO_MODE_OUTPUT_PP;
  GPIO_InitStruct.Speed = GPIO_SPEED_FREQ_HIGH;
  HAL_GPIO_Init(GPIOA, &GPIO_InitStruct);

  /*Configure GPIO pin : NRF_MISO_Pin */
  GPIO_InitStruct.Pin = NRF_MISO_Pin;
  GPIO_InitStruct.Mode = GPIO_MODE_INPUT;
  GPIO_InitStruct.Pull = GPIO_NOPULL;
  HAL_GPIO_Init(NRF_MISO_GPIO_Port, &GPIO_InitStruct);
  Then paste to CTR_nrf24l01.h in CTR_nrfPinConfig
  
  Cut (in main.h)
  #define NRF_CE_Pin GPIO_PIN_1
#define NRF_CE_GPIO_Port GPIOA
#define NRF_CSN_Pin GPIO_PIN_2
#define NRF_CSN_GPIO_Port GPIOA
#define NRF_SCK_Pin GPIO_PIN_3
#define NRF_SCK_GPIO_Port GPIOA
#define NRF_MOSI_Pin GPIO_PIN_4
#define NRF_MOSI_GPIO_Port GPIOA
#define NRF_MISO_Pin GPIO_PIN_5
#define NRF_MISO_GPIO_Port GPIOA
to CTR_nrf24l01.h (overwrite existing one after line "#include "stm32f1xx_hal.h"")

main.c
Add #include "CTR_nrf24l01.h"

#ifdef __GNUC__
  /* With GCC/RAISONANCE, small printf (option LD Linker->Libraries->Small printf
     set to 'Yes') calls __io_putchar() */
  #define PUTCHAR_PROTOTYPE int __io_putchar(int ch)
#else
  #define PUTCHAR_PROTOTYPE int fputc(int ch, FILE *f)
  #define GETCHAR_PROTOTYPE int fgetc(FILE *f)
#endif /* __GNUC__ */
	PUTCHAR_PROTOTYPE
{
  /* Place your implementation of fputc here */
  /* e.g. write a character to the USART */
	HAL_UART_Transmit(&huart1, (uint8_t*)&ch,1,100);

  /* Loop until the end of transmission */


  return ch;
}

uint8_t CTR_SENDBUFF[32];
//Hung
	CTR_nrfInit(); //khoi tao
	CTR_nrfSetTX(); //chon chuc nang gui du lieu
	// Them doan ma can gui vao buffer gui
	sprintf(CTR_SENDBUFF, "Hello Hung-gui qua NRF24L01");

	printf("Hello day la mach phat dung NRF24L01\r \n");
  
   while (1)
  {
  /* USER CODE END WHILE */
		HAL_GPIO_TogglePin(GPIOC, GPIO_PIN_13);
		
//		if (HAL_GPIO_ReadPin(GPIOC, GPIO_PIN_13) == 1){
			printf("Sending package to receiver \r \n");
			CTR_nrfSendPacket(CTR_SENDBUFF);
			CTR_nrfSetTX();
//		} else{
//			printf("Idling. Not sending any package\r \n");
//		}
		
		HAL_Delay(1000);
	
  /* USER CODE BEGIN 3 */

  }
  
  ----
  add CTR_nrfPinConfig(); to MX_GPIO_Init
  
  Then Compile and Load to your MCU
  
  Note: I use STLINK Mini to load code to MCU
  
  
  









