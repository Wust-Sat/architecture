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


> **IMPORTANT** - satellites must use [435-438 MHz bandwidth](https://www.iaru-r1.org/wp-content/uploads/2021/03/UHF-Bandplan.pdf).

For communication with the ground station, it's best that both devices use the same type of module. This can ensure good compatibility.

##  Libraries

- For [Arduino](https://www.arduino.cc/reference/en/libraries/lora/) (there are plenty of libraries on the internet)
- For STM32: [link1](https://github.com/SMotlaq/LoRa), [link2](https://github.com/wdomski/SX1278)

## LoRa radio diagram

 ![Radio diagram](radio_diagram.png)

 > *Note*: Some MCUs have an internal watchdog, such as STM32.

# Resources

-   [Wikipedia](https://en.wikipedia.org/wiki/LoRa)
-   [FossaSat-1 - satellite utilizing LoRa](https://github.com/FOSSASystems/FOSSASAT-1)
-   [TinyGS, Open Source Global Satellite Network](https://tinygs.com/)
-   [Store and Forward CubeSat using LoRa Technology and Private LoRaWAN-Server](https://digitalcommons.usu.edu/cgi/viewcontent.cgi?article=4657&context=smallsat)

