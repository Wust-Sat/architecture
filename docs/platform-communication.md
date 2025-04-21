---
title: WUST-Sat Platform Communication Architecture
date: 18.04.2025
version: v1.0.1
---

# WUST-Sat Platform Communication Architecture

## Overview

The platform utilizes two distinct communication domains:

1.  **Internal Communication:** data exchange, command distribution, and
    synchronization between the platform modules.
2.  **External Communication:** the radio frequency (RF) links between the
    satellite and ground stations for downlink, uplink, and data transfer.


## Internal Communication

The basis for internal communication is the Controller Area Network (CAN) bus,
implemented according to the specifications described below.

**Core Technology:**
The platform uses the **CAN-FD** (CAN with Flexible Data-Rate) standard
with the extended frame format (FEFF). This allows for:
- A data payload of up to **64 bytes** per frame.
- An extended message identifier (**29-bit ID**).

**Topology and Protocol:**
Communication operates on a **peer-to-peer** basis over the shared CAN lines
distributed via the platform bus. Modules can directly address other modules or
broadcast messages to all modules.

**Message ID Structure:**
The 29-bit extended CAN message ID is structured to provide information about
routing, message type and it's context:

| Bits    | Field               | Description                                                    |
| ------- | ------------------- | -------------------------------------------------------------- |
| 0-3     | Source ID           | 4-bit ID of the sending module/component.                      |
| 4-7     | Destination ID      | 4-bit ID of the target module/component (or broadcast ID 15).  |
| 8-15    | Message Type        | 8-bit identifier defining the purpose/content of the message.  |
| 16      | Ground Station Flag | 1-bit flag. Set to 1 if the message originates from or is      |
|         |                     | destined for a ground station command/telemetry stream.        |
| 17-28   | Reserved            | 12 bits reserved for future use.                               |

**Message Categories:**
CAN messages are categorized based on their function, primarily determined by
the *Message Type* field in the ID. Key categories include:

- **Telemetry:** Messages reporting the status, health, or measurements of
  a module. Examples: sensor readings (voltage, current, temperature),
  heartbeat frames indicating operational status, or current operating mode
  reports.
- **Telecommand:** Messages instructing a module to perform an action or change
  its state. Examples: commands to switch operating modes, update configuration
  parameters, set hardware registers, or trigger specific operations.
- **Events:** Messages broadcast by a module to announce significant occurrences
  spontaneously. Examples: module panic message, battery level crossing a
  predefined threshold, entering/exiting solar eclipse, initiation of an ADCS
  maneuver, component restart notification.
- **Wake-up Frames:** Special CAN frames used to transition modules between
  low-power (sleep) and active states. These are typically handled directly by
  the CAN transceiver hardware, rather than being processed by the module's main
  microcontroller.
- **Clock Synchronization:** Frames dedicated to distributing time information
  across the bus, allowing modules to synchronize their internal clocks,
  particularly after waking from a low-power state. The exact protocol for
  synchronization is yet to be finalized but must support time-setting upon
  module initialization.
- **File Transfer:** Messages facilitating the transfer of larger data blocks,
  segmented into multiple CAN frames. This is crucial for moving data between
  the OBC (specifically the CM4) and the communication modules (UHF, S-Band) for
  downlink, or for transferring data logs or images from subsystems like the
  ADCS.

**Message Definition:**
The definition of all message types, their identifiers and payload structures
(data types, byte order, unit conversion) is maintained in the `platform-dbc`
repository. The definitions are written in Python using the `cantools` library.
The script allows you to generate a CAN database file (`.dbc`). This `.dbc` file
serves as the primary reference for all CAN communication related firmware
development.


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
  bus. The *Ground Station Flag* in the CAN ID may be set.
- **Downlink:** Telemetry, Events, or File Transfer data originating from
  modules on the CAN bus are received by the relevant communication module.
  This data is then buffered, potentially encoded (e.g., using CCSDS),
  modulated, and transmitted to the ground station during available
  communication windows.
