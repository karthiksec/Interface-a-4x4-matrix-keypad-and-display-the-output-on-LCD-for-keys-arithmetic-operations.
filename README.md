# Interface-a-4x4-matrix-keypad-and-display-the-output-on-LCD-for-keys-arithmetic-operations.



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

```
# OUTPUT


# RESULT 
The project successfully interfaces a 4x4 matrix keypad with an LCD using the STM32 microcontroller.
