---
title: JAMMA / JVS
description: The Japan Amusement Machine and Marketing Association standards
published: true
date: '2026-11-24T23:58:46.490Z'
tags:
  - arcade
editor: markdown
dateCreated: '2026-11-24T23:28:08.594Z'
---

# JAMMA / JVS

The **Japan Amusement Machine and Marketing Association** (commonly known as JAMMA) standards are set of standards which define how a arcade video game PCB/System should interface to a JAMMA compliant cabinet, including the video signal levels, pinouts of connectors and power requirements.

Arcade boards based on the PlayStation used either JAMMA, JVS, both or neither in the case of the [Konami Twinkle](/hardware/arcade/twinkle.md).

## JAMMA
Originally released in 1985, this specification gave rise to 'JAMMA' compliant arcade cabinets and PCBs, allowing an operator to easily change the game within a cabinet by swapping out the game PCB. This original specification defined the 56-way edge connector physical dimensions and electrical signals used to interface to the arcade cabinet. 

This standard essentially became the universal after it's introduction and remained in use even once it's successor standard, JVS, was released. 

### Pinout - 56-way 0.156"/3.96mm pitch edge connector
JAMMA pin definitions use both numbers and letters, where the numbers are for pins on the parts side of the board (the top face edge pins), and the lettered pins are for the solder side of the board (the bottom face edge pins). There is a keying notch used for aligment and to prevent inserting the connector backwards which would damge the game board. For the lettered pins, **I**, **O** and **Q** are not used due to the similarities to **1** and **0**.

The standard pinout for JAMMA is as follows:
| Signal | Solder Side | Parts Side | Signal |
| ====== | =========== | ========== | ====== |
| GND | A | 1 | GND |
| GND | B | 2 | GND |
| +5V | C | 3 | +5V |
| +5V | D | 4 | +5V |
| -5V | E | 5 | -5V |
| +12V | F | 6 | +12V |
| KEY |  |  | KEY |
| Coin Counter 2 | J | 8 | Coin Counter 1 |
| Coin Lockout 2 | K | 9 | Coin Lockout 1 |
| Speaker -  | L | 10 | Speaker + |
| N/C <sup>1</sup>| M | 11 | N/C <sup>1</sup> |
| Video Green  (5Vpp) | N | 12 | Video Red (5Vpp) |
| Video Sync (15KHz, TTL Composite) | P | 13 | Video Blue (5Vpp) |
| Service Switch <sup>2</sup> | R | 14 | Video GND <sup>3</sup> |
| Tilt Switch | S | 15 | Test Switch |
| Coin 2 Switch | T | 16 | Coin 1 Switch |
| P2 Start | U | 17 | P1 Start |
| P2 Up | V | 18 | P1 Up |
| P2 Down | W | 19 | P1 Down |
| P2 Left | X  | 20 | P1 Left |
| P2 Right | Y | 21 | P1 Right |
| P2 Button 1 | Z | 22 | P1 Button 1 |
| P2 Button 2 | a | 23 | P1 Button 1 |
| P2 Button 3 | b | 24 | P1 Button 1 |
| N/C <sup>4</sup> | c | 25 | NC <sup>4</sup> |
| N/C <sup>5</sup> | d | 26 | NC <sup>5</sup> |
| GND | e | 27 | GND |
| GND | f | 28 | GND |

1. Sometimes used for a second speaker in stereo configurations.
2. Adds a credit to the game without advancing the physical coin counter - Used for play testing by technicians.
3. Video signal ground is not always joined to digital or logic ground so should always connect to the monitor ground.
4. Button 4 on some game boards
5. Button 5 on some game boards

### JAMMA+
JAMMA+ is a term given to a JAMMA compliant game board that offers extra features on the JAMMA edge connector (or other connectors) that is not part of the official standard. There is no defined set of features added and often they are differ per game board. Common items included increasing the button count per player to 5 buttons, adding a stereo speaker or using unused pins for a light gun inputs.

## JAMMA Video Standard
In 1996, the **JAMMA Video Standard**, more commonly known as **JVS** was released. This differed drastically from the earlier 56-way edge connector in both number of connectors, types and signalling comlexity:
* Power is now supplied using a pair of **JST-VL** connectors, drops -5v (which was not used in some later game boards) and adds two 3.3v rails.
* Video now uses a VGA style connector (and in some cases VGA signalling)
* Audio is line-level, stereo and output to the cabinet's amplifier using a pair of standard phono connectors, often called **RCA** connectors.
* I/O is vastly changed in both capability and complexity, using an RS-485 based serial protocol to connect 'I/O boards' to the game system, providing but not limited to:
  * Inputs, coins and machine switches
  * General purpose outputs for lamps
  * Analog control inputs
  * Touch screen support

> [!NOTE]
> Due to the scope of the JVS protocol, this page does not cover it. Documentation on the I/O protocol can be found online.

This standard was used by most of the main arcade manufacturers such as Sega, Konami and Namco in both their game PCBs and cabinets, altough it did not achieve the near universal adoption as JAMMA did, mostly due to the fact JAMMA based cabinets were still widely in use until the mid 2000s and because JAMMA, for the most part, was good enough for most joystick based games. Additionally, making a cabinet or game for JAMMA would allow access to a wide range of games. It also avoided the added cost for the cabinet manufacturer who would otherwise have to supply an I/O board and audio amplifier as well.

### Pinouts
#### Power - JST-VL 8-Way + JST-VL 6-Way
There are usually two power connectors in a JVS compliant cabinet but depending on the power requirement of the game board, sometimes only one of the two power connectors is used with the other going unused.
##### 8-Way
| Pin | Signal |
| === | ====== |
| 1 | N/C |
| 2 | +3.3V |
| 3 | +5V |
| 4 | +12V |
| 5 | N/C or GND |
| 6 | GND |
| 7 | GND |
| 8 | GND |
##### 6-Way
This is essentially the same as the 8-way power connector but does not include the +12V rail.
| Pin | Signal |
| === | ====== |
| 1 | N/C |
| 2 | +3.3V |
| 3 | +5V |
| 4 | N/C or GND |
| 5 | GND |
| 6 | GND |

#### Video - D-Subminiature DE15HD
This connector is the same kind used in commodity PCs for video, often called VGA after the video card standard it was first used with but it is not VGA compliant except in 31KHz seperate sync mode.
| Pin | Signal |
| === | ====== |
| 1 | Red (0.7Vpp) |
| 2 | Green (0.7Vpp) |
| 3 | Blue (0.7Vpp) |
| 4 | N/C <sup>1</sup> |
| 5 | GND |
| 6 | Red Shield (GND) |
| 7 | Green Shield (GND) |
| 8 | Blue Shield (GND) |
| 9 | Sometimes +5V, N/C otherwise |
| 10 | Sync Shield (GND) |
| 11 | N/C <sup>1</sup> |
| 12 | N/C <sup>2</sup> |
| 13 | H-Sync/Composite Sync |
| 14 | V-Sync | 
| 15 | N/C <sup>2</sup> |

1. Pin is used for 3-bit monitor ID but is not used in JVS.
2. Pin is used for I<sup>2</sup>C DDC communication but not used in JVS.

#### Audio - Phono / RCA
Audio is supplied using standard stereo/phono audio cables to amplifiers also taking a stereo line level input.

#### JVS I/O Data - USB A/B connector 
JVS I/O boards use standard USB cabling but are **not** USB compliant in any way. They only share the same physical ports to reduce cost to the manufacturer.

| Pin | Signal |
| === | ====== |
| 1 | Sense |
| 2 | RS-485 B |
| 3 | RS-485 A |
| 4 | GND |
