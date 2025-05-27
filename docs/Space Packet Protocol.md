# CCSDS-compliant data frame
## Introduction
It is a summary of CCSDS 133.0-B-2, version June 2020, for the purpose of WUST SAT project. Detailed information can be found in CCSDS 133.0-B-2 Space Packet Protocol.
## Architecture
Space Packet Protocol is described by CCSDS 133.0-B-2 standard. In the document, bytes are called „octets”.

Each Space Packet must contain:

   1. Primary Header (6 octets);
   2. Data Field (1 – 65536 octets).

The figure 4-1 below shows how a Space Packet is built.

![SPP-packet](./SPP-packet.png)

In order to fit a whole Space Packet into a CAN-FD frame, its length should not exceed 64 octets. First 6 octets are taken by Primary Header, which leaves 58 bytes for data.

### Primary Header
   Primary Header is desribed in section 4.1.3 of CCSDS 133.0-B-2.

   Each Primary Header must contain:

1. Packet Version Number (3 bits) -- shall be set to ‘000’,
2. Packet Identification Field (13 bits),
3. Packet Sequence Control Field (16 bits),
4. Packet Data Length (16 bits).

The figure 4-2 below shows Primary Header’s built.

![SPP-primary](./SPP-primary-header.png)

**Packet Identification Field** contains:

a) Packet Type – ‘0’ for telemetry (reporting) or ‘1’ for telecommand (requesting).
b) Secondary Header Flag – ‘1’ if there is Secondary Header in the packet, ‘0’ otherwise
c) Application Process Identifier (APID) (11 bits) – shall provide the naming mechanism for the managed data path. The APID may uniquely identify the individual sending or receiving application process within the satellite. Differs between projects.

**Packet Sequence Control Field** contains:

a) Sequence Flags (2 bits) – options below:

|bits| meaning                             |
|----|-------------------------------------|
| 00 | a continuation segment of User Data |
| 01 | the first segment of User Data      |
| 10 | the last segment of User Data       |
| 11 | unsegmented User Data               |

b) Packet Sequence Count (if Packet Type == ‘0’) or Packet Name (if Packet Type == ‘1’) (14 bits) – package count per APID for ordering frames (modulo 16384).

c) Packet Data Length = (Total Number of Octets in the Packet Data Field) – 1

For 1 SPP per CAN-FD it is always (58 – 1) = 57

### Secondary Header
   Secondary Header is optional and usually contains datatime. Its place is just behind the Primary Header. There must be a Secondary Header, if there is no User Data in the Data Field. More in section 4.1.4.2 of CCSDS 133.0-B-2. Its built is shown on figure 4-3 below.

   ![SPP-secondary-header](./SPP-secondary-header.png)
