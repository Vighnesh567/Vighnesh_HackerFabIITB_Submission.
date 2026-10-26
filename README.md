# Vighnesh_HackerFabIITB_Submission.
Submission of Recruitment assignment.
DESIGNING A HOT PLATE FOR SEMICONDUCTOR PROCESSING.
Problems:
1.	Although most commercial hot plates can reach temperatures near 400°C, for semiconductor processing, we require the set temperature to the actual temperature error to be <5°C for precision heating.
2.	The temperature difference between two consecutive heating runs should be less than 0.5°C.
3.	The temperature difference between any two points on the 4” silicon wafer should be less than 0.5°C i.e. there should be uniformity of temperature across the hot plate.
4.	The top surface of the hot plate needs to be flat upto +-10um.


Solutions:
(1.	and 2.) To keep the actual temperature within 5°C of the set temperature, we require an accurate temperature sensing and control system. Normal Thermocouples CANNOT be used to detect temperatures for this purpose as they are not accurate enough and give error upto +-2°C. 
Thus, we can use an RTD PT100 (Resistance Temperature Detector). This works on the principle that metals resist current more and thus their resistance increases as they get hotter. This consists of a thin platinum element which has resistance exactly 100 Ω at 0°C and rises by about 0.385 Ω per °C (Resistance (100°C) = 138.5 Ω, Resistance (400°C) = 247 Ω).
Advantages of RTD:
•	Our key spec is minimal error term. RTD’s are far more accurate and do not drift over time unlike thermocouples.
•	The signal is stronger compared to thermocouples compared to thermocouples which send signals in µV.
Along with an RTD PT100, we will use a PID (Proportional Integral Derivative) Controller which measures the error between the set temperature and the measured temperature by the RTD and accordingly decides the heating elements power. This can keep the temperature stable within ±0.1°C to ±0.5°C.
Thus, the RTD and the PID controller work by forming a closed feedback loop. A step-by-step breakdown is given below:
I.	Sense: the PT100 sits in the plate. Its resistance depends on the plate’s temperature (about 0.385 Ω per °C).
II.	Convert: the MAX31865 (An interface which connects the PT100 to the microcontroller) passes a small current through the RTD, measures the voltage, compares it against its precision reference resistor, and sends a digital number to the microcontroller. The firmware converts resistance to temperature (e.g. 138.5 Ω → 100 °C).
III.	Compare: the controller computes error = setpoint − measured temperature. If the setpoint is 150 °C and the plate reads 142 °C, the error is +8 °C.
IV.	Decide: the PID turns that error into a heater power command (0 to 100%).
V.	Act: the command drives the SSR, which switches the heaters on and off. Heat flows into the plate. (SSR stands for Solid State Relay which is basically a semiconductor made electrical switch).
VI.	Repeat: the plate’s temperature changes, the RTD reports the new value, and the loop runs again (typically every 100 to 250 ms).
[Note: The SSR is used here as the microcontroller (ESP32 or Arduino) can only    provide voltage in the range of 3.3V to 5V. However, the heaters require much higher power input which can be provided by from the main supply via the SSR with the microcontroller deciding for how long it remains on and off after receiving the signal from the PID.]
[Another Note: One might assume that because the SSR is essentially a switch to the heating power supply, turning it on and off for a specific ratio of time will not lead to the desired continuous temperature output. But the thing to note is that the temperature will remain continuous although the power supply is not continuous (For example for 600 W for 10s, SSR turns on the full 1200W heater for 5s and off for 5s) due to THERMAL INERTIA of the thick Aluminium plate.]
Extra measures taken to achieve run-to-run repeatability of <0.5°C:
a)	We use a four wire RTD instead of a cheaper two wire one as in the two- wire case, the change in resistance of the connecting wires of the RTD interferes with the actual resistance change at higher temperatures and thus gives inaccurate temperature readings.
b)	We use a thick aluminium plate as the hot plate as its large thermal mass makes it resistant to rapid changes in temperature and disturbances caused by ambient temperature fluctuations. This helps us in improving repeatability.
c)	The heaters embedded in the hot plate remain in positions at fixed positions so that heat distribution remains same across different runs.
d)	To ensure that the entire plate has reached thermal equilibrium, the controller can check that for a set temperature t₀, the RTD measures t₀ +- 0.2°C for 300 seconds, only then the wafer is ready to be heated.
3. 	To tackle the issue of uniformity of temperature across the hot plate, we first need to realize the cause of temperature gradients across normal heat plates. They are-
a)	Creation of heat spots above the heating elements.
b)	Loss of heat through the edges of the hot plate due to radiation and conduction by the supports.
c)	Heat escaping due to conduction through the metallic frame and to the surroundings.
To address these problems, we have to meet the following structural specifications-
I.	We have to insulate the side of the hot plate using ceramic to prevent heat loss to the air through the sides.
II.	There should be ceramic fibre insulation between the heating elements and the housing so that heat does not escape through the bottom and goes directly to the hot plate. Also, this prevents overheating of the electronic components.
III.	Instead of a single heater, we use four symmetrically placed heaters to uniformly spread heat throughout the plate minimizing temperature gradients.
IV.	Aluminium has good thermal conductivity (170 W/mK) which allows good lateral flow of heat to ensure uniform temperature.

4.  To maintain good contact between the wafer and hot plate the surface of the plate should be extremely smooth upto +-10um. Ensuring this is important as air is a poor conductor of heat. If the surface is not smooth enough, due to uneven air gaps between the wafer and the plate their will be non-uniform heating of different points on the wafer defeating the purpose of a precise hot plate. 
To start, we use a heat treated, stress-relieved 6061-T651 25 mm thickness aluminium plate that is resistant to warping under heat. Then we rough machine the heater pockets to make space for the four symmetrically placed cartridge heaters and stress relieve it again. We then use a CNC machine to remove a thin topmost layer of the material. This is followed by grinding using a rotating wheel to further flatten the surface. The final and the most important step is Lapping where the aluminium plate is rubbed against and extremely flat reference plate with abrasive slurry to achieve the specified flatness target of +-10 μm across the wafer area.

5. Safety features:
I.	If temperature exceeds 420 °C at any point, the thermal fuse breaks to disconnect the power supply.
II.	The MCB protects the electronics against power failures and short circuits. 
III.	The metal housing is grounded.
IV.	An emergency stop button is used to stop the heating at any point in case of any unforeseeable issues. 


BOM:
Item	Qty	Approx Cost (₹)
6061-T651 Aluminium Plate (150 mm (diameter) × 25 mm (thickness)	1	2000
CNC Machining	1	3000
Surface Grinding + Lapping	1	2000
Cartridge Heater 400 W, 230 V	4	2400
PT100 Class-A 4-Wire RTD	1	1200
MAX31865 RTD Interface	1	800
ESP32 Development Board	1	500
SSR 25 A	1	700
SSR Heat Sink	1	300
Ceramic Fiber Insulation Board	1	1000
Aluminium Housing	1	2000
IEC Power Inlet	1	150
MCB/Fuse Holder	1	250
Thermal Fuse	1	250
Wiring and Connectors	1 set	500
Cooling Fan for Electronics	1	200
LCD/OLED Display (optional)	1	350
Emergency Stop Button	1	500

Approximate cost = 18,100 excluding assembly costs.



 



