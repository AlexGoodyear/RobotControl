<div align = center>

# [PWMMotorControl](https://github.com/ArminJo/PWMMotorControl)
Arduino library to control brushed DC motors by PWM.<br/>
It uses optional attached encoders to drive fixed distances. 

[![Badge License: GPLv3](https://img.shields.io/badge/License-GPLv3-brightgreen.svg)](https://www.gnu.org/licenses/gpl-3.0)
 &nbsp; &nbsp;
[![Badge Version](https://img.shields.io/github/v/release/ArminJo/PWMMotorControl?include_prereleases&color=yellow&logo=DocuSign&logoColor=white)](https://github.com/ArminJo/PWMMotorControl/releases/latest)
 &nbsp; &nbsp;
[![Badge Commits since latest](https://img.shields.io/github/commits-since/ArminJo/PWMMotorControl/latest?color=yellow)](https://github.com/ArminJo/PWMMotorControl/commits/master)
 &nbsp; &nbsp;
[![Badge Build Status](https://github.com/ArminJo/PWMMotorControl/workflows/LibraryBuild/badge.svg)](https://github.com/ArminJo/PWMMotorControl/actions)
 &nbsp; &nbsp;
![Badge Hit Counter](https://visitor-badge.laobi.icu/badge?page_id=ArminJo_PWMMotorControl)
<br/>
<br/>
[![Stand With Ukraine](https://raw.githubusercontent.com/vshymanskyy/StandWithUkraine/main/badges/StandWithUkraine.svg)](https://stand-with-ukraine.pp.ua)

Available as [Arduino library "PWMMotorControl"](https://www.arduinolibraries.info/libraries/pwm-motor-control).<br/>
Contains the [RobotCarBlueDisplay](https://github.com/ArminJo/Arduino-RobotCar) example.

[![Button Install](https://img.shields.io/badge/Install-brightgreen?logoColor=white&logo=GitBook)](https://www.ardu-badge.com/PWMMotorControl)
 &nbsp; &nbsp;
[![Button API](https://img.shields.io/badge/API-yellow?logoColor=white&logo=OpenStreetMap)](https://github.com/ArminJo/PWMMotorControl#api)
 &nbsp; &nbsp;
[![Button Changelog](https://img.shields.io/badge/Changelog-blue?logoColor=white&logo=AzureArtifacts)](https://github.com/ArminJo/PWMMotorControl#revision-history)

</div>

#### If you find this library useful, please give it a star.

<br/>

# Features
- The PWMDcMotor.cpp controls **brushed DC motors** by PWM using standard full bridge IC's like **[L298](https://www.instructables.com/L298-DC-Motor-Driver-DemosTutorial/)**, [**SparkFun Motor Driver - Dual TB6612FNG**](https://www.sparkfun.com/products/14451), or **[Adafruit_MotorShield](https://www.adafruit.com/product/1438)** (using PCA9685 -> 2 x TB6612).
- The EncoderMotor.cpp.cpp controls a DC motor with attached encoder disc and slot-type photo interrupters to enable **driving a specified distance**.
- The CarPWMMotorControl.cpp controls **2 motors simultaneously** like it is required for most **Robot Cars**.
- To **compensate for different motor characteristics**, each motor can have a **positive** compensation value, which is **subtracted** from the requested speed PWM if you use the `setSpeedPWMCompensation()` functions. For car control, only compensation of one motor is required.

### The motor is mainly controlled by 2 dimensions:
1. **Direction** / motor driver control. Can be FORWARD, BACKWARD, BRAKE (motor connections are shortened) or RELEASE (motor connections are high impedance).
2. **SpeedPWM** which is ignored for BRAKE or RELEASE. Some functions allow a signed speedPWM parameter, which includes the direction as sign (positive -> FORWARD).

<br/>

# API
#### Basic commands are:
- `init(uint8_t aForwardPin, uint8_t aBackwardPin, uint8_t aPWMPin)`.
- `setDirection(uint8_t aMotorDirection)`.
- `setSpeedPWM(uint8_t aSpeedPWM)`.
- `setSpeedPWMAndDirection(uint8_t aRequestedSpeedPWM, uint8_t aRequestedDirection)`.
- `setSpeedPWMAndDirection(int SignedRequestedSpeedPWM)`.
- `stop()` or `setSpeedPWMAndDirection(0)`.
- `startRampUp(uint8_t aRequestedDirection)`.
- `getSpeed()`, `getAverageSpeed()`,  `getDistanceMillimeter()` and `getBrakingDistanceMillimeter()` for **encoder motors or MPU6050 IMU** equipped cars.

#### Functions to go a specified distance:
Driving speed PWM (2.0 V) is the PWM value to use for driving a fixed distance. If it is set higher than `RAMP_VALUE_OFFSET_SPEED_PWM` (2.3 V), the software generates a ramp up from `RAMP_VALUE_OFFSET_SPEED_PWM` to requested driving speed PWM at the start of the movement and a ramp down to stop.
- `goDistanceMillimeter(uint8_t aRequestedSpeedPWM, unsigned int aRequestedDistanceMillimeter, uint8_t aRequestedDirection);`<br/>
For **non encoder motors, distance and drive speed PWM is used in a formula to convert millimeters into motor driving time**.
- `updateMotor()` - call this in your loop if you use the start* functions e.g. `startGoDistanceMillimeter(int aRequestedDistanceMillimeter)`.

#### Functions for cars controlling 2 motors / motor sets from CarPWMMotorControl.hpp.
- `rotate(int aRotationDegrees, turn_direction_t aTurnDirection, bool aUseSlowSpeed, void (*aLoopCallback)(void))`.
- Plus all functions from above like `setSpeedPWM()` etc. They now affect both motors.

<br/>

# Pictures
| 4WD car with IR receiver and Bluetooth module and 4 AA rechargeable batteries. | Instructable |
|-|-|
| ![4 wheel car](https://github.com/ArminJo/PWMMotorControl/blob/master/pictures/4WDInstructable.jpg) | [![Instructable](https://github.com/ArminJo/Arduino-OpenWindowAlarm/blob/master/pictures/instructables-logo-v2.png)](https://www.instructables.com/Arduino-4WD-Car-Assembly-and-Code-With-Optional-In/) |

<br/>

# Full bridges
This library was tested with the bipolar full bridge IC L298 and the (recommended) MOSFET full bridge IC TB6612.

| The L298 has a loss of around 2 volt, which is the reason for the attached heat sink | The TB6612 has almost no loss |
| :-: | :-: |
| ![L298 board](https://github.com/ArminJo/PWMMotorControl/blob/master/pictures/L298Board_small.jpg) | ![TB6612 board](https://github.com/ArminJo/PWMMotorControl/blob/master/pictures/TB6612Board_small.jpg) |
| L298 voltages at both motor pins @7.5 V | TB6612 effective motor voltage @7.6 V |
| ![L298 output voltages](https://github.com/ArminJo/PWMMotorControl/blob/master/pictures/analytic/7.5V_L298-PWM87-2Channel.bmp) | ![TB6612 motor voltage](https://github.com/ArminJo/PWMMotorControl/blob/master/pictures/analytic/7.6V_TB6612-PWM67.bmp) |

<br/>

# PWM period
- PWM period is 600 &micro;s (1.6 kHz) for Adafruit Motor Shield V2 using PCA9685.
- PWM period is 1030 &micro;s (970 Hz) for AnalogWrite on pin 5 + 6.

<br/>

# Examples for this library
The examples are available at File > Examples > Examples from Custom Libraries / PWMMotorControl.<br/>

## Start
One motor starts with DriveSpeedPWM / 2 for one second, then runs 1 second with DriveSpeedPWM.
After stopping the motor, it tries to run for one full rotation (resulting in a 90 degree turn for a 2WD car). Then the other motor runs the same cycle.
For the next loop, the direction is switched to backwards.

## Square
4 times drive 40 cm and 90 degree left turn. After the square, the car is turned by 180 degree and the direction is switched to backwards. Then the square starts again.

## PrintMotorDiagram
This example prints **PWM, speed and distance / encoder-count** diagram of an encoder motor. The encoder increment is inverted at falling PWM slope to show the quadratic kind of encoder graph. Timebase is 20 ms per plotted value.
| Diagram for free running motor controlled by an MosFet bridge supplied by 7.0 volt | Diagram for free running motor controlled by an L298 bridge supplied by 7.6 volt |
| :-: | :-: |
| ![7.0V MosFet free run](https://github.com/ArminJo/PWMMotorControl/blob/master/pictures/analytic/7.0V_MosFet_FreeRun.png) | ![7.6V L298 free run](https://github.com/ArminJo/PWMMotorControl/blob/master/pictures/analytic/7.6V_L298_FreeRun.png) |
| Here you see that the speed is proportional to the PWM, but the minimal power to start the motor is 33/255 = 13% PWM or 0.9 volt. | Due to losses and other effects in the L298, the start voltage is much higher. |
| | |
| **MosFet bridge supplied by only 3.5 volt** | **Start diagram for L298 with 6.2 volt** |
| ![3.5V MosFet free run](https://github.com/ArminJo/PWMMotorControl/blob/master/pictures/analytic/3.5V_MosFet_FreeRun.png) | ![7.6V L298 free run](https://github.com/ArminJo/PWMMotorControl/blob/master/pictures/analytic/6.2V_L298_FreeRun.png) |
| Here, the start voltage can be observed better.<br/>There is no stop at the same voltage, so the distance gets virtually negative. | Higher start voltage and a non linear speed / PWM ratio here. |

## TestMotorWithIMU
Prints **PWM, speed and distance** diagram of the right (encoder) motor of a car equipped with a MPU6050 IMU.
Encoder and IMU (MPU6050) data are printed simultaneously, to compare and to detect slipping.<br/>
It does a start and stop car first with and second without ramp.
It starts with `DEFAULT_DRIVE_SPEED_PWM` and doubles speed for next turn until `MAX_SPEED_PWM`.

| Diagram for car controlled by an MosFet bridge | Diagram for car controlled by an L298 bridge |
| :-: | :-: |
| ![2WD Smart Car](https://github.com/ArminJo/PWMMotorControl/blob/master/pictures/analytic/7.4V_PrintCarValuesWithIMU_Encoder.png) | ![Lafvin car](https://github.com/ArminJo/PWMMotorControl/blob/master/pictures/analytic/Lafvin_Test.png) |

## RobotCarBasic
Template for your RobotCar control. Currently implemented is: Drive until distance too low, then stop, go backwards and turn random amount.

## BasicIRControl
Implements basic car control by an IR remote. Mapping between keys of any IR remote sending NEC protocol (all the cheap china ones) and car commands can be done in IRCommandMapping.h.<br/>
To support mapping, the received IR code is printed at the serial output if `INFO` is defined.

## SmartCarFollowerSimple
The car tries to hold a distance between 30 and 40 cm to an obstacle. Only forward and back movement, no turn!
The measured distance range is converted to a pitch as an acoustic feedback.

## SmartCarFollower
The car tries to hold a distance between 30 and 40 cm to an target.
If the target vanishes, the distance sensor scans to get the vanished (or a new) target.

## [RobotCarBlueDisplay](https://github.com/ArminJo/Arduino-RobotCar)
Requires also the Arduino library [BlueDisplay](https://github.com/ArminJo/Arduino-BlueDisplay).

Enables autonomous driving of a 2 or 4 wheel car with an Arduino.<br/>
To avoid obstacles a HC-SR04 Ultrasonic sensor mounted on a SG90 Servo continuously scans the environment.
Manual control is implemented by a GUI using a Bluetooth HC-05 Module and the BlueDisplay library.

<br/>

# Compile options / macros for this library
To customize the library to different requirements, there are some compile options / macros available.<br/>
These macros must be defined in your program **before** the line `#include <CarPWMMotorControl.hpp>`, `#include <EncoderMotor.hpp>` or `#include <PWMDcMotor.hpp>`to take effect.<br/>
Modify them by enabling / disabling them, or change the values if applicable.

| Name | Default value | Description |
|-|-:|-|
| `USE_ENCODER_MOTOR_CONTROL` | disabled | Use slot-type photo interrupter and an attached encoder disc to enable motor distance and speed sensing for closed loop control. |
| `USE_MPU6050_IMU` | disabled | Use GY-521 MPU6050 breakout board connected by I2C for support of precise turning and speed / distance calibration. Connectors point to the rear. Requires up to 2850 bytes program memory if `USE_SOFT_I2C_MASTER` defined and 3756 bytes if `USE_SOFT_I2C_MASTER` is not defined. |
| `USE_ACCELERATOR_Y_FOR_SPEED` | disabled | The y axis of the GY-521 MPU6050 breakout board points forward / backward, i.e. connectors are at the left / right side. |
| `USE_NEGATIVE_ACCELERATION_FOR_SPEED` | disabled | The speed axis of the GY-521 MPU6050 breakout board points backward, i.e. connectors are at the front or right side. |
| `USE_ADAFRUIT_MOTOR_SHIELD` | disabled | Use Adafruit Motor Shield v2 connected by I2C instead of simple TB6612 or L298 breakout board.<br/>This requires only 2 I2C/TWI pins in contrast to the 6 pins used for the full bridge.<br/>For full bridge, the millis() timer0 is used for analogWrite since we use pin 5 & 6. |
| `USE_STANDARD_LIBRARY_`<br/>`ADAFRUIT_MOTOR_SHIELD` | disabled | Enabling requires additionally 694 bytes program memory. |
| `DO_NOT_SUPPORT_RAMP` | disabled | Enabling saves 378 bytes program memory. |
| `DO_NOT_SUPPORT_AVERAGE_SPEED` | disabled | Enabling disables the function getAverageSpeed() and saves 44 bytes RAM per motor and 156 bytes program memory. |
| `USE_SOFT_I2C_MASTER` | disabled | Saves up to 2110 bytes program memory and 200 bytes RAM for I2C communication to Adafruit motor shield and MPU6050 IMU compared with Arduino Wire. |
| `ENABLE_MOTOR_LIST_FUNCTIONS` | disabled | Enables the convenience functions `*AllMotors*()` and `*forAll()`. Requires up to additional 80 bytes program space and 7 bytes RAM. |

## Default car geometry dependent values used in this library
These values are for a standard 2 WD car as can be seen on the pictures below.
| Name | Default value | Description |
|-|-|-|
| `CAR_HAS_4_WHEELS` | disabled | Use modified formula for turning the car. |
| `CAR_HAS_4_MECANUM_WHEELS` | disabled | Use different setDirection() and modified values for going fixed distances. |
| `DEFAULT_CIRCUMFERENCE_MILLIMETER` | 220 | At a circumference of around 220 mm this gives 11 mm per count. |
| `ENCODER_COUNTS_PER_FULL_ROTATION` | 20 | This value is for 20 slot encoder discs, giving 20 on and 20 off counts per full rotation. |
| `MILLIMETER_PER_DEGREE_DEFAULT` | 2.2777 for 2 wheel drive cars, 4.1 for 4 WD cars and 2.2 for mecanum wheel cars. | Reflects the geometry of the standard 2 WD car or mecanum cars sets. The 4 WD car value is estimated for slip on smooth surfaces. |

## Other default values for this library
These values are used by functions and some can be overwritten by set* functions.
| Name | Default value | Description |
|-|-|-|
| `VIN_2_Li-ion` | undefined | If defined sets `FULL_BRIDGE_INPUT_MILLIVOLT` to 7400. |
| `VIN_1_Li-ion` | undefined | If defined sets `FULL_BRIDGE_INPUT_MILLIVOLT` to 3700. |
| `FULL_BRIDGE_INPUT_`<br/>`MILLIVOLT` | 6000 or 7400 if `VIN_2_Li-ion` is defined | The supply voltage used for the full bridge. |
| `USE_L298_BRIDGE ` | undefined | If defined, sets `FULL_BRIDGE_LOSS_MILLIVOLT` to 2.0 volt. |
| `FULL_BRIDGE_LOSS_`<br/>`MILLIVOLT` | 0 or 2000 if `USE_L298_BRIDGE` is defined | The internal voltage loss of the full bridge used, typically 0 volt for mosfet and 2 volt for bipolar bridges like the L298. |
| `FULL_BRIDGE_OUTPUT_`<br/>`MILLIVOLT` | `(FULL_BRIDGE_INPUT_MILLIVOLT - FULL_BRIDGE_LOSS_MILLIVOLT)` | The effective voltage available for the motor. |
| `DEFAULT_START_`<br/>`MILLIVOLT` | 1100 | The DC Voltage at which the motor start to move / dead band voltage. |
| `DEFAULT_DRIVE_`<br/>`MILLIVOLT` | 2000 | The derived `DEFAULT_DRIVE_SPEED_PWM` is the speed PWM value used for fixed distance driving. |
| `DEFAULT_MILLIMETER_`<br/>`PER_SECOND` | 320 | Value at DEFAULT_DRIVE_MILLIVOLT motor supply. A factor used to convert distance to motor on time in milliseconds using the formula:<br/>`MillisForDistance = 20 + (RequestedDistanceMillimeter * MillisPerMillimeter * DriveSpeedPWM / DEFAULT_DRIVE_SPEED_PWM)` |

## Compile options / macros for RobotCarBlueDisplay example
To customize the software to different car configurations, there are some compile options / macros available.<br/>

| Name | Default value | Description |
|-|-|-|
| `CAR_HAS_VIN_VOLTAGE_DIVIDER` | undefined | VIN/11 at A2, e.g. 1 M&ohm; to VIN, 100 k&ohm; to ground. Required to show and monitor (for undervoltage) VIN voltage. |
| `VIN_VOLTAGE_CORRECTION` | undefined or 0.8 for Uno | Voltage to be subtracted from VIN voltage for voltage monitoring. E.g. if there is a series diode between Li-ion and VIN as on the Uno boards, set it to 0.8. |
| `DISTANCE_SERVO_IS_MOUNTED_HEAD_DOWN` | disabled | Distance.h | The distance servo is mounted head down to detect even small obstacles. The Servo direction is reverse then. |
| `CAR_HAS_US_DISTANCE_SENSOR` | disabled | A HC-SR04 ultrasonic distance sensor is mounted (default for most China smart cars). |
| `US_SENSOR_SUPPORTS_1_PIN_MODE` | disabled | Use modified HC-SR04 modules or HY-SRF05 ones.</br>Modify HC-SR04 by connecting 10 k&ohm; between echo and trigger and then use only trigger pin. |
| `CAR_HAS_IR_DISTANCE_SENSOR` | disabled | Use Sharp GP2Y0A21YK / 1080 IR distance sensor. |
| `CAR_HAS_TOF_DISTANCE_SENSOR` | disabled | Use VL53L1X TimeOfFlight distance sensor. |
| `CAR_HAS_DISTANCE_SERVO` | disabled | Distance sensor is mounted on a pan servo (default for most China smart cars). |
| `CAR_HAS_PAN_SERVO` | disabled | Enables the pan slider for the `PanServo` at the `PIN_PAN_SERVO` pin. |
| `CAR_HAS_TILT_SERVO` | disabled | Enables the tilt slider for the `TiltServo` at the `PIN_TILT_SERVO` pin. |
| `CAR_HAS_CAMERA` | disabled | Enables the `Camera` button for the `PIN_CAMERA_SUPPLY_CONTROL` pin. |
| `CAR_HAS_LASER` | disabled | Enables the `Laser` button for the `PIN_LASER_OUT` / `LED_BUILTIN` pin. |
| `ENABLE_RTTTL_FOR_CAR` | undefined | Plays melody after initial timeout has reached. Enables the Melody button, which plays a random melody. |
| `MONITOR_VIN_VOLTAGE` | disabled | Shows VIN voltage and monitors it for undervoltage. VIN/11 at A2, 1 M&ohm; to VIN, 100 k&ohm; to ground. |
| `ENABLE_EEPROM_STORAGE` | disabled | Activates the buttons to store compensation and drive speed. |

<br/>

# Pictures
Connection schematic of the L298 board for the examples. If motor drives in opposite direction, you must flip the motor to L298 connections.
![L298 connections](https://github.com/ArminJo/PWMMotorControl/blob/master/extras/L298_Connections_Steckplatine.png)

2 wheel car from LAVFIN with battery case for two 18650 Li-ion batteries, and IR receiver, with power wires in original length.<br/>
![2 wheel car](https://github.com/ArminJo/PWMMotorControl/blob/master/pictures/L298Car_TopView_small.jpg)

Connections on the Arduino and on the L298 board.<br/>
![Sensor shield connections](https://github.com/ArminJo/PWMMotorControl/blob/master/pictures/L298CarConnections_small.jpg)
![L298 connections](https://github.com/ArminJo/PWMMotorControl/blob/master/pictures/L298Connections_small.jpg)

2 wheel car with encoders, slot-type photo interrupter, 2 Li-ion batteries, Adafruit Motor Shield V2, HC-05 Bluetooth module, and servo mounted head down.
![2 wheel car](https://github.com/ArminJo/Arduino-RobotCar/blob/master/pictures/2WheelDriveCar.jpg)
4 wheel car with servo mounted head up.
![4 wheel car](https://github.com/ArminJo/Arduino-RobotCar/blob/master/pictures/4WheelDriveCar.jpg)
Encoder slot-type photo interrupter sensor
![Encoder slot-type photo interrupter sensor](https://github.com/ArminJo/Arduino-RobotCar/blob/master/pictures/ForkSensor.jpg)
Servo mounted head down
![Servo mounting](https://github.com/ArminJo/Arduino-RobotCar/blob/master/pictures/ServoAtTopBack.jpg)
VIN sensing
![VIN sensing](https://github.com/ArminJo/Arduino-RobotCar/blob/master/pictures/SensingVIn.jpg)

<br/>

# Revision History
### Version 2.1.0
- Improved examples, especially follower examples.
- Added convertMillimeterToMillis() etc.
- Added Variable computedMillisOfMotorForDistance.
- Added MillimeterPer256Degree and function setMillimeterPer256Degree() instead of using always constants.

### Version 2.0.0
- Renamed instance from RobotCarPWMMotorControl to RobotCar.
- MecanumWheelCar support.
- IMUCarData improved.
- Added Voltage handling functions like getVoltageAdjustedSpeedPWM() etc.

### Version 1.9.0 - a 2.0.0 pre release
- Removed all *Compensated functions, compensation now is always active.
- Removed StopSpeed from EepromMotorinfoStruct.
- Removed StartSpeed.
- Renamed *.cpp to *.hpp.
- Added and renamed functions.
- IMU / MPU6050 support.
- Support of off the shelf smart cars.
- Added and renamed functions.
- Converted to voltage based formulas.
- Easy configuration of different car features.

### Version 1.0.0
- Initial Arduino library version.

