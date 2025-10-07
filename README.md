# Interface-a-4x4-matrix-keypad-and-display-the-output-on-LCD-for-keys-arithmetic-operations.
# NAME: G.KARTHIK 
# REG NO:212223220043

# Hardware Setup

4x4 Keypad (16 keys)

4 Row pins → STM32 GPIO Output

4 Column pins → STM32 GPIO Input with Pull-Up

16x2 LCD (4-bit mode)

Data pins D4–D7 → GPIO Output

Control pins:

RS → GPIO Output

RW → GND (write mode)

EN → GPIO Output

VCC, GND, VEE (contrast) connected

# CubeMX Configuration

Open STM32CubeIDE → New Project

Select MCU/Board (e.g., STM32F103C8T6 or Nucleo board)

Configure GPIO pins:

Keypad Rows → Output mode

Keypad Columns → Input mode (Enable internal Pull-Up)

LCD pins → Output mode

Enable SYS → Debug (Serial Wire) for flashing

Generate Code

# Keypad Scanning Logic

Set one row LOW at a time, keep others HIGH

Read columns

If any column goes LOW → key pressed

Use a lookup table to map row & column to actual key

# LCD Initialization

Use 4-bit mode

Send initialization commands (function set, display on, clear display, entry mode set)

Create LCD_SendCommand() and LCD_SendData() functions using HAL_GPIO

# Main Program Logic

Initialize LCD + Keypad

Wait for key press

If key is digit → display on LCD, store in variable

If key is operator (+, -, *, /) → store operator, display it

If = → perform arithmetic and show result on LCD

If C → clear LCD & reset

# CODE
```
/* USER CODE BEGIN Header */
/**
  ******************************************************************************
  * @file           : main.c
  * @brief          : Main program body
  ******************************************************************************
  * @attention
  *
  * Copyright (c) 2025 STMicroelectronics.
  * All rights reserved.
  *
  * This software is licensed under terms that can be found in the LICENSE file
  * in the root directory of this software component.
  * If no LICENSE file comes with this software, it is provided AS-IS.
  *
  ******************************************************************************
  */
/* USER CODE END Header */
/* Includes ------------------------------------------------------------------*/
#include "main.h"
#include "lcd.h"
#include <stdbool.h>
#include <stdio.h>
#include <string.h>
#include <stdlib.h>
/* Private includes ----------------------------------------------------------*/
/* USER CODE BEGIN Includes */
bool col1, col2, col3, col4;
/* USER CODE END Includes */

/* Private typedef -----------------------------------------------------------*/
/* USER CODE BEGIN PTD */
int num1 = 0;
int num2 = 0;
char op = 0;
bool opSelected = false;
bool resultDisplayed = false;
char buffer[16];
/* USER CODE END PTD */

/* USER CODE BEGIN 0 */
/* USER CODE END PTD */

/* Private define ------------------------------------------------------------*/
/* USER CODE BEGIN PD */

/* USER CODE END PD */

/* Private macro -------------------------------------------------------------*/
/* USER CODE BEGIN PM */

/* USER CODE END PM */

/* Private variables ---------------------------------------------------------*/

/* USER CODE BEGIN PV */

/* USER CODE END PV */

/* Private function prototypes -----------------------------------------------*/
void SystemClock_Config(void);
static void MX_GPIO_Init(void);
/* USER CODE BEGIN PFP */
Lcd_HandleTypeDef lcd;
/* USER CODE END PFP */

/* Private user code ---------------------------------------------------------*/
/* USER CODE BEGIN 0 */
void key(void);

Lcd_PortType ports[] = {GPIOA, GPIOA, GPIOA, GPIOA};
Lcd_PinType pins[] = {GPIO_PIN_3, GPIO_PIN_2, GPIO_PIN_1, GPIO_PIN_0};
/Lcd_HandleTypeDef lcd;/
/* USER CODE END 0 */

/**
  * @brief  The application entry point.
  * @retval int
  */
int main(void)
{

  /* USER CODE BEGIN 1 */

  /* USER CODE END 1 */

  /* MCU Configuration--------------------------------------------------------*/

  /* Reset of all peripherals, Initializes the Flash interface and the Systick. */
  HAL_Init();

  /* USER CODE BEGIN Init */

  /* USER CODE END Init */

  /* Configure the system clock */
  SystemClock_Config();

  /* USER CODE BEGIN SysInit */

  /* USER CODE END SysInit */

  /* Initialize all configured peripherals */
  MX_GPIO_Init();
  /* USER CODE BEGIN 2 */
  lcd = Lcd_create(ports, pins, GPIOB, GPIO_PIN_0, GPIOB, GPIO_PIN_1, LCD_4_BIT_MODE);
  /* USER CODE END 2 */
  /lcd = Lcd_create(ports, pins, GPIOB, GPIO_PIN_0, GPIOB, GPIO_PIN_1, LCD_4_BIT_MODE);/
  /* Infinite loop */
  /* USER CODE BEGIN WHILE */
  while (1)
  {
    /* USER CODE END WHILE */
	  key();
    /* USER CODE BEGIN 3 */
  }
  /* USER CODE END 3 */
}

void key()
{
	// ---- your keypad scanning logic ----
	    // (unchanged: we only add arithmetic logic inside conditions)

	    HAL_GPIO_WritePin(GPIOC, GPIO_PIN_0, GPIO_PIN_RESET);
	    HAL_GPIO_WritePin(GPIOC, GPIO_PIN_1, GPIO_PIN_SET);
	    HAL_GPIO_WritePin(GPIOC, GPIO_PIN_2, GPIO_PIN_SET);
	    HAL_GPIO_WritePin(GPIOC, GPIO_PIN_3, GPIO_PIN_SET);

	    col1 = HAL_GPIO_ReadPin(GPIOC, GPIO_PIN_4);
	    col2 = HAL_GPIO_ReadPin(GPIOC, GPIO_PIN_5);
	    col3 = HAL_GPIO_ReadPin(GPIOC, GPIO_PIN_6);
	    col4 = HAL_GPIO_ReadPin(GPIOC, GPIO_PIN_7);

	    if (!col1)
	    {
	        Lcd_clear(&lcd);
	        Lcd_string(&lcd, "7");
	        if (!opSelected)
	            num1 = num1 * 10 + 7;
	        else
	            num2 = num2 * 10 + 7;
	        HAL_Delay(300);
	    }
	    else if (!col2)
	    {
	        Lcd_clear(&lcd);
	        Lcd_string(&lcd, "8");
	        if (!opSelected)
	            num1 = num1 * 10 + 8;
	        else
	            num2 = num2 * 10 + 8;
	        HAL_Delay(300);
	    }
	    else if (!col3)
	    {
	        Lcd_clear(&lcd);
	        Lcd_string(&lcd, "9");
	        if (!opSelected)
	            num1 = num1 * 10 + 9;
	        else
	            num2 = num2 * 10 + 9;
	        HAL_Delay(300);
	    }
	    else if (!col4)
	    {
	        Lcd_clear(&lcd);
	        Lcd_string(&lcd, "%");
	        op = '%';
	        opSelected = true;
	        HAL_Delay(300);
	    }

	    HAL_GPIO_WritePin(GPIOC, GPIO_PIN_0, GPIO_PIN_SET);
	    HAL_GPIO_WritePin(GPIOC, GPIO_PIN_1, GPIO_PIN_RESET);
	    HAL_GPIO_WritePin(GPIOC, GPIO_PIN_2, GPIO_PIN_SET);
	    HAL_GPIO_WritePin(GPIOC, GPIO_PIN_3, GPIO_PIN_SET);

	    col1 = HAL_GPIO_ReadPin(GPIOC, GPIO_PIN_4);
	    col2 = HAL_GPIO_ReadPin(GPIOC, GPIO_PIN_5);
	    col3 = HAL_GPIO_ReadPin(GPIOC, GPIO_PIN_6);
	    col4 = HAL_GPIO_ReadPin(GPIOC, GPIO_PIN_7);

	    if (!col1)
	    {
	        Lcd_clear(&lcd);
	        Lcd_string(&lcd, "4");
	        if (!opSelected)
	            num1 = num1 * 10 + 4;
	        else
	            num2 = num2 * 10 + 4;
	        HAL_Delay(300);
	    }
	    else if (!col2)
	    {
	        Lcd_clear(&lcd);
	        Lcd_string(&lcd, "5");
	        if (!opSelected)
	            num1 = num1 * 10 + 5;
	        else
	            num2 = num2 * 10 + 5;
	        HAL_Delay(300);
	    }
	    else if (!col3)
	    {
	        Lcd_clear(&lcd);
	        Lcd_string(&lcd, "6");
	        if (!opSelected)
	            num1 = num1 * 10 + 6;
	        else
	            num2 = num2 * 10 + 6;
	        HAL_Delay(300);
	    }
	    else if (!col4)
	    {
	        Lcd_clear(&lcd);
	        Lcd_string(&lcd, "*");
	        op = '*';
	        opSelected = true;
	        HAL_Delay(300);
	    }

	    HAL_GPIO_WritePin(GPIOC, GPIO_PIN_0, GPIO_PIN_SET);
	    HAL_GPIO_WritePin(GPIOC, GPIO_PIN_1, GPIO_PIN_SET);
	    HAL_GPIO_WritePin(GPIOC, GPIO_PIN_2, GPIO_PIN_RESET);
	    HAL_GPIO_WritePin(GPIOC, GPIO_PIN_3, GPIO_PIN_SET);

	    col1 = HAL_GPIO_ReadPin(GPIOC, GPIO_PIN_4);
	    col2 = HAL_GPIO_ReadPin(GPIOC, GPIO_PIN_5);
	    col3 = HAL_GPIO_ReadPin(GPIOC, GPIO_PIN_6);
	    col4 = HAL_GPIO_ReadPin(GPIOC, GPIO_PIN_7);

	    if (!col1)
	    {
	        Lcd_clear(&lcd);
	        Lcd_string(&lcd, "1");
	        if (!opSelected)
	            num1 = num1 * 10 + 1;
	        else
	            num2 = num2 * 10 + 1;
	        HAL_Delay(300);
	    }
	    else if (!col2)
	    {
	        Lcd_clear(&lcd);
	        Lcd_string(&lcd, "2");
	        if (!opSelected)
	            num1 = num1 * 10 + 2;
	        else
	            num2 = num2 * 10 + 2;
	        HAL_Delay(300);
	    }
	    else if (!col3)
	    {
	        Lcd_clear(&lcd);
	        Lcd_string(&lcd, "3");
	        if (!opSelected)
	            num1 = num1 * 10 + 3;
	        else
	            num2 = num2 * 10 + 3;
	        HAL_Delay(300);
	    }
	    else if (!col4)
	    {
	        Lcd_clear(&lcd);
	        Lcd_string(&lcd, "-");
	        op = '-';
	        opSelected = true;
	        HAL_Delay(300);
	    }

	    HAL_GPIO_WritePin(GPIOC, GPIO_PIN_0, GPIO_PIN_SET);
	    HAL_GPIO_WritePin(GPIOC, GPIO_PIN_1, GPIO_PIN_SET);
	    HAL_GPIO_WritePin(GPIOC, GPIO_PIN_2, GPIO_PIN_SET);
	    HAL_GPIO_WritePin(GPIOC, GPIO_PIN_3, GPIO_PIN_RESET);

	    col1 = HAL_GPIO_ReadPin(GPIOC, GPIO_PIN_4);
	    col2 = HAL_GPIO_ReadPin(GPIOC, GPIO_PIN_5);
	    col3 = HAL_GPIO_ReadPin(GPIOC, GPIO_PIN_6);
	    col4 = HAL_GPIO_ReadPin(GPIOC, GPIO_PIN_7);

	    if (!col1) // ON -> clear/reset
	    {
	        Lcd_clear(&lcd);
	        num1 = 0;
	        num2 = 0;
	        op = 0;
	        opSelected = false;
	        resultDisplayed = false;
	        Lcd_string(&lcd, "Cleared");
	        HAL_Delay(300);
	    }
	    else if (!col2)
	    {
	        Lcd_clear(&lcd);
	        Lcd_string(&lcd, "0");
	        if (!opSelected)
	            num1 = num1 * 10;
	        else
	            num2 = num2 * 10;
	        HAL_Delay(300);
	    }
	    else if (!col3) // '=' pressed
	    {
	        int result = 0;
	        switch (op)
	        {
	        case '+': result = num1 + num2; break;
	        case '-': result = num1 - num2; break;
	        case '*': result = num1 * num2; break;
	        case '%': result = (num2 != 0) ? num1 / num2 : 0; break;
	        default: result = num1; break;
	        }

	        Lcd_clear(&lcd);
	        sprintf(buffer, "%d", result);
	        Lcd_string(&lcd, buffer);

	        num1 = result;
	        num2 = 0;
	        op = 0;
	        opSelected = false;
	        resultDisplayed = true;
	        HAL_Delay(500);
	    }
	    else if (!col4)
	    {
	        Lcd_clear(&lcd);
	        Lcd_string(&lcd, "+");
	        op = '+';
	        opSelected = true;
	        HAL_Delay(300);
	    }
}

/**
  * @brief System Clock Configuration
  * @retval None
  */
void SystemClock_Config(void)
{
  RCC_OscInitTypeDef RCC_OscInitStruct = {0};
  RCC_ClkInitTypeDef RCC_ClkInitStruct = {0};

  /** Configure the main internal regulator output voltage
  */
  __HAL_RCC_PWR_CLK_ENABLE();
  __HAL_PWR_VOLTAGESCALING_CONFIG(PWR_REGULATOR_VOLTAGE_SCALE2);

  /** Initializes the RCC Oscillators according to the specified parameters
  * in the RCC_OscInitTypeDef structure.
  */
  RCC_OscInitStruct.OscillatorType = RCC_OSCILLATORTYPE_HSI;
  RCC_OscInitStruct.HSIState = RCC_HSI_ON;
  RCC_OscInitStruct.HSICalibrationValue = RCC_HSICALIBRATION_DEFAULT;
  RCC_OscInitStruct.PLL.PLLState = RCC_PLL_NONE;
  if (HAL_RCC_OscConfig(&RCC_OscInitStruct) != HAL_OK)
  {
    Error_Handler();
  }

  /** Initializes the CPU, AHB and APB buses clocks
  */
  RCC_ClkInitStruct.ClockType = RCC_CLOCKTYPE_HCLK|RCC_CLOCKTYPE_SYSCLK
                              |RCC_CLOCKTYPE_PCLK1|RCC_CLOCKTYPE_PCLK2;
  RCC_ClkInitStruct.SYSCLKSource = RCC_SYSCLKSOURCE_HSI;
  RCC_ClkInitStruct.AHBCLKDivider = RCC_SYSCLK_DIV1;
  RCC_ClkInitStruct.APB1CLKDivider = RCC_HCLK_DIV1;
  RCC_ClkInitStruct.APB2CLKDivider = RCC_HCLK_DIV1;

  if (HAL_RCC_ClockConfig(&RCC_ClkInitStruct, FLASH_LATENCY_0) != HAL_OK)
  {
    Error_Handler();
  }
}

/**
  * @brief GPIO Initialization Function
  * @param None
  * @retval None
  */
static void MX_GPIO_Init(void)
{
  GPIO_InitTypeDef GPIO_InitStruct = {0};
/* USER CODE BEGIN MX_GPIO_Init_1 */
/* USER CODE END MX_GPIO_Init_1 */

  /* GPIO Ports Clock Enable */
  __HAL_RCC_GPIOC_CLK_ENABLE();
  __HAL_RCC_GPIOA_CLK_ENABLE();
  __HAL_RCC_GPIOB_CLK_ENABLE();

  /*Configure GPIO pin Output Level */
  HAL_GPIO_WritePin(GPIOC, GPIO_PIN_0|GPIO_PIN_1|GPIO_PIN_2|GPIO_PIN_3, GPIO_PIN_RESET);

  /*Configure GPIO pin Output Level */
  HAL_GPIO_WritePin(GPIOA, GPIO_PIN_0|GPIO_PIN_1|GPIO_PIN_2|GPIO_PIN_3, GPIO_PIN_RESET);

  /*Configure GPIO pin Output Level */
  HAL_GPIO_WritePin(GPIOB, GPIO_PIN_0|GPIO_PIN_1, GPIO_PIN_RESET);

  /*Configure GPIO pins : PC0 PC1 PC2 PC3 */
  GPIO_InitStruct.Pin = GPIO_PIN_0|GPIO_PIN_1|GPIO_PIN_2|GPIO_PIN_3;
  GPIO_InitStruct.Mode = GPIO_MODE_OUTPUT_PP;
  GPIO_InitStruct.Pull = GPIO_NOPULL;
  GPIO_InitStruct.Speed = GPIO_SPEED_FREQ_LOW;
  HAL_GPIO_Init(GPIOC, &GPIO_InitStruct);

  /*Configure GPIO pins : PA0 PA1 PA2 PA3 */
  GPIO_InitStruct.Pin = GPIO_PIN_0|GPIO_PIN_1|GPIO_PIN_2|GPIO_PIN_3;
  GPIO_InitStruct.Mode = GPIO_MODE_OUTPUT_PP;
  GPIO_InitStruct.Pull = GPIO_NOPULL;
  GPIO_InitStruct.Speed = GPIO_SPEED_FREQ_LOW;
  HAL_GPIO_Init(GPIOA, &GPIO_InitStruct);

  /*Configure GPIO pins : PC4 PC5 PC6 PC7 */
  GPIO_InitStruct.Pin = GPIO_PIN_4|GPIO_PIN_5|GPIO_PIN_6|GPIO_PIN_7;
  GPIO_InitStruct.Mode = GPIO_MODE_INPUT;
  GPIO_InitStruct.Pull = GPIO_PULLUP;
  HAL_GPIO_Init(GPIOC, &GPIO_InitStruct);

  /*Configure GPIO pins : PB0 PB1 */
  GPIO_InitStruct.Pin = GPIO_PIN_0|GPIO_PIN_1;
  GPIO_InitStruct.Mode = GPIO_MODE_OUTPUT_PP;
  GPIO_InitStruct.Pull = GPIO_NOPULL;
  GPIO_InitStruct.Speed = GPIO_SPEED_FREQ_LOW;
  HAL_GPIO_Init(GPIOB, &GPIO_InitStruct);

/* USER CODE BEGIN MX_GPIO_Init_2 */
/* USER CODE END MX_GPIO_Init_2 */
}

/* USER CODE BEGIN 4 */

/* USER CODE END 4 */

/**
  * @brief  This function is executed in case of error occurrence.
  * @retval None
  */
void Error_Handler(void)
{
  /* USER CODE BEGIN Error_Handler_Debug */
  /* User can add his own implementation to report the HAL error return state */
  __disable_irq();
  while (1)
  {
  }
  /* USER CODE END Error_Handler_Debug */
}

#ifdef  USE_FULL_ASSERT
/**
  * @brief  Reports the name of the source file and the source line number
  *         where the assert_param error has occurred.
  * @param  file: pointer to the source file name
  * @param  line: assert_param error line source number
  * @retval None
  */
void assert_failed(uint8_t *file, uint32_t line)
{
  /* USER CODE BEGIN 6 */
  /* User can add his own implementation to report the file name and line number,
     ex: printf("Wrong parameters value: file %s on line %d\r\n", file, line) */
  /* USER CODE END 6 */
}
#endif /* USE_FULL_ASSERT */
```
# OUTPUT

![04](https://github.com/user-attachments/assets/4682d04d-fb9c-44eb-b372-47ccb0e0f417)
![03](https://github.com/user-attachments/assets/74d7e95a-fbbd-4fd3-80be-13e52a5acb5d)
![01](https://github.com/user-attachments/assets/bab54172-35f3-4a8d-bc8b-d0bd666cbbbd)
![00](https://github.com/user-attachments/assets/f0d8ea86-34df-4479-bf88-54089b2c95a8)


# RESULT 
The project successfully interfaces a 4x4 matrix keypad with an LCD using the STM32 microcontroller.
