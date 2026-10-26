# SPI Master

Generic SPI master for FPGA applications.

## Features

- 27 MHz system clock
- Configurable transfer length
- Valid/ready handshake interface
- Separate transmit and receive shift registers
- SPI clock generated from the system clock
- Internal logic remains in a single clock domain

## Architecture

The SPI master consists of a transmit shift register, a receive shift register, a clock divider and transfer control logic.

Transmission starts after a successful valid/ready handshake.

All internal logic runs from the 27 MHz FPGA system clock. The SPI clock is generated as an output signal and is not used as an internal FPGA clock.

For the current configuration:

- MOSI data changes on falling SPI clock edges
- MISO data is sampled on rising SPI clock edges
- SCLK is idle low

This corresponds to SPI mode 0.

## Interface

- `TX_Data[15:0]` — parallel transmit data
- `TX_Valid` — transmit request
- `TX_Ready` — SPI master can accept a new transfer
- `RX_Data[15:0]` — received data
- `RX_Valid` — received data is available
- `RX_Ready` — downstream logic can accept the received data
- `N[4:0]` — number of transferred bits
- `MOSI` — serial data output
- `MISO` — serial data input
- `SCLK` — generated SPI clock

A new transfer is accepted when:

`TX_Valid && TX_Ready`

Received data is consumed when:

`RX_Valid && RX_Ready`

## Timing

With the current clock-divider configuration:

- FPGA clock: 27 MHz
- SPI clock: approximately 843.75 kHz
- SCLK idle state: low
- MISO sampling: rising edge
- MOSI shifting: falling edge

## Usage

The SPI master is intended as a reusable transport layer for device-specific controllers.

It is currently used by:

- MCP3002 ADC controller
- MCP4822 DAC controller

Functional verification of the SPI master is performed as part of the corresponding device-controller tests.
