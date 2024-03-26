# ADCS Solutins

## Actuators:
Three magnetorquers, one for each axis, will be utilized for satellite 
position control.

### Magnetorquer:
Electromagnetic coils operating with the earth's magnetic field. The use of
3 coils in 3 axes allows full position control. A cheap solution and 
relatively easy to implement. Lightweight, and energy-efficient. Only two
axis control is aviable at any given time. If necessary, it can be combined 
with another solution.

## Sensors:
To determine the satellite's position, the method of fusing data from an 
accelerometer, gyroscope, and magnetometer is used. These sensors aid 
in identifying forces acting on the satellite, tracking rotational movements, 
and determining orientation relative to the Earth's magnetic field. To 
process data from sensors, it is necessary to use filtering methods such as 
the Kalman filter or complementary filter. By using filtering, sensor 
drift can be eliminated, and more stable satellite position data can be 
obtained.
### Gyroscope:
Precise measurement of angular velocity in three axes, particularly useful for 
tracking fast movements. Provides instantaneous measurement delivery. Prone to 
drift and sensitive to vibrations.
### Accelerometer: 
Measures linear acceleration in three axes, enabling orientation determination 
and motion monitoring. Susceptible to measurement noise, lacks information on 
angular orientation, and sensitive to vibrations
### Magnetometer:
Measurement of orientation relative to the Earth's magnetic field, 
complementing accelerometer and gyroscope measurements. Resistant to 
vibrations. Susceptible to external interference, such as electromagnetic 
coils. Calibration required.

## Communication:
- ADCS will send data from the IMU to the OBC,
- ADCS will receive data from the OBC on how to control the magnetorquers on each axis.

## Assumptions:
- microcontroller to control magnetorquers and read data from the IMU,
- three magnetorquers for position control,
- three H-bridges for coil control,
- gyroscope, accelerometer, and magnetometer for position determination,
- data filtering,
- power supply at 3.3V,
- maximum power consumption up to 1W.
- protection against overheating,
- heatsink if needed.

## Goals:
- determining satellite position,
- detumbling and stabilizing post-launch,
- pointing the camera towards Earth,
- maintaining desired position during missions,
- optimizing energy consumption,
- resistance to overheating,

## Sources 
- [Wikipedia](https://en.wikipedia.org/wiki/Spacecraft_attitude_control)
- [Satellite Wiki - Magnetorquers](https://www.aero.iitb.ac.in/satelliteWiki/index.php/Magnetorquers)
- [Satellite Wiki - ADCS](https://www.aero.iitb.ac.in/satelliteWiki/index.php/Attitude_Determination_and_Control_Subsystem)