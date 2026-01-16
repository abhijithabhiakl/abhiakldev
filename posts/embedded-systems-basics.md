---
title: Basics of Embedded Systems
slug: embedded-systems-basics
description: >-
  An introduction to embedded systems, perfect for beginners in hardware development.
tags:
  - technical
  - embedded
  - c
  - arduino
added: "May 31 2024"
---

## Introduction

Embedded systems are specialized computing systems that perform dedicated functions within larger mechanical or electrical systems. Unlike general-purpose computers that can run any software, embedded systems are designed for specific tasks with real-time computing constraints. They're everywhere—from the microwave in your kitchen to the engine control unit in your car.

This guide will take you from the basics to hands-on programming with real examples using AVR microcontrollers. By the end, you'll understand not just _what_ embedded systems are, but _how_ to build them.

## What is an Embedded System?

An embedded system is a combination of hardware and software designed to perform a particular function. The key characteristics are:

- **Dedicated Function:** Unlike a PC, it does one thing very well
- **Real-time Operation:** Must respond to inputs within strict time constraints
- **Resource Constrained:** Limited memory, processing power, and energy
- **Reliability:** Often must operate for years without failure

## Understanding Microcontrollers

At the heart of most embedded systems is a **microcontroller (MCU)**—a complete computer on a single chip. Unlike microprocessors (like those in your laptop), microcontrollers integrate:

- **CPU:** The processing unit that executes instructions
- **Memory:** Flash (program storage), SRAM (data), and EEPROM (persistent data)
- **Peripherals:** GPIO pins, timers, ADC, USART, I2C, SPI, etc.
- **Clock:** Timing circuitry (typically 1-20 MHz for simple MCUs)

For this guide, I'll use the **ATmega328P** (the chip on Arduino Uno) as our example. It has 32KB Flash, 2KB SRAM, and runs at 16 MHz.

## Your First Program: LED Blink

The "Hello World" of embedded systems is blinking an LED. Here's a complete example:

```c
#include <avr/io.h>        // Hardware register definitions
#include <util/delay.h>    // Delay functions

int main(void) {
    // Set PD5 (pin 13 on Arduino) as output
    DDRD = 0b11111111;     // Data Direction Register D: all outputs
    
    while (1) {            // Infinite loop
        PORTD |= (1 << DDD5);   // Turn LED ON (set bit 5)
        _delay_ms(500);         // Wait 500ms
        PORTD &= ~(1 << DDD5);  // Turn LED OFF (clear bit 5)
        _delay_ms(500);         // Wait 500ms
    }
}
```

### Understanding the Code

**Registers:** Microcontrollers are programmed by writing to hardware registers. Key registers for GPIO:

- `DDRx` (Data Direction Register): Configures pins as input (0) or output (1)
- `PORTx`: Sets output value (HIGH/LOW) or enables pull-up resistors for inputs
- `PINx`: Reads the current state of input pins

**Bit Manipulation:** We use bitwise operations to control individual pins:

- `(1 << DDD5)` creates a bitmask with only bit 5 set
- `|=` sets a bit to 1 (OR operation)
- `&= ~` clears a bit to 0 (AND with inverted mask)

## More Advanced: Cyclone LED Effect

Let's create a "Knight Rider" style LED effect that sweeps back and forth:

```c
#include <avr/io.h>
#include <util/delay.h>

#define DELAYTIME 200

int main(void) {
    uint8_t i;
    DDRD = 0xff;           // All PORTD pins as output
    
    while (1) {
        // Sweep forward
        for (i = 0; i < 8; i++) {
            PORTD = (1 << i);     // Light only i'th LED
            _delay_ms(DELAYTIME);
        }
        // Sweep backward
        for (i = 7; i > 0; i--) {
            PORTD = (1 << i);
            _delay_ms(DELAYTIME);
        }
    }
}
```

## Serial Communication (USART)

Real embedded systems need to communicate. The **USART** (Universal Synchronous/Asynchronous Receiver/Transmitter) enables serial communication with computers, sensors, or other devices.

### USART Basics

Serial communication sends data one bit at a time over a wire. Key parameters:

- **Baud Rate:** Bits per second (common: 9600, 115200)
- **Data Bits:** Usually 8 bits per character
- **Parity:** Error checking (usually none)
- **Stop Bits:** End-of-character marker (usually 1)

### Serial Loopback Example

This program receives characters over serial and echoes them back, displaying the binary representation:

```c
#include <avr/io.h>
#include "USART.h"

void printByteBinary(uint8_t byte) {
    for (int8_t i = 7; i >= 0; i--) {
        if (byte & (1 << i)) {
            transmitByte('1');
        } else {
            transmitByte('0');
        }
    }
}

int main(void) {
    char serialCharacter;
    
    // GPIO Setup
    DDRD = 0b11111100;     // PD2-PD7 as outputs (LEDs)
    DDRB = 0b00000011;     // PB0-PB1 as outputs
    
    // Initialize USART
    initUSART();
    printString("Hello World!\r\n");
    
    while (1) {
        serialCharacter = receiveByte();     // Wait for input
        
        printString("char: ");
        transmitByte(serialCharacter);
        
        printString("  binary: ");
        printByteBinary(serialCharacter);
        printString("\r\n");
        
        // Display on LEDs
        PORTD = (PORTD & 0x03) | (serialCharacter & 0xFC);
        PORTB = (PORTB & 0xFC) | (serialCharacter & 0x03);
    }
}
```

## Professional Project Structure

As projects grow, dumping everything into one folder becomes unmanageable. I recommend a structured approach found in professional environments. Here is the structure I use for my AVR projects:

```bash
avr/
├── toolchain/
│   └── Makefile.common   # Shared configuration (MCU, F_CPU, Programmer)
│
├── projects/
│   ├── blink/            # Individual Project
│   │   ├── src/          # Source files (.c)
│   │   └── makefile      # Project-specific build rules
│   └── ...
│
└── build/                # Global build directory (keeps source clean)
```

## The Magic of Makefiles

Typing long `avr-gcc` commands manually is error-prone and tedious. A **Makefile** automates this. It abstracts complex compilation flags and dependency tracking into simple commands.

Here is the actual **Makefile** from my `blink` project. It leverages a shared configuration to keep things clean:

```makefile
# =================================================
# Repository root (relative to projects/<name>/)
# =================================================
REPO_ROOT := ../..

include $(REPO_ROOT)/toolchain/Makefile.common

# =================================================
# Project identity
# =================================================
TARGET := blink
SRC    := src/main.c

# =================================================
# Global build dir (ONE build/ at repo root)
# =================================================
BUILD := $(ROOT_BUILD)/$(TARGET)

ELF := $(BUILD)/$(TARGET).elf
HEX := $(BUILD)/$(TARGET).hex

# ... (Standard Phony Targets) ...

# =================================================
# Default
# =================================================
all: $(HEX)

# =================================================
# Flash / verify
# =================================================
flash: $(HEX)
	@$(AVRDUDE) -c $(PROG) -p $(PART) -U flash:w:$<:i $(AVRDUDE_OPTS)

# =================================================
# Cleanup (PROJECT ONLY)
# =================================================
clean:
	@rm -rf $(BUILD)

# =================================================
# Composite workflows
# =================================================
upload: all size device fuse flash verify
	@echo "==> Build and upload completed"

qupload: all flash verify
	@echo "==> Quick upload completed"
```

## My Development Workflow

With this setup, the "Edit → Compile → Flash" loop becomes seamless:

1. **`make`**: Compiles the source in `src/` and generates artifacts in `build/`.
2. **`make flash`**: Writes the confirmed `.hex` file to the microcontroller.
3. **`make qupload`**: A custom target for rapid iteration (Build + Flash + Verify).
4. **`make clean`**: Wipes the `build/` directory to ensure a fresh start.

## Key Concepts to Master

### 1. Memory Architecture
- **Flash:** Non-volatile, stores your program (32KB on ATmega328P)
- **SRAM:** Volatile, stores variables during runtime (2KB)
- **EEPROM:** Non-volatile, stores data that persists across resets (1KB)

### 2. Interrupts

Instead of constantly checking (polling), interrupts let hardware "interrupt" your program when something happens (button press, timer overflow, serial data received).

### 3. Timers and PWM

Hardware timers count clock cycles and can trigger interrupts or generate PWM signals for motor control, LED dimming, etc.

### 4. Communication Protocols
- **USART:** Serial communication (2 wires: TX, RX)
- **I2C:** Multi-device bus (2 wires: SDA, SCL)
- **SPI:** High-speed serial (4 wires: MOSI, MISO, SCK, SS)

## Getting Started with Your Own Projects

### Hardware You'll Need
- Arduino Uno or bare ATmega328P chip
- USB cable for programming
- Breadboard and jumper wires
- LEDs, resistors, buttons
- Optional: sensors (temperature, light, motion)

### Software Setup
1. Install `avr-gcc` (compiler)
2. Install `avrdude` (programmer)
3. Install `make` (build automation)
4. Optional: `minicom` or `screen` for serial monitoring

### Project Ideas for Learning
1. **LED Blink:** Basic GPIO output
2. **Button Input:** Read GPIO inputs with debouncing
3. **Serial Echo:** USART communication basics
4. **PWM LED Dimmer:** Analog output using timers
5. **Temperature Monitor:** ADC and sensor reading
6. **I2C Scanner:** Detect devices on I2C bus
7. **Real-time Clock:** Timer interrupts for timekeeping

## Common Pitfalls and Tips
- **Forgot to set DDR:** Always configure pin direction before use
- **Wrong baud rate:** Transmitter and receiver must match exactly
- **Floating inputs:** Use pull-up/pull-down resistors for buttons
- **Blocking delays:** `_delay_ms()` freezes your program—use timers for multitasking
- **Power issues:** Ensure stable 5V supply and proper grounding

## Resources for Further Learning
- **Datasheets:** ATmega328P datasheet is your bible—learn to read it
- **AVR Libc Manual:** Documentation for all AVR C library functions
- **Make: AVR Programming** by Elliot Williams (excellent book)
- **Practice:** Build projects, break things, learn from mistakes

## Conclusion

Embedded systems programming is both challenging and rewarding. You're working at the intersection of hardware and software, where every byte of memory and every clock cycle matters. Start with simple projects like LED blink, gradually add complexity (buttons, serial communication, sensors), and before you know it, you'll be building sophisticated IoT devices and robots.

The key is to **get your hands dirty**—read datasheets, write code, flash it to hardware, debug when it doesn't work, and celebrate when it does. That's how you truly learn embedded systems.

**Want to discuss embedded systems or need help with a project?** Reach out to me via [email](mailto:hi@abhiakl.xyz) or connect on my social links on the main page!
