---
title: WUST-Sat Platform Communication Architecture
date: 21.04.2025
version: v1.1.0
---

# WUST-Sat Platform Communication Architecture

## Overview

The platform utilizes two distinct communication domains:

1.  **Internal Communication:** data exchange, command distribution, and
    synchronization between the platform modules using the **CANopen FD**
    protocol.
2.  **External Communication:** the radio frequency (RF) links between the
    satellite and ground stations for downlink, uplink, and data transfer.


## Internal Communication

Internal communication is based on the **CANopen FD** protocol suite. It
provides standardized communication services and object models. It leverages
the **CAN-FD** standard (up to 64 bytes in message payload) with the extended
frame format (29-bit ID).


## External Communication

External communication involves transmitting data between the satellite and
ground stations via RF links.

**Purpose:**
- **Uplink:** Receiving telecommands from authorized ground stations to control
  the satellite and its payload.
- **Downlink:** Transmitting telemetry, event notifications, and payload data
  (e.g., images, scientific measurements) back to ground station.

**Communication Modules:**
As defined in the [WUST-Sat Platform Architecture](./wust-sat-platform.md),
the platform will have three distinct communication modules to handle
external links:
- **LoRa Module:** For low-power, long-range, low-data-rate communication,
  potentially for basic telemetry or module pinging.
- **UHF Module:** Primarily for robust command uplink and routine telemetry
  downlink, operating in the UHF band.
- **S-Band Module:** For higher bandwidth communication, suitable for
  downlinking larger data volumes like files or images, operating in the S-Band.

**Protocols:**
The specific RF modulation and encoding schemes are defined by the respective
LoRa, UHF, and S-Band module documentation and used hardware. Higher-level
protocols may be implemented on top, particularly for file transfer. Protocols
like **CFDP** (CCSDS File Delivery Protocol) might be employed over the UHF or
S-Band links to ensure reliable delivery of large data sets, addressing issues
like interference and limited transmission windows (see 
[Issues of Earth-satellite communication](./earth-satellite-communication.md)).

**Interaction with Internal Bus:**
The communication modules act as bridges between the external RF links and the
internal CAN bus.
- **Uplink:** Received telecommands are demodulated, potentially decoded (e.g.,
  if using CCSDS), validated, and then translated into corresponding CAN
  Telecommand messages addressed to the appropriate target module(s) on the CAN
  bus.
- **Downlink:** Telemetry, Events, or File Transfer data originating from
  modules on the CAN bus are received by the relevant communication module.
  This data is then buffered, potentially encoded (e.g., using CCSDS),
  modulated, and transmitted to the ground station during available
  communication windows.
