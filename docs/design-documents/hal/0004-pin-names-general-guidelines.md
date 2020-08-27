# General Pin Names design document

# Table of contents

1. [Table of contents](#table-of-contents).
    1. [Revision history](#revision-history).
1. [Introduction](#introduction).
    1. [Overview and assumptions](#overview-and-assumptions)
1. [Detailed design](#detailed-design).
    1. [LEDs](#leds).
    1. [Buttons](#buttons).
1. [Non-valid definitions](#non-valid-definitions).
1. [Testing compliance](#testing-compliance).


### Revision history

1.0 - Initial revision - Malavika Sajikumar, Marcelo Salazar - August 2020  
This document is written for Mbed OS 6.

## Introduction

### Overview and assumptions

Mbed OS is designed so that application code written in the platform is portable across different Mbed supported boards with the same hardware capabilities or interfaces. However, the code may not be truly portable due to the differences in pin name definitions for the same kind of interfaces across different boards. 

This document provides general guidelines and best practices for defining pin names that could apply to all boards but it's not specific to any type of connector.

Note there might be separate documents for pin names that apply to specific connectors such as the Arduino Uno. These would be avaialble in the [HAL design documents](./) folder.



## Detailed design

To achieve meaningful portability of application code across various Mbed Enabled boards, certain pin names of commonly used interfaces and board components should be common across these boards. This document describes a set of rules on how to name generic pins in the board support package, specifically in PinNames.h file of the board.

### LEDs

**Definition of LEDs**

Only add LEDs that are available in the board. This is an example on how to define LEDs in PinNames.h:

    // Px_xx relates to the processor pin connected to the LED
    
    #define LED1 = Px_xx  // LED1
    #define LED2 = Px_xx  // LED2  
    #define LED3 = Px_xx  // LED3  
    #define LED4 = Px_xx  // LED4  
    .  
    .  
    #define LEDN = Px_xx   // LEDN

**Using LEDs at application**

The detection of available LEDs at application level can be done as follow:

    #ifdef(LED1)
        DigitalOut myLED(LED1);
        myLED = 1;
    #endif 

Alternatively, if the usage of an LED is required, then the application can detect its availability generate an error accordingly:

    #ifndef(LED1)
        #error This application requires usage of an LED
    #endif 


It's possible to define new names of LEDs related its properties, like color or functionality. They can be defined as aliases at application level as shown below:

    #define RED_LED    LED1
    #define STATUS_LED LED2 

However, these names do not apply to all boards and hence should not be used in official example applications that are considered platform agnostic.

### Buttons

**Definition of Buttons**

Only add buttons that are available in the board. This is an example on how to define buttons in PinNames.h:

    // Px_xx relates to the processor pin connected to the Button  
    #define BUTTON1 = Px_xx  // BUTTON1  
    #define BUTTON2 = Px_xx  // BUTTON2  
    #define BUTTON3 = Px_xx  // BUTTON3  
    #define BUTTON4 = Px_xx  // BUTTON4   
    .  
    .  
    #define BUTTONN = Px_xx   // BUTTONN  

**Using Buttons at application**

The detection of available Buttons at application level can be done as follow:

    #ifdef(BUTTON1)
        DigitalIn myBUTTON(BUTTON1);
        int input = myBUTTON.read();
    #endif 

Alternatively, if the usage of a BUTTON is required, then the application can detect its availability generate an error accordingly:

    #ifndef(BUTTON1)
        #error This application requires usage of a BUTTON
    #endif 

It's possible to define new names of BUTTONs related its properties with corresponding comments. They can be defined as aliases at application level as shown below:

    #define PUSH_BUTTON BUTTON1 // Momentary push Button

However, these names do not apply to all boards and hence should not be used in official example applications that are considered platform agnostic.

### UART

Every Mbed board includes a serial interface to the host PC, which allows the console to print useful information about the application status as well to perform basis debug tasks.

This is also a requirement to run automated tests using Greentea, as the communication between the host PC and the MCU is done over serial.

This is an example on how to define UART names in PinNames.h:

    USBTX       = PB_6,
    USBRX       = PB_7,

Note Mbed OS expects to use these names internally (a fix might be needed), for example:

    mbed-os/platform/source/mbed_retarget.cpp
    mbed-os/hal/static_pinmap.h
    mbed-os/hal/mbed_pinmap_default.cpp

### Non-valid definitions

If either LEDs or BUTTONs are not available, then should not be defined.
This allows for unavailable LEDs or BUTTONs to be caught and generate the corresponding error.
   
    LED1 = PB_0,       // LED1 is valid
    LED2 = LED1,       // Not valid as it's duplicate  
    LED3 = PB_0,       // Not valid as it's duplicate 
    LED4 = NC          // Not valid definition as LED4 does not exist

    BUTTON1 = PB_1,    // BUTTON1 is valid
    BUTTON2 = BUTTON1, // Not valid as it's duplicate  
    BUTTON3 = PB_1,    // Not valid as it's duplicate  
    BUTTON4 = NC,      // Not valid as BUTTON4 doesn't exist 


### Testing compliance

There should be both compile and run time checks to confirm whether a board has valid LEDs and BUTTONS defined. This can be achieved by using Greentea, for example:

    mbed test -t <toolchain> -m <target> -n *test_generic_pin_names* --compile
    mbed test -t <toolchain> -m <target> -n *test_generic_pin_names* --run

Note the testing of UART is implicit when running Greentea tests.

It's not mandatory to add LEDs or BUTTONs if they don't exist in the board.
If they are defined but contain no valid pinnames, then a warning should be displayed.

In a new version of Mbed OS, the tests should be enabled to check compliance and generate errors when non-valid LEDs are being used.
