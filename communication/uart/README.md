# UART

Custom UART transmitter and receiver for FPGA applications.

## Features

- 8N1 communication
- 3 Mbaud
- 27 MHz system clock
- Valid/ready handshake interfaces
- Independent transmitter and receiver modules
- Receiver overflow detection

## Architecture

The implementation consists of independent UART TX and RX modules.

The transmitter accepts a byte when both `TX_Valid` and `TX_Ready` are asserted. The byte is converted into an 8N1 UART frame and transmitted serially.

The receiver detects the UART start bit, samples the eight data bits and stores the completed byte. Received data remains valid until the downstream logic accepts it using the valid/ready handshake.

If another byte is received while the previous byte is still waiting to be accepted, `RX_Overflow` is asserted.

## Interface

### UART TX

- `TX_Data[7:0]` — byte to transmit
- `TX_Valid` — indicates valid input data
- `TX_Ready` — transmitter can accept a new byte
- `TX` — serial UART output

A transfer is accepted when:

`TX_Valid && TX_Ready`

### UART RX

- `RX` — serial UART input
- `RX_Data[7:0]` — received byte
- `RX_Valid` — received data is available
- `RX_Ready` — downstream logic can accept the byte
- `RX_Overflow` — a new byte was received before the previous byte was accepted

A received byte is consumed when:

`RX_Valid && RX_Ready`

## Timing

With a 27 MHz FPGA clock and a baud rate of 3 Mbaud:

- 9 FPGA clock cycles per UART bit
- 10 transmitted bits per byte using 8N1
- Maximum payload throughput: 2.4 Mbit/s

## Verification

Verification includes:

- Transmission and reception of all byte values from `0x00` to `0xFF`
- TX-to-RX loopback
- Back-to-back byte transmission
- Valid/ready backpressure
- RX overflow detection
