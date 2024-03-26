# OBC - Onboard Computer

## System's requirements

- **Processing Power:** The OBC must have enough processing power to be able to handle the processing of the payload
- **Reliability:** OBC is able to operate in the harsh environment of space
- **Power Efficiency:** OBC is power-efficient to minimize power consumption and increase battery life
- **Communication with Modules:** Communicate with other modules by UART

## System's goals

- **Power-Efficient Design:** Manage to create a power-efficient system without additional services (bluetooth, WiFi and other modules)
- **Telemetric Data Collection:** The OBC collects telemetric data form the Attitude Determination and Control System (ADCS)
- **Redundancy and Failover:** The OBC should have redundant mechanisms and failover strategies
- **Communcation with Ground Station:** Communicate with the radio module to send data to the ground station
- **Usage of the Payload:** Operation of the payload and data acquisition
- **Minor System Updates:** The OBC is capable of getting minor system updates from the ground station
- **Sleep Mode:** A feature that allows the OBC to conserve power and be remotely awakened by commands from the ground station to resume operations
- **Autonomy Mode:** The OBC can enter in a autonomy mode to operate autonomously for extended periods when communication with the ground station is impossible
- **Log data:** The OBC logs data about the system status of other modules after being awakened remotely. The data is sent back to the ground station by the LORA module
- **ADCS control:** The OBC will process data from ADCS and send control commands to it

## Main candidates for OBC

- Rasperry Pi 4
- Rasperry Pi Zero 2
- Odroid modules (potentially more powerful but also power-hungry)

## Success criteria

- OBC has a stable operating system
- OBC is capable of waking from ground station command by radio
- OBC sends data to the ground station by radio
- Send and receive data from the payload (camera)

