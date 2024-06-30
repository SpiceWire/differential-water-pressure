# Water Pressure Sensor

This Arduino sketch reads water pressure sensors on each side of a water filter and alerts when the filter needs changing. 
It implements the MySensors library to send the sensor values through a gateway to a home automation program for
display and monitoring.

## Background

I wanted an objective way to determine if my whole-house water filter neeeded changing. I decided that I could tolerate a
15 psi (1 bar) drop in water pressure across the filter. 

I needed to measure pressure on both sides of the filter. There are a few factors to consider when measuring pressure drop:
flowing water has less pressure than stationary water, supply water pressure fluctuates during the day and between days,
and differing baseline voltages of the sensors. This sketch therefore measures and reports relative pressure
loss across the water filter, not the absolute pressure drop when water flows across the filter. 

This sketch is built on the Arduino [MySensors](https://www.mysensors.org/) library for open-source home automation which provides a protocol
for reading sensor data and transmitting it across a radio network. As MySensors does not have a native sensor protocol for "water pressure", 
this sketch treats the water pressure sensor as [multimeter](https://www.mysensors.org/download/serial_api_20) instead. Water pressure sensors often output
readings as analog voltage or current, making "multimeter" an appropriate equivalent. The sketch sends two values to the network: the greatest
reduction in pressure (as a percentage) recorded since last button puch, and the present measurement of 

The push button resets the variables and calibrates the sensors to each other. The sensors are zeroed to each other by 
taking a reading whenthe sensors have equal pressure, averaging the two values, and using each sensor's difference from the average as an offset. Before
pushing the button, ensure equal pressure at the sensors by either opening a bypass valve (if available) or by waiting a minute with no
water flow. 

The LED serves as an alert that the filter needs changing. The button resets it.

## Calibration 

Arduino analog pins return values from 0 to 1023. A 15 psi drop across the sensor is a difference of about 70-75 in Arduino's analog read.
Sensor output vs pressure is treated as linear. There is a 200 range in Arduino data covering the 40 psi of pressure, making about 5 
value counts per psi.

  For the water pressure sensors used, the following chart applies: 
   
   | PSI |   bar  |    Arduino read |
   | ---- | ---- | ---- |
   | 40 psi  | 2.75  |  304 |
   | 30 psi  | 2.06  |  247 |
   | 25 psi  | 1.72  |  230 |
   | 20 psi  | 1.38  |  200 |
   | 10 psi |  0.68  |  175 |
   | 5       | 0.34  |  165 |
   | 0            about 100 |

## Hardware

   * two 5v water pressure sensors (output 0.5 to 4.5 VDC) 
   * Arduino Nano 
   * momentary push button 
   * red LED
   * NRF24l01 radio module
   * Nano IO shield with nrf2401 socket
   * assorted hardware (jumper wire, screws, water fittings, etc.)

     
## Wiring
   Analog 1 is prefilter voltage.
   Analog 3 is postfilter voltage.
   Ground both sensors to Arduino ground. I used a separate power supply for both sensors.
   Long leg of LED is on digital 3, attached through a 220 ohm resistor. Short leg goes to Arduino ground.
   Button is on digital 2. Button push should make digital 1 read HIGH. See Arduino documentation for
      the use of a resistor to force the pin LOW.
   Radio uses digital pins 9, 10, 11, 12, 13 according to MySensors Wiring the Radio.
