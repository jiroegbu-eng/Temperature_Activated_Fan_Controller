# Design Rationale

## 1. NTC Thermistor Voltage Divider

The temperature sensor section uses a 10k NTC thermistor and a 10k resistor as a voltage divider. The NTC thermistor is connected to +12V, while the fixed 10k resistor is connected to ground. The midpoint between them is the `TEMP_SENSE` signal.

As temperature increases, the resistance of the NTC thermistor decreases. Because of this, the voltage at `TEMP_SENSE` rises. This changing voltage gives the LM358 a simple way to detect temperature changes.

## 2. LM358 Comparator Switching

The LM358 compares two voltages: the temperature voltage from `TEMP_SENSE` and the reference voltage from `REF_SET`.

`TEMP_SENSE` goes into the non-inverting input of the LM358, while `REF_SET` goes into the inverting input. When the temperature rises high enough and `TEMP_SENSE` becomes greater than `REF_SET`, the LM358 output switches high. That output signal then drives the MOSFET gate and turns the fan on.

## 3. Potentiometer Threshold Adjustment

The 10k potentiometer is used to set the fan turn-on threshold. Instead of using fixed resistor values for one fixed temperature point, the potentiometer lets the user adjust when the fan should start running.

This makes the circuit more flexible. The same board can be tuned for different cooling needs by turning the potentiometer.

## 4. 100k Hysteresis Resistor

The 100k resistor feeds a small amount of the LM358 output back into the `TEMP_SENSE` input. This adds hysteresis to the comparator.

Without hysteresis, the fan could rapidly turn on and off when the temperature is very close to the threshold. That kind of switching is called chattering. The 100k feedback resistor helps create a small gap between the turn-on and turn-off points, making the fan behavior more stable.

## 5. IRLIZ44N MOSFET Selection

The IRLIZ44N was selected because it is an N-channel MOSFET suitable for switching 12V fan loads. It is used as a low-side switch, meaning the fan is connected to +12V and the MOSFET controls the ground path.

This is a practical choice because the LM358 only needs to drive the MOSFET gate, while the MOSFET handles the actual fan current. The TO-220 package also gives useful current and heat-handling margin for a small fan controller.

## 6. Flyback Diode Protection

The fan is an inductive load because it contains a motor. When the MOSFET switches the fan off, the motor can produce a voltage spike as the magnetic field collapses.

The 1N4007 flyback diode gives that spike a safe path to circulate instead of forcing it through the MOSFET. The diode cathode is connected to +12V, and the anode is connected to the MOSFET drain side of the fan output. This helps protect the MOSFET from damage.

## 7. PCB Trace Width Choices

The fan current path uses wider traces because it carries more current than the signal section. The main +12V route, fan output route, and MOSFET ground return were routed with wider tracks to reduce resistance, voltage drop, and heating.

The LM358 input signals, potentiometer signals, gate control signal, and LED indicator traces carry very small currents, so smaller traces are enough for those connections. This keeps the board easier to route while still giving the power path enough copper.

## 8. Thermistor Placement Away from MOSFET and Fan Area

The thermistor was placed away from the MOSFET and fan switching area so it can measure ambient temperature more accurately.

The MOSFET and high-current fan traces can generate heat and electrical noise. If the thermistor is too close to that area, it may sense heat from the board instead of the surrounding air. Keeping the thermistor on the quieter side of the board helps the temperature reading stay more realistic.
