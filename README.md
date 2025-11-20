# UART-Controlled LED Indicator with LCD Echo (ATmega328P)

## 📌 Overview
This project demonstrates how to control an LED on an ATmega328P microcontroller using UART serial commands. The system listens to incoming characters via USART. When the user sends '`y`', the LED turns **ON**; when '`n`' is received, the LED turns **OFF**.

An attached **16×2 LCD** displays the received character for user feedback, and an additional LED is toggled periodically in the main loop to confirm the MCU is running.

This project is built using CodeVisionAVR libraries (`mega328p.h`, `alcd.h`) and uses interrupt-driven UART reception.

---

## ✨ Features
- **Interrupt-based UART reception**
- Controls LED using serial `'y'` or `'n'` commands
- Echoes received characters on a **16x2 LCD**
- Heartbeat LED blink to indicate the system is alive
- Configured for **double-speed UART mode (U2X0)**

---

## 🛠 Hardware Requirements
- ATmega328P microcontroller
- 16×2 alphanumeric LCD (HD44780-compatible)
- LED on **PORTD.5**
- Blinking status LED on **PORTC.0**
- USB-to-UART module (CP2102, CH340, FT232, etc.)
- 5V power supply

---

## 🔧 UART Settings
- **Baud Rate:** 9600 (UBRR0 = 0xCF with U2X0 = 1)
- **Data Bits:** 8  
- **Parity:** None  
- **Stop Bits:** 1  
- **Mode:** Asynchronous, double speed  
- **Receive interrupt:** Enabled  

---

## 📂 How It Works

### 1. **Main Loop**
- Initializes UART, LCD, I/O pins
- Blinks PORTC.0 every 50 ms (heartbeat)

### 2. **USART Receive Interrupt**
```c
if(temp == 'y') LED1(1);
if(temp == 'n') LED1(0);
lcd_gotoxy(0,0);
lcd_putchar(temp);


 ┌────────────────────────────┐
 │        Serial Device       │
 │ (PC / USB-UART Converter)  │
 └──────────────┬─────────────┘
                │ UART RX/TX
                │
 ┌──────────────▼─────────────┐
 │       ATmega328P MCU        │
 │ ┌─────────────────────────┐ │
 │ │   USART Receiver ISR    │ │
 │ │  - Reads incoming byte  │ │
 │ │  - 'y' → LED ON         │ │
 │ │  - 'n' → LED OFF        │ │
 │ └─────────────────────────┘ │
 │            │                 │
 │            │ Writes char     │
 │            ▼                 │
 │     16×2 LCD Display         │
 │   (Shows received data)      │
 │                              │
 │   PD5 ──────► LED Control    │
 │   PC0 ──────► Heartbeat LED  │
 └──────────────────────────────┘

