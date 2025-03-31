---
layout: post
title: Issues of Earth-satellite communication
author: Matylda Odrowąż-Wilkońska
date: 30.03.2025
version: v1.0
---

# Issues of Earth-satellite communication

## Dictionary

**ground station (GS)** -- an object on Earth used to communicate with a
satellite; consists of a sufficiently large antenna and a properly tuned
transceiver; has a limited range, usually dome-shaped

**uplink** -- data transfer from the Earth to space, from the ground
station to the satellite

**downlink** -- data transfer from space to the Earth, from the
satellite to the ground station

**operator** -- the person who manages communication with the satellite,
schedules communication sessions and sends commands (ex. downlink) to
the satellite during the session

**transmission window** -- the period during which the connection
between the ground station and the satellite is possible (satellite\'s
antenna is within range of the ground station's antenna); its duration
depends on satellites elevation above the horizon

**elevation** -- maximum angle \[0;90\]° above the horizon, at which the
satellite will be visible to the ground station; the higher the
elevation the longer the transmission window

The image below shows the differences in satellite\'s track within GS
range depending on the elevation. From the left: \~10°, \~40°, \~70°.
![elewacja](./elevation.png)

**communication session** -- the connection between
satellite and GS during one transmission window, during which data and
commands are exchanged

**radio bands** -- the division of radio waves into frequency bands,
mainly in terms of application, see:
*https://en.wikipedia.org/wiki/Radio_spectrum#IEEE*

**UHF band** -- herr: band of frequencies 300-1000 MHz; due to fairly
low bandwidth used mainly for command transmission and telemetry
collection

**S-Band** -- here: band of frequencies 2-4 GHz; used for downlink of
photos and bigger files, but also for command transmission and telemetry
collection

**CCSDS** -- an organization developing generally accepted standards for
space communications, called CCSDS standards, which are used by space
agencies such as NASA or ESA

**CFDP (CCSDS File Delivery Protocol)** -- CCSDS protocol for
communication between the satellite and the ground station

**LoRa, LoRaWAN** -- long-range, low-power communication protocol and
system operating on license-free frequencies: 169 MHz, 433 MHz, 868 MHz;
uses a proprietary spread spectrum modulation that is similar to and a
derivative of chirp spread spectrum (CSS) modulation; not suitable for
real-time services nor transferring large files

Official website of LoRa:
[*https://lora-alliance.org/about-lorawan/*](https://lora-alliance.org/about-lorawan/)


## Problems of communication with a satellite

The biggest obstacles in communication with a satellite are:

1.  Limited opportunities of communication
2.  Limited communication time
3.  No direct access to hardware
4.  Interference


### 1. Limited opportunities of communication 

Communication between the satellite and the ground station is usually by
radio. Laser beam communication is under development.


### 2. Limited communication time

At the LEO orbit (500-600 km) transmission windows are quite short,
about several minutes. The cause is movement of the satellite in orbit.
At this altitude one revolution around the Earth takes about 1.5 hours.

The duration of a transmission window depends on both the satellite and
the ground station.

**Relative position**

The satellite and the ground station are constantly moving relative to
each other. The station rotates around Earth's axis, while the satellite
orbits the Earth. For successful data transfer, the satellite must be
close enough above the station. The acceptable distance is determined
mainly by the technical capabilities of the transmitters, receivers and
antennas -- such as power, datarate etc. It goes without saying that in
order to communicate, the two devices must use the same frequency,
modulation, protocol, etc. At the same time, the power of the
transmitted signals must not be too high, so as not to interfere with
other devices.

**Frequencies**

The available frequencies are limited -- quite a few bands are reserved
by the military or telephony, among others. The remaining are heavily
used by amateurs or companies, including for satellite communications.

With longer wavelengths, it is easier to establish and maintain contact.
Due to the small amount of energy required for propagation, successful
transmission is possible even without directing the satellite\'s antenna
toward the station. But the same phenomenon means that the amount of
data transmitted is small. Hence the frequencies of the 350-450 MHz
band, customarily referred to as the UHF band, are mainly used to
collect telemetry and transmit commands to the satellite.

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


### 3. No direct access to hardware

Manual repairs of hardware are not possible. Radio failure means the end
of the mission. A satellite from which no data can be received is dead.
Therefore, on most satellites there are 1 or even 2 backup radio
modules. Also software repairs and debugging are available only to a
limited extent.


### 4. Interference

The causes of interference are either natural (like solar weather or
clouds) or artificial (other transmissions). There is no guarantee that
all transmitted packets will be received.

**Communication ensurance**

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
