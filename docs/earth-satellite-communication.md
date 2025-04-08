---
title: Issues of Earth-satellite communication
author: Matylda Odrowąż-Wilkońska
date: 30.03.2025
version: v1.0.1
---

# Issues of Earth-Satellite Communication

## Dictionary

**Ground Station** (**GS**) -- A facility on Earth used to communicate with
satellites. Consists of a sufficiently large antenna and a properly tuned
transceiver. Its operational range defines a coverage area, often visualized as
a dome.

**Uplink** -- Data transfer from a ground station to a satellite.

**Downlink** -- Data transfer to a ground station from a satellite.

**Operator** -- The person responsible for managing satellite communication,
including scheduling communication sessions and sending commands to the
satellite.

**Transmission Window** -- The time period during which communication between
a specific ground station and a satellite is possible (satellite is within the
station's communication range). Its duration depends on the satellite's
trajectory relative to the ground station, particularly the maximum elevation
achieved during the pass.

**Elevation** -- The maximum angle (0° to 90°) during a pass, between the
satellite and the local horizon from the ground station perspecive. A higher
maximum elevation generally corresponds to a longer transmission window.

The image below shows satellite ground tracks within the ground station's range
for passes with different maximum elevations. From left to right: maximum
elevations of approximately ~10°, ~40°, and ~70°.
![elevation](./elevation.png)

**Communication Session** -- The period of active communication between the
satellite and the ground station occurring within a single transmission window,
during which data and commands are exchanged.

**Radio Bands** -- The division of radio waves into frequency bands, often
designated by convention or regulation based on usage.  
See: [https://en.wikipedia.org/wiki/Radio_spectrum#IEEE]

**UHF band** -- Band of frequencies in the 300 MHz to 1 GHz range. Due to fairly
low bandwidth, it is often used for command transmission and telemetry
collection.

**S-Band** -- Band of frequencies in the 2 GHz to 4 GHz range. Offers higher
bandwidth suitable for downloading larger data volumes (like images or files),
while also supporting command **uplink** and telemetry **downlink**.

**CCSDS** (Consultative Committee for Space Data Systems) -- an organization
developing generally accepted standards for space communications used by
agencies like NASA and ESA.

**CFDP (CCSDS File Delivery Protocol)** -- A **CCSDS** standard protocol
designed for reliable file transfer between spacecraft and ground systems.

**LoRa, LoRaWAN** -- A long-range, low-power communication protocol using
license-free sub-gigahertz frequencies: (e.g., 169 MHz, 433 MHz, 868 MHz).
Uses a proprietary spread spectrum modulation based on chirp spread spectrum
(CSS). Well-suited for intermittent, low-data-rate transmissions, but generally
not suitable for real-time applications or transferring large files due to
bandwidth limitations.

Official website of LoRa: [https://lora-alliance.org/about-lorawan/]

## Problems of Communication with a Satellite

The biggest obstacles in communication with a satellite include:

1. Limited opportunities for communication
2. Limited transmission window time
3. No direct access to hardware
4. Signal interference

### Limited Opportunities for Communication

Communication between the satellite and the ground station usually relies on
radio communication. Laser beam communication is under development.


### Limited Communication Time

At the LEO (500-600 km), **transmission windows** are quite short, often lasting
only several minutes per pass over a given **ground station**. The cause is
high orbital velocity of the satellite in orbit. At these altitudes, one
revolution around the Earth takes about 1.5 hours.

The duration of a **transmission window** depends on factors related to both
the satellite and the **ground station**.

**Relative Position**

The **ground station** rotates with the Earth, while the satellite orbits the
Earth. For successful data transfer, the satellite must pass within the
line-of-sight and communication range of the **ground station**. The maximum
communication distance depends on factors like transmitter power, antenna gain
(on both satellite and **ground station**), receiver sensitivity, and the chosen
data rate. It goes without saying that in order to communicate, the two devices
must use the same frequency, modulation, protocol, etc. At the same time, the
power of the transmitted signals must not be too high, to prevent interference
with other radio services.


**Frequencies**

The available frequencies are limited -- quite a few bands are reserved
by the military or telephony, among others. Bands available for satellite
communications, particularly hose suitable for small satellites can be congested.

Lower frequencies (longer wavelengths) are generally easier to establish and
maintain contact. They experience less atmospheric dispersion and path loss. 
Due to the small amount of energy required for propagation, successful
transmission is possible even without directing the satellite's antenna
toward the station. However, the same phenomenon limits the achievable data
rates, which means that the amount of data transmitted is small. Hence the 
main function of **UHF band** (often referring to the 430-440 MHz amateur
satellite allocation for CubeSats) is to collect telemetry and transmit
commands to the satellite.

For transferring larger files, ex. photos, higher frequency bands are
used, such as S-Band, X-Band (8000-8400 MHz), and recently also K-Band
(20 GHz). Since they contain more energy, fast transfer of even large
data sets is possible. In order to get the right level of energy, the
beam must be concentrated. Its beamwidth gets smaller and the
transmission windows gets fewer and shorter. To compensate for this, an
Attitude Determination and Control System (ADCS) is often used to make
the beam follow the ground station throughout the communication session.

Higher frequency transceivers are more difficult to build and operate,
and therefore more expensive.


### No Direct Access to Hardware

Manual repairs of hardware are not possible. Radio failure means the end
of the mission. A satellite from which no data can be received is dead.
Therefore, on most satellites there are 1 or even 2 backup radio
modules. Also software repairs and debugging are available only to a
limited extent.


### Interference

The causes of interference are either natural (like solar weather or
clouds) or artificial (other transmissions). There is no guarantee that
all transmitted packets will be received.

**Communication Ensurance**

There are special communication protocols adapted to space conditions.
One of them is the CCSDS standard. Thanks to Phase Shifting Key
modulation and correction coding methods, it is highly reliable and
allows, among other things, detection of communication errors and even
identification of a lost packet and retransmission. Unfortunately, these
methods require a lot of computing power and energy. The hardware must
include a processor or FPGA that allows real-time coding, with the time
synchronized to the CCSDS Time Code Formats standard. Such a
programmable radio is more expensive and on top of that, not every
ground station supports the CCSDS protocol.
