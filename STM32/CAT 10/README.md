# EXP: CAT3

**DATE:** 05/10/2026

**NAME OF EXP:** STM32-Wemos Wi-Fi Modem Interface Using AT Commands

**HARDWARE SETUP / PLATFORM:**
- STM32 Blue Pill Development Board (STM32F103C8T6)
- Wemos D1 Mini (ESP8266 Wi-Fi Module)
- PC with PuTTY/Tera Term
- USB-to-TTL converter for PC communication

## TASK NO

- **Task1:** Establish STM32–Wemos UART Communication
- **Task2:** Explore the ESP8266 AT Command Interface
- **Task3:** Connect Wemos to Wi-Fi

## Circuit Diagram

![Circuit Diagram](circuit.png)

## Core Logic / Pseudocode / Code Base

## Core Logic / Pseudocode / Code Base

### Task1 – Establish STM32–Wemos UART Communication

**Core Logic:**

STM32 sends the `AT` command to the ESP8266 through USART1. The modem response is received one character at a time and forwarded to the PC terminal through USART2.

**Pseudocode:**

```text
START

Initialize HAL, system clock and GPIO
Initialize USART1 and USART2

Transmit "AT\r\n" through USART1

REPEAT
    Receive one character from Wemos through USART1

    IF reception is successful THEN
        Forward character to PC through USART2
    ELSE
        Stop receiving
    END IF
UNTIL reception times out

Wait for 500 milliseconds
Enter an infinite loop

END
```

**Code Base:**

- `HAL_UART_Transmit()` sends the AT command and forwards received characters.
- `HAL_UART_Receive()` receives the modem response.
- USART1 handles Wemos communication.
- USART2 handles PC terminal communication.

### Task2 – Explore the ESP8266 AT Command Interface

**Core Logic:**

The STM32 sends a sequence of AT commands to configure the ESP8266 in station mode, enable DHCP, connect to a Wi-Fi access point and obtain IP address information. The response to each command is forwarded to the PC terminal.

**Pseudocode:**

```text
START

Initialize HAL, system clock and GPIO
Initialize USART1 and USART2

Define AT commands:
    AT
    AT+CWMODE=1
    AT+CWDHCP=1,1
    AT+CWJAP with Wi-Fi credentials
    AT+CIFSR

FOR each command DO
    Transmit command through USART1

    Set reception timeout
    Use 5000 milliseconds for Wi-Fi connection
    Use 100 milliseconds for other commands

    REPEAT
        Receive one character through USART1

        IF reception succeeds THEN
            Forward character through USART2
        ELSE
            Stop receiving
        END IF
    UNTIL timeout

    Wait for 500 milliseconds
END FOR

Enter an infinite loop

END
```

**Code Base:**

- `HAL_UART_Transmit()` sends the AT commands.
- `HAL_UART_Receive()` receives modem responses.
- `strlen()` determines command string lengths.
- `HAL_Delay()` introduces delays between commands.
- USART1 and USART2 handle modem and PC communication respectively.

### Task3 – Connect Wemos to Wi-Fi

**Core Logic:**

The STM32 configures the ESP8266 as a Wi-Fi station, enables DHCP, connects it to the specified access point and obtains its IP address. It then enables multiple connections and starts an HTTP server on port 80. All modem responses are forwarded to the PC terminal.

**Pseudocode:**

```text
START

Initialize HAL, system clock and GPIO
Initialize USART1 and USART2

Define the required AT commands

Transmit AT
Receive and forward modem response
Wait for 500 milliseconds

Transmit AT+CWMODE=1
Receive and forward modem response
Wait for 500 milliseconds

Transmit AT+CWDHCP=1,1
Receive and forward modem response
Wait for 500 milliseconds

Transmit AT+CWJAP with Wi-Fi credentials
Receive and forward modem response
Use 5000 milliseconds timeout
Wait for 500 milliseconds

Transmit AT+CIFSR
Receive and forward modem response
Wait for 500 milliseconds

Transmit AT+CIPMUX=1
Receive and forward modem response
Wait for 500 milliseconds

Transmit AT+CIPSERVER=1,80
Receive and forward modem response
Wait for 500 milliseconds

Enter an infinite loop

END
```

**Code Base:**

- `HAL_UART_Transmit()` sends commands to the ESP8266.
- `HAL_UART_Receive()` receives the modem responses.
- `strlen()` determines the number of bytes in command strings.
- `HAL_Delay()` introduces delays between successive commands.
- USART1 provides STM32–Wemos communication.
- USART2 forwards modem responses to the PC terminal.

## INFERENCE

Established UART communication between STM32 and the Wemos ESP8266 module. Explored the ESP8266 AT command interface and configured the module in station mode. Enabled DHCP, connected the module to a Wi-Fi access point, obtained IP address information, enabled multiple connections and configured an HTTP server on port 80. The modem responses were forwarded to the PC terminal through STM32.