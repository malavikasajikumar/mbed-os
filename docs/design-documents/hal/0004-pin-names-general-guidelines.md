# Pin Names design document

# Table of contents

1. [Table of contents](#table-of-contents).
    * [Revision history](#revision-history).
1. [Introduction](#introduction).
    1. [Overview and background](#overview-and-background).
    1. [Requirements and assumptions](#requirements-and-assumptions).
1. [Detailed design](#detailed-design).
    1. [Arduino Uno (Rev 3) Header Pins](#arduino-uno-rev-3-header-pins).
        1. [I2C Definition for Arduino Uno (Rev3) Pins](#i2c-definition-for-arduino-uno-rev3-pins)
        1. [SPI Definition for Arduino Uno (Rev3) Pins](#spi-definition-for-arduino-uno-rev3-pins)
    1. [Custom/Vendor Header Pins](#custom/vendor-header-pins).
        1. [Custom/Vendor Connector I2C Definition](#custom/vendor-connector-i2c-definition).
        1. [Custom/Vendor Connector SPI Definition](#custom/vendor-connector-spi-definition).
    1. [Board Components Pins](#board-components-pins).
        1. [LED Definition](#led-definition)
        1. [BUTTON Definition](#button-definition)
1. [Other information](#other-information).
    1. [Avoid Unnecessary Definitions](#avoid-unnecessary-definitions).


### Revision history

1.0 - Initial revision - Malavika Sajikumar - 3/5/2020.  
This document was written for Mbed OS version 5.15 (released on 11th of February 2020).

# Introduction

### Overview and background

Mbed is designed so that application code written in the platform is portable across different Mbed supported boards with the same hardware capabilities or interfaces. However, the code, in most cases, is not truly portable due to the differences in pin name definitions for the same kind of interfaces across different boards. 

This document provides guidelines and best practices for defining pin names that apply to all connectors without making references to any of them.

Note there might be separate documents for pin names that apply to specific connectors such as the Arduino Uno. 

### Requirements and assumptions

This document mainly applies to all connectors and defines best practices for defining pin-names.

# Detailed design

~~To achieve meaningful portability of application code across various Mbed enabled boards, the pin names of commonly used interfaces and board components should be common across these boards. This document describes a set of rules on how to name these pins in the board support package, specifically in PinNames.h file of the board.~~


### Board Components Pins

>>Modifications from previous version: #defines for LEDs,BUTTONs. Decision on NC should be used or not. 
>>Does this make sense or should we stick to Kevin's suggestion? Should the code be able to catch:
static_assert(BUTTON1 != NC && BUTTON2 != NC, "This application needs two buttons");
>> [MS] This makes sense. AP: Need to understand implications.

>>If so, how many LEDs/BUTTONs would you define?
>> [MS] AP: define mininum number of LEDs/BUTTONs

**LED Definition**

All LED definitions should be defined as follows in PinNames.h:

    // Px_xx relates to the processor pin connected to the LED
    
    #define LED1 = Px_xx  // LED1
    #define LED2 = Px_xx  // LED2  
    #define LED3 = Px_xx  // LED3  
    #define LED4 = Px_xx  // LED4  
    .  
    .  
    #define LEDN = Px_xx   // LEDN

>>Should be in application code
>> [MS] Need more context on this comment.

A comment describing more LED properties, like color or functionality can also be provided. If color descriptive names are required, they can be defined as aliases, as shown below:

    #define RED_LED = LED1
    #define STATUS_LED = LED2

However, these names do not apply to all boards and hence should not be used in example applications that are considered platform agnostic.

**BUTTON Definition**

All BUTTON definitions should be defined as follows in PinNames.h:

    // Px_xx relates to the processor pin connected to the Button  
    #define BUTTON1 = Px_xx  // BUTTON1  
    #define BUTTON2 = Px_xx  // BUTTON2  
    #define BUTTON3 = Px_xx  // BUTTON3  
    #define BUTTON4 = Px_xx  // BUTTON4   
    .  
    .  
    #define BUTTONN = Px_xx   // BUTTONN  

A comment describing more BUTTON properties can also be provided. If descriptive names are required, they can be defined as aliases, as shown below:

    #define RESET_BUTTON = BUTTON1

# Other information

### Avoid Unnecessary Definitions

If only one LED/BUTTON is on board, do not define additional LEDs/BUTTONs. This allows for unavailable LEDs/BUTTONs to be caught as a compile error.

    // Do not do the following
    
    #define LED1 = Px_xx,  // LED1  
    #define LED2 = LED1,   // LED2  
    #define LED3 = LED1,   // LED3  
    #define LED4 = LED1,   // LED4   
    
    #define BUTTON1 = Px_xx,     // BUTTON1  
    #define BUTTON2 = BUTTON1,   // BUTTON2  
    #define BUTTON3 = BUTTON1,   // BUTTON3  
    #define BUTTON4 = BUTTON1,   // BUTTON4  
