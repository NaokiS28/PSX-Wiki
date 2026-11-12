---
title: Controllers
description: ''
published: true
date: '2026-11-02T23:01:36.851Z'
tags: []
editor: markdown
dateCreated: '2026-11-02T23:01:36.851Z'
---

# Controllers

# Controllers

> [!NOTE]
> This page is a work in progress. Much of the information is incorrect or incomplete.

## Brief overview

This page covers information that applies to controllers generally.

Controllers are connected via the serial port.
They are accessed via the SIO0 registers.
The protocol is similar to SPI.

## Protocol
Bytes are sent LSB first.
Baud rate is ~250KHz.
Clock high-when-idle. Bits are output on the falling edge, and sampled on the rising edge.
Separate /ACK line.
/ACK must be pulled low for at least 2 microseconds to request another byte from the host.

## Communicating with the controllers
The SIO0 registers are mapped at `0x1f801040`
There are a few registers, but the only relevant ones for communicating with the controllers are as follows:

`0x00` - Data
`0x04` - Status (Read-Only)
`0x08` - Mode
`0x0A` - Control

### Status Register
The status register contains several flags to do with the serial port.
The relevant flags for controllers are listed below.

| :-- | :-- | :-- |
|0|TXRDY|The TX FIFO is ready to accept a new byte|
|1|RXRDY|The RX FIFO has data to be read.|
|2|TXU|TX is idle. No data is being transferred.|
|3|PERROR |RX Parity Error. |
|4|-|Unused|
|5|-|Unused|
|6|-|Unused|
|7|DSR|DSR input level|
|8|-|Unused|
|9|IRQ|Interrupt request|


### Data Register
The data register acts differently when reading from it vs writing to it.
When writing






### Controller IDs

5A12h - [Mouse](mouse.md)
5A23h - [NegCon](negcon.md)
5A31h - [Koanmi Lightgun](koanmi-lightgun.md)
5A41h - [Digital Pad](digital-pad.md)
5A53h - [Analog Stick](analog-stick.md)
5A63h - [Namco Lightgun](namco-lightgun.md)
5A73h - [Analog Pad](analog-pad.md)
5A7xh - [Dualshock 2](dualshock-2.md)
5A79h - [Dualshock 2](dualshock-2.md)
5A80h - [Multitap](multitap.md)
5A96h - [Keyboard](keyboard.md)
5AE3h - [Jogcon](jogcon.md)
5AE8h - [Keyboard/Sticks](sticks.md)
5AF3h - [Config Mode](config-mode.md)

