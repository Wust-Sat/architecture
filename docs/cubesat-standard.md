---
title: Introduction to CubeSats
date: 08.04.2025
version: v1.0
---

# Introduction to CubeSats

A CubeSat is a type of miniaturized satellite, characterized by its standardized
size and form factor. The CubeSat unit, denoted as **1U**, is a cube measuring
10x10x10cm and typically weighing no more than 1.3 kg. CubeSats can be scaled by
combining basic units. This allows for scaled sizes such as **2U**, **3U**,
**6U**, etc.

Detailed requirements can be found in the [CubeSat Design Specification](https://www.cubesat.org/s/CDS-REV14_1-2022-02-09.pdf).

The aim of the standard is to provide a simple framework for the design,
construction, testing and launch of small satellites. It is significantly
cheaper to build and launch a CubeSat compared to traditional satellites. 

CubeSats are sent to outer space from standardized containers called deployers. 
This approach lowers the cost of access to space, making satellite missions 
feasible. This significantly shortens the time it takes to send a satellite into
orbit. CubeSats are often launched as secondary payloads on larger rockets.


## Dictionary

**unit** -- the basic 10x10x10 cm building block for CubeSats.

**deployer** -- a standardized deployment system designed to hold and release
CubeSats into orbit once the launch vehicle reaches the target altitude.

**platform** (**bus**) -- the main structure of the satellite that houses all
subsystems required for operation, excluding the payload. This typically
includes structure, electric power system, on-board computer, and communication
systems.

**payload** -- the mission-specific equipment carried by the satellite. This
could be a camera, scientific sensor, technology demonstration experiment,
communication relay, etc.

**ADCS** (Attitude Determination and Control System) -- the subsystem
responsible for determining the satellite's orientation (attitude) in space and
controlling it (e.g., pointing a camera or antenna). Components can include
sensors (magnetometers, gyros, startrackers) and actuators (reaction wheels,
magnetorquers).

**EPS** (Electrical Power System) -- the subsystem responsible for generating,
storing, and distributing electrical power to all other satellite components.
Typically includes solar panels, batteries, and a printed circuit board whose
purpose is to regulate and distribute energy.

**OBC** (On-Board Computer) -- the satellite's "brain." Main computing unit that
processes commands received from the ground station, manages data from the
payload and subsystems, executes automated sequences, and formats data for
downlink. Sometimes referred to as Command and Data Handling (C&DH) subsystem.

**Comms** (Communications System) -- the radio system (transceiver, antennas)
used to send data (telemetry) back to Earth and receive commands (telecommand)
from a ground station.

**ground station** -- the facility located on Earth equipped with antennas and
communication equipment to track the satellite, send commands, and receive data.

**LEO** (Low Earth Orbit) -- an orbit around Earth with an altitude typically
between 160 km and 2,000 km. Most CubeSats operate in LEO due to launch
constraints and mission objectives.


## Literature and Resources

* **cubesat.org**: Webpage with a list of current standards and most important
  documents. 
  * https://www.cubesat.org/cubesatinfo
  * [**CubeSat Design Specification**](https://www.cubesat.org/s/CDS-REV14_1-2022-02-09.pdf)
  * [**Nasa CubeSat 101**](https://www.nasa.gov/sites/default/files/atoms/files/nasa_csli_cubesat_101_508.pdf)

* **ESA Education - CubeSats**: Information on European Space Agency's CubeSat
  programs and activities.
  * https://www.esa.int/Education/CubeSats_-_Fly_Your_Satellite

* **Nanosats Database**: A list of launched and planned small satellites,
including CubeSats.
  * https://www.nanosats.eu/

* **Small Satellites Conference (SmallSat)**: Annual conference. Proceedings
are a great source of technical papers.
  * https://smallsat.org/
  * https://digitalcommons.usu.edu/smallsat/

* **Libre Space Foundation**: An umbrella organization for several open-source
space projects, including [SatNOGS](https://satnogs.org/) and [UPSat](https://archive.li/dVKLS).
  * [https://libre.space/](https://libre.space/)


## Other Open Source Satellite Projects

* **UPSat:** The first open-source hardware and software CubeSat. Design files
and software are available.
  * [https://upsat.gr/](https://archive.li/dVKLS)

* **SatNOGS** (Satellite Networked Open Ground Station): A global network of
open-source satellite ground stations. They also have hardware/software designs.
  * [https://satnogs.org/](https://satnogs.org/)
  * [https://wiki.satnogs.org/](https://wiki.satnogs.org/)
