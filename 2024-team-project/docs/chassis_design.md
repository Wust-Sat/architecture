# Chassis design requirements and module integration
## System's goals
- provide slim, but rigid framework for all other satellite components
- ensure compatibility with CubeSat specification and launcher requirements
- maximize solar panel light exposure with hinged side panels
- enable radio communication with expandable antenna

## Success criteria
- successful integration of all submodules
- working solar panel unfolding system
- working antenna deployment system

## Design decisions
### Envelope
CubeSat chassis will conform to base 3U specification utilizing the rail system
and without any extensions. To enable quick iteration and reduce unnecessary
costs parts will be manufactured with a FFF 3D printer.

### Internals
CubeSat will be divided in two major sections:
- 0.5/1u - electronics supporting orbital operation
- 2.5/2u - payload

Exact proportions are yet to be determined. Space not occupied by the round
payload will be filled with li-ion cells. Each corner should have space for 3
cells (12 in total).

### Solar panels
All but one of Z panels will be covered with solar cells. To maximize solar
irradience Y and X sides will be hinged at the short end. After satellite
deployment panels will pop up creating a flat cross facing the sun. Due to
unavailability of space-grade panels tests will be conducted with mockups of
similar weight and dimensions.

### Antenna
Like solar panels, radio communication antenna has do be stowed while inside of
the deployer. Its deployment mechanism has to compatible with the design of the
chassis.
