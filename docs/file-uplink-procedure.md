---
title: File Uplink Procedure
date: 14.05.2025
version: v1.0.0
---

# File Uplink Procedure

## Overview

This document describes the procedure for transferring a file from a Ground
Station (GS) to the On-Board Computer (OBC) of the WUST-Sat. The transfer
utilizes the CCSDS File Delivery Protocol (CFDP) for end-to-end file integrity.
The physical transmission from the GS to the satellite's S-Band module is via
QPSK modulated radio frames. Internally, CFDP Protocol Data Units (PDUs) are
relayed from the S-Band module to the OBC using the CANopen SDO Block Transfer
protocol over CAN FD.


## Glossary

**ACK/NAK** (Acknowledge/Negative Acknowledge) -- Control messages used in
acknowledged communication protocols (like CFDP) to indicate successful receipt
(ACK) or failure/error in receipt (NAK) of data.

**CAN FD** (Controller Area Network Flexible Data-Rate) -- An extension to the
original CAN bus protocol that allows for higher data bit rates and larger
message payloads (up to 64 bytes per frame). 

**CANopen FD** -- A higher-layer communication protocol and device profile
specification based on CAN FD. It standardizes communication between devices on
a CAN FD network.

**CCSDS** (Consultative Committee for Space Data Systems) -- An international
organization that develops standardized communication and data handling
protocols for space missions.

**CFDP** (CCSDS File Delivery Protocol) -- A CCSDS standard protocol designed
for reliable transfer of files in space communication environments, which can be
unreliable and experience significant delays.  
Relevant CCSDS Blue Book: [CCSDS 727.0-B-5: CCSDS File Delivery Protocol (CFDP)](https://ccsds.org/wp-content/uploads/gravity_forms/5-448e85c647331d9cbaf66c096458bdd5/2025/01//727x0b5e1.pdf)

**OD** (Object Dictionary) -- A standardized part of a CANopen device that
contains all accessible data within that device, including configuration
parameters, operational data (telemetry), commands, and device status. Each
entry is identified by a 16-bit index and an 8-bit sub-index.

**PDU** (Protocol Data Unit) -- A discrete unit of data specified by a CFDP. 
A file is broken down into a sequence of PDUs for transmission.

**QPSK** (Quadrature Phase Shift Keying) -- A digital modulation scheme that
conveys data by changing (modulating) the phase of a carrier signal. It uses
four phases, allowing two bits of data to be transmitted per symbol.

**SDR** (Software Defined Radio) -- A radio communication system where
components that have been traditionally implemented in hardware (e.g., mixers,
filters, amplifiers, modulators/demodulators) are instead implemented by means
of software on a personal computer or embedded system.

**SDO** (Service Data Object) -- A CANopen communication service used to access
entries in a device's Object Dictionary. SDOs allow for reading or writing data
of any size, often used for configuration and less frequent data exchange.
SDO Block Transfer is a specific mode for efficiently transferring larger blocks
of data.


## System Components Involved

**Ground Station (GS)**  
- A Linux-based server (e.g., Raspberry Pi).
- Equipped with a Software Defined Radio (e.g., PlutoSDR) capable of QPSK
  modulation and transmission.
- Runs a CFDP implementation capable of serializing files into PDUs.

**S-Band Communication Module**  
- Features a radio transceiver for S-Band communication.
- Receives radio signals from the GS.
- Demodulates and decodes the signals to recover CFDP PDUs.
- Transmits the recovered CFDP PDUs to the OBC via the internal CANopen FD bus.
- Acts as a CANopen FD device.

**On-Board Computer (OBC)**  
- Receives CFDP PDUs from the S-Band module via the CANopen FD bus.
- Reassembles the file from the CFDP PDUs.
- Performs CFDP procedural logic, including checksum verification.
- Saves the validated file to the specified location in its filesystem.
- Acts as a CANopen FD device and the CFDP receiving entity.


## Prerequisites

- A functional CFDP implementation is present on both the GS and the OBC.
- The S-Band module is configured for QPSK demodulation/decoding and can
  identify and extract CFDP PDUs from the incoming radio stream.
- The CANopen FD network within the satellite is operational.
- The S-Band module and the OBC have pre-configured Object Dictionary (OD) 
  entries and mappings to facilitate the transfer of CFDP PDU fragments.
- The CFDP Entity ID for the GS and the OBC are defined and known to both
  systems.


## File Transfer Process (Uplink)

The following steps describe the uplink of a single file:


### Ground Station

**Operator Command**  
The GS operator initiates the transfer by providing:
- The path to the source file on the GS.
- The target destination path (including filename) on the OBC's filesystem.

**CFDP PDU Serialization**  
- The GS's CFDP implementation reads the source file.
- The file is segmented and encapsulated into a sequence of CFDP PDUs
  (e.g., File Data PDUs, followed by an EOF PDU).
- Each PDU has a maximum size of 255 bytes.
- The EOF PDU will contain metadata such as the file checksum and total file
  size.

**QPSK Encoding and Radio Transmission**  
- Each CFDP PDU is treated as a payload for radio transmission.
- The PDU is encoded using QPSK modulation to form a radio message.
- The PlutoSDR (or equivalent) transmits these radio messages towards the
  satellite. This process is repeated for all PDUs constituting the file.


### S-Band Module

**Radio Reception and PDU Recovery**  
- The S-Band module's radio transceiver receives the incoming QPSK modulated
  radio messages.
- The module's processor demodulates and decodes each radio message to recover
  the raw CFDP PDU.
- It is assumed that the S-Band module's radio processing layer handles any
  necessary forward error correction to present a complete PDU.

**Preparing PDU for CAN Transfer**  
Once a CFDP PDU is recovered by the S-Band module, it must be transferred to
the OBC. As CAN FD frames have a maximum data payload of 64 bytes, the PDU 
(a byte array up to 255 bytes) must be fragmented.

**SDO Block Transfer Initiation**  
- The S-Band module, acting as a CANopen SDO Client, initiates an SDO Block
  Transfer write operation to the OBC, which acts as the SDO Server.
- This transfer targets a pre-defined Object Dictionary (OD) entry on the OBC
  designated for receiving incoming CFDP PDUs.

**PDU Segmentation and Transmission**  
- The CFDP PDU is the data block for the SDO Block Transfer.
- The CANopen stack on the S-Band module segments this PDU into multiple CAN FD
  frames, each with a payload of up to 64 bytes, as per the SDO Block Transfer
  protocol.
- These CAN FD frames are transmitted over the CAN bus to the OBC's Node-ID.


### On-Board Computer

**SDO Block Transfer Reception and PDU Reassembly**  
- The OBC's CANopen stack (acting as SDO Server) receives the incoming CAN FD
  frames constituting the SDO Block Transfer.
- It reassembles these segments to reconstruct the complete CFDP PDU (up to
  255 bytes) in its memory.

**CFDP PDU Processing and File Reconstruction**  
The reconstructed PDU is passed to the OBC's CFDP implementation. The CFDP
implementation processes the PDU according to its type. In the minimal,
simplified case we will transfer three types of PDUs:
- **Metadata PDU**: The data frame containing information about the start of
  transmission and the destination location of the file.
- **File Data PDUs**: The data payload is appended to the file being
  reconstructed in memory or a temporary location.
- **EOF PDU**: This PDU signals the end of the file transfer. It contains the
  file checksum and original file size.

**File Validation (Checksum) and Storage**  
Upon receiving and processing the EOF PDU, the OBC's CFDP implementation:
- Calculates the checksum of the reassembled file data.
- Compares the calculated checksum with the checksum received in the EOF PDU.
- Verifies the file size.

If the checksums match and other integrity checks pass, the reassembled file is
saved to the target path on the OBC's file system, as initially specified by
the GS operator. If checksums mismatch or other errors occur, the CFDP
protocol's error handling procedures would apply (e.g., sending a NAK PDU, which
is outside the scope of this specific uplink data flow description).


## Data Flow Summary

Single PDU from Radio to OBC:

```
1. GS Operator --> [File] --(CFDP Lib)--> [CFDP PDUs]

2. GS (PlutoSDR) --(QPSK Radio Frame carrying one PDU)--> Satellite Atmosphere

3. Satellite S-Band Module (Radio Rx) --(Demod/Decode)--> [Recovered CFDP PDU]

4. S-Band Module (CANopen Client) --[Segmented CAN FD Frames]--> OBC (CANopen Server)

5. OBC (CFDP Lib) --> [Reconstructed CFDP PDU] --(Process & Accumulate)--> [File on OBC FS]
```


## CANopen Configuration

Proper CANopen configuration is essential for the internal transfer of CFDP PDUs
between the S-Band module and the OBC.

**Node-IDs**  
The S-Band module and the OBC must each have a unique CANopen Node-ID, derived
from their respective 4-bit hardware IDs as defined in the [WUST-Sat Platform Architecture](./platform-communication.md)
document.

**OBC as SDO Server**  
The OBC must implement a CANopen SDO server.

A specific Object Dictionary (OD) entry must be defined on the OBC for receiving
incoming CFDP PDUs via SDO Block Transfer. This entry should be of type `DOMAIN`
or `OCTET_STRING` and capable of holding at least 255 bytes (the maximum CFDP
PDU size).

Example OD Entry:
- Index: `0x2F00` (User-specific application profile area)
- Sub-index: `0x01`
- Object Type: `DOMAIN` or `OCTET_STRING`
- Access: Writeable
- This entry must support SDO Block Transfer write operations.

**S-Band Module as SDO Client**  
The S-Band module must implement a CANopen SDO client. It must be configured
with the Node-ID of the OBC and the specific Index/Sub-index
(e.g., `0x2F00`, sub `0x01`) of the OD entry on the OBC designated for CFDP PDU
reception.

**SDO Block Transfer Parameters**  
The SDO Block Transfer protocol parameters, such as the number of segments per
block (BLKSIZE in CANopen), should be configured on both the S-Band module
(client) and the OBC (server) to ensure efficient transfer of the 255-byte PDUs
using CAN FD frames (up to 64 bytes payload each).

**XDC Files**  
The XML Device Configuration (XDC) files for both the S-Band module and the OBC
must accurately describe these SDO server/client capabilities and the relevant
Object Dictionary entries to facilitate network configuration and integration.


## CFDP Considerations

Several aspects of the CFDP protocol are pertinent to this file uplink procedure

**CFDP Entity IDs**  
- The GS acts as the source CFDP entity, and the OBC acts as the destination
  CFDP entity.
- Each entity is identified by a unique CFDP Entity ID, which is included in
  the header of every CFDP PDU.
- The S-Band module is assumed to be transparent to these CFDP Entity IDs;
  it merely transports the PDU byte stream from the radio interface to the
  CANopen interface.

**Transaction Context and Initiation**  
- The target path for the file on the OBC, initially specified by the GS
  operator, is typically conveyed within a CFDP Metadata PDU. This PDU is
  usually sent at the beginning of a CFDP transaction and includes fields for
  source and destination filenames. The OBC's CFDP implementation parses this
  to determine where to store the incoming file.
- The OBC's CFDP layer uses the combination of the source CFDP Entity ID and
  the Transaction Sequence Number (also part of the PDU header) to uniquely
  identify and associate incoming PDUs with the correct file transfer, allowing
  for concurrent or interleaved transactions if necessary (though typically one
  file transfer at a time is simpler).

**CFDP Mode**  
- The procedure described primarily implies the use of CFDP Unacknowledged Mode
  (Class 1). In this mode, the sender (GS) transmits all PDUs for a file without
  requiring an explicit acknowledgment for each PDU from the receiver (OBC).
- Reliability hinges on the end-to-end file checksum transmitted in the EOF PDU.
- If CFDP Acknowledged Mode (Class 2) were to be implemented, the OBC would need
  to generate and transmit Acknowledgment (ACK) or Negative Acknowledgment (NAK)
  PDUs back to the GS. This would necessitate a defined reverse data path
  (OBC -> S-Band Module -> Radio -> GS) and logic within the S-Band module to
  handle outgoing PDUs for radio transmission.

**Error Handling and Recovery**  
- **CFDP Layer**: CFDP includes robust error detection mechanisms, primarily the
  file checksum in the EOF PDU. If the calculated checksum on the OBC does not
  match the received checksum, the file is considered corrupt. The protocol can
  also detect missing or out-of-sequence PDUs based on their transaction
  sequence numbers.
- **Lower Layer Issues**: If a CFDP PDU is lost or irreparably corrupted during
  radio transmission or during the SDO Block Transfer over CAN, it will not be
  delivered intact to the OBC's CFDP layer. In Unacknowledged Mode, the CFDP
  layer on the OBC would likely detect this as a gap in the transaction sequence
  or ultimately through the final file checksum validation failure. There is no
  automatic retransmission request for individual lost PDUs from the OBC in this mode.

**File Metadata and EOF PDU**  
The **Metadata PDU** and the **EOF (End Of File) PDU** are critical.
- The Metadata PDU typically carries filenames, segmentation control, and other
  transaction parameters.
- The EOF PDU formally signals the completion of the file data transfer and
  contains the crucial file checksum and the original file size. The OBC uses
  this information to validate the integrity and completeness of the received
  file.

**PDU Size Constraint**  
The specified maximum CFDP PDU size of 255 bytes is a system-level constraint
for this implementation. This size influences how the GS segments the file and
how the S-Band module buffers and prepares data for the SDO Block Transfer.
CFDP itself can accommodate larger PDU sizes.
