
---
title: Internal Communication 
date: 2025-05-13  
version: v1
---

## Overview

This document defines the internal communication scheme between the **On-Board Computer (OBC)**, the **Electrical Power System (EPS)**, and the **Radio Communication Module** in a CubeSat platform. All communication between these subsystems is implemented over the **CANopen** protocol, where the **OBC** functions as the **CANopen Master / NMT Manager**, and EPS and Radio are **slave nodes**.

The communication is based on **Process Data Objects (PDO)** for real-time data exchange, **Service Data Objects (SDO)** for configuration and control, and **Network Management (NMT)** for state control. **Emergency messages** and **Heartbeat monitoring** are used for fault detection and health monitoring.

---

## 1. EPS (Power System) Communication Events

| Event                          | CANopen Mechanism     | Description                                                                 |
|-------------------------------|------------------------|-----------------------------------------------------------------------------|
| Low voltage condition         | PDO (status EPS)       | EPS sends a PDO message when the battery voltage drops below a threshold.  |
| Battery status / voltage      | PDO                    | Periodic transmission of battery telemetry.                                |
| Radio power-on command        | SDO / PDO              | OBC sends command to EPS to power on radio line.                           |
| Power system fault            | PDO / Emergency Object | EPS reports faults like overcurrent or overtemperature.                    |
| Entering power saving mode    | PDO                    | EPS informs OBC about switching to power-saving mode.                      |
| Voltage threshold configuration | SDO                  | OBC sets low voltage threshold via SDO.                                    |

---

## 2. Radio Module Communication Events

| Event                          | CANopen Mechanism     | Description                                                                 |
|-------------------------------|------------------------|-----------------------------------------------------------------------------|
| Ready for transmission        | PDO                    | Radio informs OBC it is ready to transmit.                                 |
| Data reception notification   | PDO                    | Radio notifies OBC that data has been received.                            |
| Transmission error            | Emergency / PDO        | Radio sends error message (e.g. CRC, timeout).                             |
| Frequency/power config        | SDO                    | OBC sets parameters via SDO.                                               |
| Start/stop transmission       | PDO                    | Control messages to start or stop transmission.                            |
| Buffer status update          | PDO                    | Radio reports buffer status (full/empty).                                  |

---

## 3. CANopen Network Management (NMT)

| Event                        | CANopen Mechanism     | Description                                                                 |
|-----------------------------|------------------------|-----------------------------------------------------------------------------|
| Start / Stop / Reset node   | NMT                    | OBC manages operational states of EPS and Radio.                           |
| Heartbeat monitoring        | Heartbeat             | Slave nodes periodically transmit heartbeat messages.                      |
| Node failure detection      | Heartbeat / NMT       | OBC detects node failure via heartbeat loss or reset.                      |
| Diagnostics and health check| SDO / Emergency        | OBC queries or receives health/fault data.                                 |







