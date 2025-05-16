# S-Band Communication Module

## Overview
The S-Band Communication Module is a subsystem of the satellite responsible for downlinking and uplinking telemetry and payload data using the S-Band frequency spectrum (~2.4 GHz). It is designed to comply with ITU regulations and CubeSat communication standards, including CCSDS protocols.

## Objectives
- Provide a reliable half-duplex communication channel in S-Band
- Support both uplink and downlink operations
- Integrate with the On-Board Computer (OBC) via CANopen protocol
- Comply with frequency allocation and transmission regulations

## Functional Requirements
- Frequency Range: around 2.4 GHz
- Modulation: QPSK (CCSDS compatible)
- Data Rate: 1–2 Mbps (configurable)
- Duplex Mode: Half-duplex
- Protocol: CCSDS + custom encoding between ground station and module

## Hardware Architecture
### Block Diagram
![bb](https://github.com/user-attachments/assets/678284d1-3649-4e18-a969-4f32fe69d801)


### Key Components
| Component           | Description                                 |
|---------------------|---------------------------------------------|
| STM32 Processor     | Handles CCSDS encoding/decoding and modulation |
| RF Power Amplifier  | Boosts signal power for transmission           |
| Transceiver         | Converts data to/from RF signals               |
| Antenna             | Responsible for both transmitting and receiving radio signals. |
## Software and Communication Protocol
- CCSDS framing
- Encoding/decoding between ground station and module
- Command/telemetry interface via CANopen
- Bootloader for firmware updates via CAN

## Testing and Validation
- RF validation with PlutoSDR

## Current Status
- Component selection and research for PCB design is ongoing  
- STM32 capabilities and integration are being evaluated  
- Research is ongoing to ensure smooth integration between CCSDS-based QPSK transmission (handling 400-byte blocks) and CANopen (limited to 64-byte frames), including encoding, segmentation, and reassembly mechanisms.


## Responsible Team
- Hayrettin Ayçetin — STM programming
- Pınar Aydın — Telecommunication / Antenna
- Barış Bulgan — Hardware Design

---
_Last updated: May 2025_

