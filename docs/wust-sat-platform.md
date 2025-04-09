---
title: WUST-Sat Platform Architecture
date: 08.04.2025
version: v1.0.1
---

# WUST-Sat Platform Architecture

This document outlines the architecture of the WUST-Sat platform.

The primary goal of the current project phase is the design and development of
a platform prototype. This phase focuses specifically on the electronic
subsystems and their intercommunication. Aspects related to the mechanical
structure, payload integration, and adaptation to space conditions are
considered out of scope at this stage.


## Platform Architecture Overview

The WUST-Sat platform is designed as a modular system:
- Modules are derived from a common **Template Board**.
- Modules are interconnected via a common **bus**. A PC104 connector serves as
  the physical interface, distributing power, **CAN**, and USB lines to each
  module.
- A 4-bit **ID** system uniquely identifies each module or addressable
  component.
- Communication between modules is handled using the Controller Area Network
  (**CAN**) protocol.

Subsequent sections detail these architectural elements.


## Platform Modules

A **module** is a distinct functional board (e.g., EPS, UHF Comm, OBC
Motherboard). Most modules are separate PCBs based on the **Template Board**
design. Each module is addressable units on the CAN bus.

The WUST-Sat platform consists of the following modules:

**EPS** (Electrical Power System): Responsible for power generation, storage,
and distribution. Redundancy, supporting up to three independent EPS modules
operating concurrently, is planned for future versions.

**Comm** (Communications System): Handles telemetry downlink and telecommand
uplink. The platform integrates three independent communication modules
operating on different frequency bands/protocols:
- **LoRa** (Long Range)
- **UHF** (Ultra High Frequency)
- **S-Band**

**OBC** (On-Board Computer): Serves as the central processing unit. The detailed
architecture is described in the [OBC documentation](./on-board-computer.md).
It comprises two modules:
- A **Motherboard** based on an STM32 microcontroller. This module acts as the
  supervisor of the on-board computer. Future responsibilities may include
  real-time tasks and bus communication management.
- A Raspberry Pi **Compute Module 4** (**CM4**) for higher-level processing,
  data storage, and complex computations.

**ADCS** (Attitude Determination and Control System): Manages the satellite's
orientation. The design and implementation of the ADCS module will be undertaken
by an external team and integrated into the WUST-Sat platform.


## Design Philosophy and Template Board

A core principle of the WUST-Sat design is modularity facilitated by a
**Template Board**. Each specific module (like EPS, Comm, OBC Motherboard) is
based on this common template design.

The **Template Board** includes:
- An STM32U5 microcontroller.
- An integrated ST-Link programmer/debugger.
- A USB interface connected to the ST-Link for programming and debugging.
- A local power supply system to regulate voltage received from the **bus**.
- A **CAN** transceiver capable of managing the microcontroller's sleep/wake
  state based on bus activity.

**Repository-Based Design**

The KiCad design files for the **Template Board** are hosted in a central
repository. To develop a new platform module, developers fork this repository
and populate the template with the specific components required for that
module's function.


## System Bus

The term **bus** refers to the electrical interconnections between the modules,
facilitated by a standard PC104 connector. This connector carries power and data
signals, including:

- **Battery Voltage**: Unregulated power line directly from the battery.
- **5V**: Regulated 5-volt power line.
- **Ground (GND)**: Common electrical reference.
- **CAN Bus Lines**: Four shared differential pair (**CAN_H**, **CAN_L**) for
  inter-module communication.
- **USB Lines**: 12 independent USB data pairs, each routed to the ST-Link
  programmer on a specific module slot, enabling individual module programming.
- **Reserved Pins**: Additional pins allocated for future use or specific
  interconnections, such as direct GPIO links between adjacent modules or
  connections between radio modules and antennas.


## Module Identification (ID)

Each module or addressable component within the platform is assigned a unique
4-bit **ID**. This **ID** serves two critical functions:

1. **CAN Message Addressing**: Identifies the source and destination of
   messages on the **CAN** bus.
2. **Programming Port Selection**: Determines which of the 12 USB lines on the
   **bus** connector corresponds to the ST-Link programmer of a specific
   STM32-based module.

The **ID** allocation scheme is as follows:

**0 - 11**: Reserved for modules based on the **Template Board** (containing an
STM32 microcontroller).

**12 - 14**: Reserved for other addressable components, such as the **CM4**
within the **OBC**, or a future **payload**.

**15**: Broadcast address. Messages sent to this **ID** are intended for all
components on the **CAN** bus.
