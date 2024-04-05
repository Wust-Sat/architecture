# EPS - Electrical Power System

## System's goals 

- Charge the battery with more than one solar panel (panels could be mounted
  at different angles, so their power output could be different)
- Battery charger should use MPPT (maximum power point tracking)
- Current and voltage measurements on all power rails
- MCU can turn off each power rail individually when fault is detected
- Maximize power efficiency to reduce risk of overheating
- Ensure a positive energy balance
- The LoRa module can communicate via UART with the EPS to request powering on
  the OBC
- EPS and OBC can communicate via UART
- power to the OBC can be cut to save energy
- RBF (Remove Before Flight) circuit to ensure that the satellite starts
  operation only after ejection from the launch vehicle

## Success criteria

- Batteries charge from solar panels
- All output voltages are correct at full range of current
- All voltage rails can be turned off idividually when overcurrent is detected
  at given line
- UART communication works

## Current establishments

- 1S12P 18650 battery
- cheaper solar panels, with similar electrical parameters as pannels
  appropriate for work in space
- 5V and 3,3V power rails
