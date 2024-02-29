# Mission Assumptions and Constraints 

The project is to create an Engineering Module of a satellite that meets the
following conditions:

1. The satellite is a CubeSat with a size of 3U, of which 2.5U is occupied by
the payload - a cylinder-shaped camera with a radius of 5cm and a length of
25cm. During the implementation of the project, it is possible to switch to a
smaller payload occupying 2U - with a radius of 4 cm and a length of 20 cm.

2. The satellite is to be placed in LEO orbit at an altitude of approximately
500 km above sea level. The mission is expected to last approximately 3 years.

3. The components must take into account the conditions occurring in orbit,
such as:
  - temperature at which they will operate,
  - eclipse durations and their impact on power management,
  - level of radiation,
  - atmospheric drag.

4. The power system should ensure a positive energy balance assuming one photo
per day is taken and transmitted to the ground station.

5. The project should meet formal and technical requirements such as:     
  - Compliance with space debris mitigation guidelines.
  - Radio frequency and spectrum use permissions.
  - CubeSat standards.

6. The satellite should be enable to communicate even if some components stop
working properly and allow the download of basic telemetry.

7. The prepared Engineering Module does not have to meet all the requirements,
but it must address them in such a way that they are ultimately met in next
iterations.
