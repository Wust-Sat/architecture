
---
title: Internal Communication 
date: 2025-05-2  
version: v1.1
---

## Overview

This document defines the internal communication scheme between the **On-Board Computer (OBC)**, the **Electrical Power System (EPS)**, and the **Radio Communication Module** in a CubeSat platform. All communication between these subsystems is implemented over the **CANopen** protocol, where the **OBC** functions as the **CANopen Master / NMT Manager**, and EPS and Radio are **slave nodes**.

The communication is based on **Process Data Objects (PDO)** for real-time data exchange, **Universal Service Data Objects (USDO)** for configuration and control, and **Network Management (NMT)** for state control. **Emergency messages** and **Heartbeat monitoring** are used for fault detection and health monitoring.

---

## 0. Common Subsystem Requirements
All subsystems communicating over CANopen (e.g., EPS, Radio) must implement the following features to ensure compatibility, diagnostics, and redundancy:
| Features                          | Description      | 
|-------------------------------|------------------------|
| Boot Counter         | Each node must increment and store a persistent boot counter for every power cycle.      | 
| Heartbeat     | Each node must periodically transmit heartbeat messages as per CANopen standard.                   | 
| Power Control (on/off)       | Each node must support controlled activation and shutdown via CANopen              | 
| Status Report            | Each node must be capable of sending a summarized status report on request or periodically | 
| Last Error Record    | Each node must store and be able to report the last error/event                    | 
| Telemetry Storage | 	Nodes must store and expose recent telemetry parameters                  | 
|System Uptime | 	Each module must track and report time since last power-on (uptime)|

## 1. EPS object dictionary
| Index    | Sub | Name                    | Type          | Access | Description                              |
| -------- | --- | ----------------------- | ------------- | ------ | ---------------------------------------- |
| `0x1000` | 00  | Device Type             | `UNSIGNED32`  | ro     | Device type (standard)                   |
| `0x1001` | 00  | Error Register          | `UNSIGNED8`   | ro     | Standard error register                  |
| `0x1003` | 00  | Pre-defined Error Field | `UNSIGNED32`  | ro     | Last error code                          |
| `0x1017` | 00  | Heartbeat Producer Time | `UNSIGNED16`  | rw     | Time [ms] between heartbeat messages     |
| `0x2000` | 00  | Boot Counter            | `UNSIGNED32`  | ro     | Number of system startups                |
| `0x2001` | 00  | System On/Off           | `UNSIGNED8`   | rw     | 0 = OFF, 1 = ON                          |
| `0x2002` | 00  | System Status Report    | `UNSIGNED16`  | ro     | System status bitmask                    |
| `0x2003` | 00  | Last Error Code         | `UNSIGNED32`  | ro     | Last reported error code                 |
| `0x2004` | 00  | Uptime                  | `UNSIGNED32`  | ro     | Time since last power-on [seconds]       |

## 2. Object Dictionary – Radio Module

| Index    | Sub | Name                         | Type          | Access | Description                                       |
| -------- | --- | ---------------------------- | ------------- | ------ | ------------------------------------------------- |
| `0x1000` | 00  | Device Type                  | `UNSIGNED32`  | ro     | Device type (standard)                            |
| `0x1001` | 00  | Error Register               | `UNSIGNED8`   | ro     | Standard error register                           |
| `0x1003` | 00  | Pre-defined Error Field      | `UNSIGNED32`  | ro     | Last error code                                   |
| `0x1017` | 00  | Heartbeat Producer Time      | `UNSIGNED16`  | rw     | Time [ms] between heartbeat messages              |

### System Management

| Index    | Sub | Name                         | Type          | Access | Description                                       |
| -------- | --- | ---------------------------- | ------------- | ------ | ------------------------------------------------- |
| `0x2100` | 00  | Boot Counter                 | `UNSIGNED32`  | ro     | Number of system startups                         |
| `0x2101` | 00  | Radio On/Off                 | `UNSIGNED8`   | rw     | 0 = OFF, 1 = ON                                   |
| `0x2102` | 00  | Radio Status Report          | `UNSIGNED16`  | ro     | Status bitmask                                    |
| `0x2103` | 00  | Last Error Code              | `UNSIGNED32`  | ro     | Last reported error code                          |
| `0x2104` | 00  | Uptime                       | `UNSIGNED32`  | ro     | Time since last power-on [seconds]                |

### Radio Communication & Control

| Index    | Sub | Name                         | Type          | Access | Description                                       |
| -------- | --- | ---------------------------- | ------------- | ------ | ------------------------------------------------- |
| `0x2200` | 00  | Ready for Transmission       | `UNSIGNED8`   | ro     | 1 = Ready to TX, 0 = Busy or unavailable          |
| `0x2201` | 00  | Data Reception Notification  | `UNSIGNED8`   | ro     | 1 = New data received                             |
| `0x2202` | 00  | Transmission Error Code      | `UNSIGNED16`  | ro     | Error code related to last TX                     |
| `0x2203` | 00  | Frequency Configuration      | `UNSIGNED32`  | rw     | TX/RX frequency in Hz                             |
| `0x2204` | 00  | Power Level Configuration    | `UNSIGNED8`   | rw     | TX power level [% or steps]                       |
| `0x2205` | 00  | Start/Stop Transmission      | `UNSIGNED8`   | rw     | 1 = Start TX, 0 = Stop                            |
| `0x2206` | 00  | Buffer Status                | `UNSIGNED8`   | ro     | % of buffer used (0–100)                          |

## 3. Object Dictionary – OBC (On-Board Computer)

### Core System Information

| Index    | Sub | Name                         | Type          | Access | Description                                       |
| -------- | --- | ---------------------------- | ------------- | ------ | ------------------------------------------------- |
| `0x1000` | 00  | Device Type                  | `UNSIGNED32`  | ro     | Device type (standard)                            |
| `0x1001` | 00  | Error Register               | `UNSIGNED8`   | ro     | Standard error register                           |
| `0x1003` | 00  | Pre-defined Error Field      | `UNSIGNED32`  | ro     | Last error code                                   |
| `0x1017` | 00  | Heartbeat Producer Time      | `UNSIGNED16`  | rw     | Time [ms] between heartbeat messages              |

### Local OBC State & Control

| Index    | Sub | Name                         | Type          | Access | Description                                       |
| -------- | --- | ---------------------------- | ------------- | ------ | ------------------------------------------------- |
| `0x2300` | 00  | Boot Counter                 | `UNSIGNED32`  | ro     | Number of system startups                         |
| `0x2301` | 00  | OBC Power On/Off             | `UNSIGNED8`   | rw     | 0 = OFF, 1 = ON                                   |
| `0x2302` | 00  | OBC Status Report            | `UNSIGNED16`  | ro     | System status bitmask                             |
| `0x2303` | 00  | Last Error Code              | `UNSIGNED32`  | ro     | Last reported error code                          |
| `0x2304` | 00  | Uptime                       | `UNSIGNED32`  | ro     | Time since power-on [seconds]                     |

---

### Network Management Functions

| Index    | Sub | Name                         | Type          | Access | Description                                       |
| -------- | --- | ---------------------------- | ------------- | ------ | ------------------------------------------------- |
| `0x2400` | 00  | Node Start Command           | `UNSIGNED8`   | wo     | Send Start command to specific node ID            |
| `0x2401` | 00  | Node Stop Command            | `UNSIGNED8`   | wo     | Send Stop command to specific node ID             |
| `0x2402` | 00  | Node Reset Command           | `UNSIGNED8`   | wo     | Send Reset Node to specific node ID               |
| `0x2403` | 00  | Node Reset Comm. Command     | `UNSIGNED8`   | wo     | Send Reset Communication to specific node ID      |

### Heartbeat Monitoring & Failure Detection

| Index    | Sub | Name                         | Type          | Access | Description                                       |
| -------- | --- | ---------------------------- | ------------- | ------ | ------------------------------------------------- |
| `0x2500` | 00  | Monitored Node ID List       | `VISIBLE_STRING` | rw  | List of node IDs being monitored via heartbeat    |
| `0x2501` | 00  | Node Failure Bitmask         | `UNSIGNED32`  | ro     | Bitmask of failed/missing nodes                   |
| `0x2502` | 00  | Node Last Seen Timestamp     | `UNSIGNED32[]`| ro     | Last heartbeat time per monitored node (optional) |

### Diagnostics and Health Check

| Index    | Sub | Name                         | Type          | Access | Description                                       |
| -------- | --- | ---------------------------- | ------------- | ------ | ------------------------------------------------- |
| `0x2600` | 00  | Diagnostics Status           | `UNSIGNED16`  | ro     | Health check flags (e.g. memory, CPU load)        |
| `0x2601` | 00  | Self Test Result             | `UNSIGNED8`   | ro     | 0 = OK, 1 = Warning, 2 = Error                     |
| `0x2602` | 00  | Last Self Test Timestamp     | `UNSIGNED32`  | ro     | Time of last successful self-test [sec]           |


## 4. EPS (Power System) Communication Services

| Service       | Purpose/Usage                                                                |
| ------------- | ---------------------------------------------------------------------------- |
| **USDO**       | Read/write parameters: `System On/Off`, `Heartbeat Time`, `Status`, `Uptime` |
| **PDO**       | Real-time transmission of `System Status Report`, `Uptime`, `Error`          |
| **EMCY**      | Transmit emergency messages from `Error Register` or `Last Error Code`       |
| **Heartbeat** | Periodic presence signal in CAN network                                      |
| **NMT**       | EPS can receive NMT commands (Start, Stop, Reset)                            |


---

## 5. Radio Module Communication Services

| Service       | Purpose/Usage                                                                         |
| ------------- | ------------------------------------------------------------------------------------- |
| **USDO**       | Configure `Frequency`, `Power`, `Start/Stop TX`, `On/Off`, `Heartbeat`; read `Buffer` |
| **PDO**       | Transmit runtime data: `Ready for TX`, `RX Notification`, `Status`, `Error`           |
| **EMCY**      | Report transmission errors via `Transmission Error Code`                              |
| **Heartbeat** | Indicates radio module activity in the CAN bus                                        |
| **NMT**       | Controlled by OBC via NMT commands (e.g. restart after fault)                         |


---

## 6. OBC Services

| Service       | Purpose/Usage                                                       |
| ------------- | ------------------------------------------------------------------- |
| **USDO**       | Access local config: `Power`, `Uptime`, `Self Test`, `Status`       |
| **PDO**       | Send live diagnostics: `Diagnostics Status`, `Node Failure Bitmask` |
| **EMCY**      | Report system-level errors or failures                              |
| **Heartbeat** | Broadcast own heartbeat and monitor other nodes                     |
| **NMT**       | Acts as **NMT master**: controls start/stop/reset of other nodes    |








