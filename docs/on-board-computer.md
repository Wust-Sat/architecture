---
title: On-Board Computer (OBC)
date: 09.04.2025
version: v1.0.0
---

# On-Board Computer (OBC)

## Overview

The On-Board Computer (OBC) serves as the central processing unit of the
WUST-Sat platform. It is responsible for executing commands, managing data flow,
monitoring satellite health, running operational schedules, and interfacing with
the payload. The OBC architecture employs a dual-component approach with
motherboard based on STM32 and Compute Module running Linux.


## OBC Components

The OBC consists of two main electronic modules integrated onto a single PCB
within the platform stack:

1.  **OBC Motherboard:** A custom PCB based on the WUST-Sat **Template Board**
    design. It houses an STM32U5 microcontroller and provides the necessary
    interfaces and mounting points for the **Compute Module**. It connects
    OBC to the platform **bus**.

2.  **Compute Module (CM):** A standard Raspberry Pi Compute Module 4,
    providing a Linux environment and significantly more processing power and
    memory than the microcontroller. It is mounted onto the OBC Motherboard.

Currently, it is not specified whether OBC will have separate CAN transceivers
for both modules or will use a single one.


## Current Development Status

In the current phase of the project, the design of the custom Motherboard is
out of scope.

For development and testing purposes, the OBC functionality will be implemented
using a **mockup** - a standard **Raspberry Pi 4** or a **Compute Module 4**
mounted on an official development board.

This mockup will be connected to the platform prototype via the **CAN** bus,
most likely using a [RS485 CAN HAT](https://www.waveshare.com/wiki/RS485_CAN_HAT).
The functions designated for the OBC Motherboard (STM32U5) will either be skip
or simulated on the CM4 mockup if its make sence.


## OBC Motherboard (STM32)

The STM32U5 microcontroller on the Motherboard is designed for low-power
operation and supervisory tasks. By default, it is intended to remain powered on
due to its very low power consumption, although mechanisms to turn it off will
exist. Its primary roles include:

- **Low-Power Schedule & Log Management:** Handling basic operational
  schedules and logging essential communication or events when the **CM**
  is powered down to conserve energy.
- **CM Power Management:** Managing the power-up and shutdown of the Compute
  Module 4 based on the operational schedule or specific commands.
- **CM Peripheral Coordination (Optional):** Managing aspects like selecting
  the boot source (e.g., different SD card images) for the **CM**.
- **Real-Time Task Execution (Optional):** Handling tasks requiring
  deterministic, real-time responses. No specific real-time requirements have
  been identified for the platform at this stage.


## Compute Module (CM)

The **CM** acts as the primary high-level processing unit and the main
point of interaction for ground operators via the **Comm** modules. Running
a Linux operating system, it handles most computational, data management and
complex operational tasks:

- **High-Level Platform Management:** Overseeing the overall operation of the
  satellite.
- **Communication Logging:** Actively logging all or selected **CAN** bus
  traffic between modules for diagnostics and telemetry.
- **Status Monitoring & Event Handling:** Tracking the status of all platform
  modules, identifying significant events (e.g., low battery voltage, module
  restarts, sensor anomalies), and triggering appropriate responses.
- **Critical Situation Response:** Executing response procedures to critical
  events, such as unexpected satellite tumbling detected by the **ADCS** or the
  failure of a redundant **EPS** module.
- **File System Management:** Managing all aspects of file storage and
  transfer. This includes:
    - Saving data (e.g., images, scientific results) from the **payload**.
    - Compiling telemetry data from various modules into archives for downlink.
    - Handling file uploads (uplink) and downloads (downlink) requested by the
      ground station, likely interacting with the **UHF** or **S-Band** **Comm**
      modules via **CAN** file transfer messages.
- **Mission Scheduling:** Managing and executing the operational schedule,
  including tasks like ADCS maneuvers, **payload** activation periods, battery
  charging cycles, and scheduled communication windows.
- **Payload Interface (Future):** Handling communication, command, and data
  acquisition for the mission **payload** (once defined and integrated).
- **Module Software Updates:** Managing the process of updating the firmware on
  other STM32-based modules using dedicated USB lines routed through the system
  **bus** to each module's ST-Link programmer.
- **Complex Task Execution:** Handling non-standard tasks that benefit from the
  Linux environment. For example libraries, scripting capabilities (Python), or
  containerization, which would be difficult or inefficient to implement on the
  microcontrollers.
- **Extensibility:** Providing a flexible platform for extra tasks or future
  software problems that may arise during the develompent of mission.
