# EXP
CAT 10

# DATE
05/10/2026

# NAME OF EXP
STM32-Wemos Wi-Fi Modem Interface Using AT Commands

# HARDWARE SETUP / PLATFORM

**Tasks 1, 2 and 3:**
- Development Board: STM32 Blue Pill
- Microcontroller: STM32F103C8T6
- Processor Core: ARM Cortex-M3
- Wi-Fi Module: Wemos D1 (ESP8266)
- Communication: UART using STM32 HAL libraries
- PC Interface: Arduino IDE Serial Monitor

**Task 4:**
- Development Board: NUCLEO-F410RB
- Microcontroller: STM32F410RBT6
- Processor Core: ARM Cortex-M4
- Wi-Fi Module: Wemos D1 (ESP8266)
- Communication: UART using STM32 HAL libraries
- PC Interface: Arduino IDE Serial Monitor through USART 2

**UART Configuration used in the supplied programs:**
- USART1: STM32–Wemos communication.
- USART2: STM32–PC communication.
- Baud rate: 115200
- Word length: 8 bits
- Stop bits: 1
- Parity: None
- Hardware flow control: None

# TASK NO
- Task 1 – Basic AT Command Communication
- Task 2 – Wi-Fi Configuration and Connection
- Task 3 – TCP Server Configuration
- Task 4 – HTTP Request Processing and Response

# CIRCUIT DIAGRAM

The circuit diagrams are provided separately in the respective task folders because the development board used in Task 4 differs from the board used in Tasks 1–3.

- Tasks 1–3: STM32 Blue Pill with Wemos D1.
- Task 4: NUCLEO-F410RB with Wemos D1.

# CORE LOGIC

The experiment progressively develops UART-based communication between an STM32 microcontroller and an ESP8266 Wi-Fi module.

1. Establish UART communication between STM32 and Wemos using AT commands.
2. Receive responses from Wemos and forward them to the PC serial terminal.
3. Configure the ESP8266 in station mode and connect it to a Wi-Fi access point.
4. Retrieve the network IP information.
5. Enable multiple TCP connections and configure the ESP8266 as a TCP server on port 80.
6. Receive HTTP request data from the ESP8266 through `+IPD` notifications.
7. Extract the TCP connection ID and HTTP request payload.
8. Construct an HTTP response containing the status line, headers and HTML body.
9. Transmit the response through `AT+CIPSEND`.
10. Close the TCP connection after transmission.

# TASK 1 – BASIC UART COMMUNICATION

## Core Logic
- Initialize HAL, system clock, GPIO, USART1 and USART2.
- Send the `AT` command to the ESP8266 through USART1.
- Receive the ESP8266 response one byte at a time.
- Forward each received byte to the PC through USART2.
- Wait for the response collection timeout before proceeding.

## Pseudocode
1. Initialize HAL.
2. Configure the system clock.
3. Initialize GPIO, USART1 and USART2.
4. Declare the AT command `AT\r\n`.
5. Transmit the AT command through USART1.
6. Start receiving bytes from USART1.
7. For every successfully received byte, transmit it through USART2.
8. Continue receiving until the configured timeout expires.
9. Wait for a short delay.
10. Keep the microcontroller running.

## Code Base
- `HAL_UART_Transmit()` – Sends the AT command.
- `HAL_UART_Receive()` – Receives the ESP8266 response.
- USART1 – STM32–Wemos communication.
- USART2 – STM32–PC communication.

# TASK 2 – WI-FI CONFIGURATION AND CONNECTION

## Core Logic
- Initialize the STM32 peripherals.
- Send AT commands to configure the ESP8266 as a Wi-Fi station.
- Enable DHCP for station mode.
- Send the Wi-Fi credentials to connect to the access point.
- Wait for the `WIFI GOT IP` and `OK` responses.
- Retrieve the IP information using `AT+CIFSR`.
- Forward the received responses to the PC terminal.

## Pseudocode
1. Initialize HAL, system clock, GPIO, USART1 and USART2.
2. Declare the required AT commands:
   - `AT`
   - `AT+CWMODE=1`
   - `AT+CWDHCP=1,1`
   - `AT+CWJAP`
   - `AT+CIFSR`
3. Transmit `AT` and collect the response.
4. Transmit `AT+CWMODE=1` and collect the response.
5. Transmit `AT+CWDHCP=1,1` and collect the response.
6. Transmit the Wi-Fi connection command.
7. Receive the response for up to 5000 ms.
8. Check the received data for:
   - `WIFI GOT IP`
   - `OK`
   - `FAIL` or `ERROR`
9. Stop waiting when both successful connection indications are received, an error occurs, or the timeout expires.
10. Forward the collected response to the PC.
11. Transmit `AT+CIFSR` and collect the IP information.
12. Keep the microcontroller running.

## Code Base
- `HAL_UART_Transmit()` – Sends AT commands.
- `HAL_UART_Receive()` – Receives ESP8266 responses.
- `HAL_GetTick()` – Measures elapsed time.
- `strstr()` – Searches for connection status messages.
- `HAL_UART_Transmit()` through USART2 – Displays responses on the PC.

# TASK 3 – TCP SERVER CONFIGURATION

## Core Logic
- Perform the initialization and Wi-Fi connection steps from Task 2.
- Enable multiple TCP connections using `AT+CIPMUX=1`.
- Start a TCP server on port 80 using `AT+CIPSERVER=1,80`.
- Collect and forward the ESP8266 responses to the PC.
- Keep the microcontroller running after server configuration.

## Pseudocode
1. Initialize HAL, system clock, GPIO, USART1 and USART2.
2. Declare the AT commands for:
   - Communication check: `AT`
   - Station mode: `AT+CWMODE=1`
   - DHCP configuration: `AT+CWDHCP=1,1`
   - Wi-Fi connection: `AT+CWJAP`
   - IP information: `AT+CIFSR`
   - Multiple connections: `AT+CIPMUX=1`
   - TCP server: `AT+CIPSERVER=1,80`
3. Send each command through USART1.
4. Receive the ESP8266 response until the configured timeout expires.
5. Forward each collected response to the PC through USART2.
6. Wait for the required delay between commands.
7. After connecting to Wi-Fi and retrieving IP information, send `AT+CIPMUX=1`.
8. Send `AT+CIPSERVER=1,80`.
9. Display the collected responses on the PC.
10. Keep the microcontroller running.

## Code Base
- `HAL_UART_Transmit()` – Sends ESP8266 AT commands.
- `HAL_UART_Receive()` – Collects ESP8266 responses.
- `HAL_Delay()` – Provides delays between commands.
- `HAL_GetTick()` – Supports timeout handling.

# TASK 4 – HTTP REQUEST PROCESSING AND RESPONSE

## Core Logic
- Initialize the NUCLEO-F410RB peripherals and UART communication.
- Configure the ESP8266 in station mode and connect it to the Wi-Fi access point.
- Enable multiple TCP connections and start the TCP server on port 80.
- Continuously receive data from the ESP8266.
- Forward received data to the PC.
- Detect `+IPD` frames and extract the connection ID and payload length.
- Identify incoming HTTP GET or POST requests.
- Construct a static HTTP response with HTML content.
- Send the response using the two-step `AT+CIPSEND` procedure.
- Wait for the `>` prompt before transmitting the response.
- Wait for the `SEND OK` acknowledgement or an error.
- Close the TCP connection using `AT+CIPCLOSE`.

## Pseudocode

**Step 1 – Initialize and configure the ESP8266**
1. Initialize HAL, system clock, GPIO, USART1 and USART2.
2. Wait for the ESP8266 to become ready.
3. Send `AT` to verify communication.
4. Send `AT+CWMODE=1` to configure station mode.
5. Send `AT+CWDHCP=1,1` to enable DHCP.
6. Send the Wi-Fi connection command.
7. Wait for `WIFI GOT IP` and `OK`, or stop if an error or timeout occurs.
8. Send `AT+CIFSR` to retrieve IP information.
9. Send `AT+CIPMUX=1` to enable multiple TCP connections.
10. Send `AT+CIPSERVER=1,80` to start the TCP server.

**Step 2 – Receive and process HTTP requests**
1. Continuously receive bytes from USART1.
2. Store the received bytes in a buffer.
3. Stop collecting when the reception timeout expires, no new byte arrives for 200 ms, or the buffer becomes full.
4. If data is available, forward it to the PC through USART2.
5. Search the buffer for the `+IPD,` marker.
6. If the marker is absent, return.
7. Extract the TCP connection ID.
8. Extract the incoming data length.
9. Verify that the declared payload is available in the buffer.
10. Check whether the payload starts with `GET ` or `POST `.
11. If an HTTP request is detected, call the HTTP response function with the extracted connection ID.

**Step 3 – Construct and send the HTTP response**
1. Define the HTML body containing a heading and paragraph.
2. Calculate the HTML body length.
3. Construct the HTTP response with:
   - `HTTP/1.1 200 OK`
   - `Content-Type: text/html`
   - `Content-Length`
   - `Connection: close`
   - A blank line
   - The HTML body
4. Calculate the complete response length.
5. Construct the command `AT+CIPSEND=<connection ID>,<response length>`.
6. Transmit the CIPSEND command through USART1.
7. Wait up to 5000 ms for the `>` prompt.
8. If the prompt is not received, return without transmitting the response.
9. If the prompt is received, transmit the complete HTTP response.
10. Wait up to 3000 ms for `SEND OK` or `ERROR`.
11. Send `AT+CIPCLOSE=<connection ID>` to close the TCP connection.

## Code Base
- `HAL_UART_Transmit()` – Transmits AT commands and HTTP response data.
- `HAL_UART_Receive()` – Receives ESP8266 responses and incoming network data.
- `HAL_GetTick()` – Measures timeouts.
- `HAL_Delay()` – Provides delays during initialization.
- `strlen()` – Calculates string length.
- `snprintf()` – Constructs AT commands and the HTTP response.
- `strstr()` – Searches for `+IPD`, `WIFI GOT IP`, `OK`, `SEND OK` and error messages.
- `strncmp()` – Identifies HTTP GET and POST requests.
- `memset()` – Clears the Wi-Fi connection response buffer.

# INFERENCE

The experiment demonstrates the progressive development of an STM32-based Wi-Fi communication system using the ESP8266 module.

Tasks 1–3 establish UART communication, configure Wi-Fi connectivity and start a TCP server. Task 4 extends this functionality by processing incoming HTTP requests, constructing an HTTP response and transmitting it to the browser through the ESP8266.

The experiment demonstrates UART communication, ESP8266 AT command control, Wi-Fi station configuration, TCP server operation, `+IPD` frame processing and HTTP response transmission.

The implemented Task 4 returns a static HTML response. Sensor acquisition and dynamic sensor-data transmission are not implemented in the supplied code. Node-RED integration is specified as a separate subsequent task.