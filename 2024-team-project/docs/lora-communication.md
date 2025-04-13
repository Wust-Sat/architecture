# LoRaWAN in space

LoRaWAN seems to offer numerous benefits over traditional data transmission solutions.

-   **Long Range:** transmissions over 2000km have been reported.
-   **Low barrier to entry:** cheap off the shelf ground station equipment is available.
-   **World-wide infrastructure:** there are over 1500 ground stations in the Open Source Global Satellite Network.
-   **High power efficiency:** LoRaWAN IoT devices can operate on battery power for years.
-   **Free frequency bands:** no license required (**only free bandwidths**).

Its main drawback is low data rate, but it should be enough for telemetry.


## Technical data


|Technical data | |
|---|---|
|Max range | 2000 km | 
|Data rate| 200 bps  (to space) |
|Link budget| 155-117 dB |
|Frequencies| 169 MHz, 433 MHz, 868 MHz (EU), 915MHz (US)|
|Communication protocol with MCU| SPI | 

## LoRa modules

- [LoRa SX1278](https://nettigo.pl/products/modul-lora-ra-02-sx1278-433mhz) - used in FOSSAT-1 with Atmega328
- [LoRa SX1301](https://www.semtech.com/products/wireless-rf/lora-core/sx1301)
- [RFM98PW-433S2](https://www.tme.eu/pl/details/rfm98pw-433s2/moduly-rf/hope-microelectronics/) - high output power (27 dBm)


> **IMPORTANT** - satellites must use [435-438 MHz bandwidth](https://www.iaru-r1.org/wp-content/uploads/2021/03/UHF-Bandplan.pdf).

For communication with the ground station, it's best that both devices use the same type of module. This can ensure good compatibility.

##  Libraries

- For [Arduino](https://www.arduino.cc/reference/en/libraries/lora/) (there are plenty of libraries on the internet)
- For STM32: [link1](https://github.com/SMotlaq/LoRa), [link2](https://github.com/wdomski/SX1278)

# Radio structure

Radio module will contain a microcontroler, that will communicate with
LoRa via SPI. An external watchdog will be connected to the controler,
to reset the MCU, when it crashes. A dipole antenna will be connected to LoRa.
The MCU will communicate with the OBC and EPS via UARTs.

In this scenario, LoRa will be used to send telemetry from the EPS and the OBC.
## Power

Depending on which LoRa will be used, there are two power options.
Despite everything, the board must be powered **all the time**, because it 
must provide a connection between the Earth and the Cubesat.

### For LoRa SX1278

MCU and LoRa must be supplied with 3.3 V, but the LoRa should be powered
through an LDO voltage stabilizer. The maximum power consuption is 1W.

### For RFM98PW-433S2

The Lora must be supplied with 5 V. Thanks to the built-in LDO voltage stabilizier,
the MCU can be powered from Lora with 3.3 V. The maximum power consuption is 4.5W.

## LoRa radio diagram

### For LoRa SX1278
 ![LoRa SX1278 diagram](radio_diagram.png)
 
### For RFM98PW-433S2
 ![RFM98PW-433S2 diagram](radio_diagram2.png)

 > *Note*: For redundancy, it is better to use an external watchdog.

# Radio distance calculation

To calculate the radio distance, you must first calculate a Free Space Loss.
After calculating Free Space Loss, you can calculate maximum range.

You can use a [spreadsheet](Distance_calc.ods) to calculate the theoretical maximum radio range.

## Free Space Loss 

Free Space Loss represents how much power loss you can tolarate in-between
transmitting antenna and receving antenna. The equation that calculates Free Space Loss is shown below:

$$ L_{fs} = P_{tx} + G_{tx} - L_{tx} - L_{M} + G_{rx} - L_{rx} - P_{rx} $$

where:

- $L_{fs}$ -- Free Space Loss (expressed in dB).
- $P_{tx}$ -- Transmition output power (in dBm).
- $G_{tx}$ -- Transmitter antenna Gain (in dBi).
- $L_{tx}$ -- Transmitter losses (in dB). Contains all loses cables, connectors etc.
- $L_{M}$ -- Miscellaneous Losses (in dB), Contains all loses like fading margin,
body loss, polarization mismatch, etc.
- $G_{rx}$ -- Receiver antenna Gain (in dBi).
- $L_{rx}$ -- Receiver loss (in dB). Same as Transmitter losses.
- $P_{rx}$ -- Receiver sensitivity (in dBm). The minimum amount of RF 
power that receiver can detect.

## Range calculation

If you want calculate radio link distance, you have to use this equation:

$$d = \frac{10^{\frac{(L_{fs}-M)}{20}}}{4\pi}\cdot \lambda$$

where:

- $L_{fs}$ -- Free Space Loss (in dB).
- $M$ -- Margin (in dB). If zero, equation show ideal maximum range.
- $\lambda$ -- Wavelenght (expressed in meters).
# Resources

-   [LoRa - Wikipedia](https://en.wikipedia.org/wiki/LoRa)
-   [FossaSat-1 - satellite utilizing LoRa](https://github.com/FOSSASystems/FOSSASAT-1)
-   [TinyGS, Open Source Global Satellite Network](https://tinygs.com/)
-   [Store and Forward CubeSat using LoRa Technology and Private LoRaWAN-Server](https://digitalcommons.usu.edu/cgi/viewcontent.cgi?article=4657&context=smallsat)
-   [Link budget - Wikipedia](https://en.wikipedia.org/wiki/Link_budget)
-   [Example of radio distance calculation](https://www.rcgroups.com/forums/showatt.php?attachmentid=3819041)

