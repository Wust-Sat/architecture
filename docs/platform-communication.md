---
title: WUST-Sat Platform Communication Architecture
date: 22.04.2025
version: v1.1.1
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

Modules act as CANopen nodes. Each module type (e.g., EPS, Comm) is described by
an **XML Device Configuration (XDC)** file. These files define the device's
**Object Dictionary (OD)** which is a standardized, structured collection of all
accessible data within a device, including configuration parameters, operational
data (telemetry), commands, and device status.

**XDC files** serve as the database for defining module capabilities and
communication parameters. Tools can parse these files to:
- Generate C/C++ code skeletons for the Object Dictionary implementation on
  the STM32 modules.
- Configure the network master or configuration tools.
- Facilitate simulation and testing using tools or scripts (e.g., Python with
  the `canopen` library) that understand the device descriptions.


### Node Identification

Each CANopen node on the bus must have a unique **7-bit Node-ID** (ranging from
1 to 127). Node-ID 0 is reserved for specific broadcast functions (e.g., NMT
master messages, LSS).

The platform's 4-bit hardware **ID** (0-15) assigned to each module is used
to derive the CANopen Node-ID using mapping: `Node_ID = 16 + Hardware_ID`.

This results in unique Node-IDs within the valid CANopen range (1-127) starting
from 16:
- Hardware ID 0 -> Node-ID 16
- Hardware ID 1 -> Node-ID 17
- ...
- Hardware ID 15 -> Node-ID 31


### CAN Message ID Structure

CANopen strictly defines the usage of the 29-bit CAN identifier based on
**Function Code** and **Node-ID**. The previously defined custom 29-bit ID
structure is **not used**. Communication is based on standard CANopen ID
assignments.

**Telemetry**
Module health & status monitoring:
- **Basic Liveness:** Modules will periodically transmit **NMT Heartbeat**
  messages, allowing the NMT Master (OBC) to quickly detect unresponsive nodes.
- **Routine Telemetry:** Key operational data (e.g., EPS voltages/currents,
  temperatures, communication module status, ADCS sensor readings) will be
  mapped to **Transmit PDOs (TPDOs)**. These PDOs can be configured for
  periodic transmission, asynchronous transmission on value change, or
  synchronous transmission triggered by **SYNC** messages.
- **On-Demand Data:** Specific, less frequently needed parameters can be read
  by the OBC using **SDO read requests**.

**Telecommand**
Commands and configuration:
- **Module State Control:** The OBC (as NMT Master) will use **NMT commands** to
  transition modules between states (e.g., Operational, Pre-Operational,
  Stopped, Reset Node).
- **Configuration Parameters:** Non-volatile settings (e.g., operational
  thresholds, communication parameters) stored in the Object Dictionary will be
  modified using **SDO write requests**.
- **Real-time Actions:** Frequently used commands (e.g., trigger ADCS maneuver,
  switch Comm transmitter on/off, set EPS output state) will be mapped to
  **Receive PDOs (RPDOs)** on the target module for low-latency execution.

**Events**
Events and error reporting:
- **Critical Faults:** Modules will report serious internal errors (e.g.,
  overcurrent, critical failure) immediately using **EMCY (Emergency) messages**.
  The OBC will monitor for EMCY messages and trigger appropriate responses.
- **Significant Events:** Non-critical but important occurrences (e.g., mode
  change completion, battery threshold crossing, task completion) can trigger
  asynchronous **TPDO transmissions** containing relevant status information or
  event flags.

**Time Distribution**:
The OBC will act as the time master, periodically broadcasting the 
**TIME message** to allow all modules to synchronize their internal clocks,
crucial after wake-up from low-power states or for correlating data logs.

**File Transfer**:
Transferring large data blocks, such as payload data (images, science logs) from
the source (e.g., Payload Interface, CM4) to the appropriate Communication
Module (UHF/S-Band) for downlink, or transferring logs between modules, will
utilize **SDO Block Transfer**. This provides a standardized, reliable mechanism
for segmented transfer over CAN.

**Handling Ground Station Context:**
The context of whether data/commands relate to ground communication will be
managed within the data payload or via specific Object Dictionary entries.
- **Uplink:** When a Comm module receives a command from the ground, it
  translates it into an SDO write or RPDO transmission. The OBC (or the target
  module itself, if appropriate) can identify the origin based on which Comm
  module sent the CANopen message or by including a flag/source field within the
  SDO/PDO data payload.
- **Downlink:** Data intended for downlink (e.g., telemetry logs compiled by the
  CM4, payload data) will be sent via SDO Block Transfer or PDOs specifically to
  the designated Comm module's Node-ID. The Comm module inherently knows data
  received via these specific mechanisms is for transmission. Specific Object
  Dictionary entries might be used to request specific telemetry data explicitly
  for downlink.

**Wake-up Management**
Physical wake-up relies on the CAN transceiver detecting bus activity. Logical
wake-up and transition to operational states are managed using **NMT commands**
after a node has powered up or been woken by bus activity.


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
