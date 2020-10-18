# [PWMMotorControl](https://github.com/ArminJo/PWMMotorControl)
Available as Arduino library "PWMMotorControl"

### [Version 1.1.1](https://github.com/ArminJo/PWMMotorControl/releases)

[![License: GPL v3](https://img.shields.io/badge/License-GPLv3-blue.svg)](https://www.gnu.org/licenses/gpl-3.0)
[![Installation instructions](https://www.ardu-badge.com/badge/PWMMotorControl.svg?)](https://www.ardu-badge.com/PWMMotorControl)
[![Commits since latest](https://img.shields.io/github/commits-since/ArminJo/PWMMotorControl/latest)](https://github.com/ArminJo/PWMMotorControl/commits/master)
[![Build Status](https://github.com/ArminJo/PWMMotorControl/workflows/LibraryBuild/badge.svg)](https://github.com/ArminJo/PWMMotorControl/actions)
![Hit Counter](https://visitor-badge.laobi.icu/badge?page_id=ArminJo_PWMMotorControl)

- The PWMDcMotor.cpp controls **brushed DC motors** by PWM using standard full bridge IC's like **[L298](https://www.instructables.com/L298-DC-Motor-Driver-DemosTutorial/)**, [**SparkFun Motor Driver - Dual TB6612FNG**](https://www.sparkfun.com/products/14451), or **[Adafruit_MotorShield](https://www.adafruit.com/product/1438)** (using PCA9685 -> 2 x TB6612).
- The EncoderMotor.cpp.cpp controls a DC motor with attached encoder disc and slot-type photo interrupters to enable **driving a specified distance**.
- The CarMotorControl.cpp controls **2 motors simultaneously** like it is required for most **Robot Cars**. 
- To **compensate for different motor characteristics**, each motor can have a **positive** compensation value, which is **subtracted** from the requested speed if you use the `setSpeedCompensation()` functions. For car control, only compensation of one motor is required.

The motor is mainly controlled by 2 dimensions:
1. Motor driver control / direction. Can be FORWARD, BACKWARD, BRAKE (motor connections are shortened) or RELEASE (motor connections are high impedance).
2. Speed / PWM which is ignored for BRAKE or RELEASE. Some functions allow a signed speed parameter, which incudes the direction as sign (positive -> FORWARD).

Basic commands are:
- `init(uint8_t aForwardPin, uint8_t aBackwardPin, uint8_t aPWMPin)`.
- `setSpeed(uint8_t Unsigned_Speed, uint8_t Direction)` or `setSpeed(int Signed_Speed)`.
- `setSpeedCompensation(uint8_t aSpeedCompensation)` and `setSpeedCompensated(uint8_t Unsigned_Speed, uint8_t Direction)` or `setSpeedCompensated(int Signed_Speed)`.
- `stop()` or `setSpeed(0)`.

To go a specified distance (in 5mm/one encoder tick steps), use:
- `setDefaultsForFixedDistanceDriving()` to set minimal speed and maximal speed. Minimal speed is the PWM value where the motors start to move. It depends of the motor supply voltage.<br/>
Maximal speed is the PWM value to use for driving a fixed distance. For encoder equipped motors the software generates a ramp up from minimal to maximal at the start of the movement and a ramp down to stop.
- `calibrate()` to automatically set minimal speed for encoder motors.
- `initGoDistanceCount(uint8_t Unsigned_DistanceCount,uint8_tDirection)` or `setSpeed(intSigned_DistanceCount)` - for non encoder motors a formula, using distance and the difference between minimal speed and maximal speed, is used to convert counts into motor driving time.
- `updateMotor()` - call this in your loop if you use the start* functions.

2 wheel car with encoders, slot-type photo interrupter, 2 LiPo batteries, Adafruit Motor Shield V2, HC-05 Bluetooth module, and servo mounted head down.
![2 wheel car](https://github.com/ArminJo/PWMMotorControl/blob/master/pictures/L298Car_TopView_small.jpg)

# Compile options / macros for this library
To customize the library to different requirements, there are some compile options / macros available.<br/>
Modify it by commenting them out or in, or change the values if applicable. Or define the macro with the -D compiler option for gobal compile (the latter is not possible with the Arduino IDE, so consider to use [Sloeber](https://eclipse.baeyens.it).<br/>
Some options which are enabed by default can be disabled by defining a *inhibit* macro like `USE_STANDARD_LIBRARY_FOR_ADAFRUIT_MOTOR_SHIELD`.

| Macro | Default | File | Description |
|-|-|-|-|
| `USE_ENCODER_MOTOR_CONTROL` | disabled | PWMDCMotor.h | Use slot-type photo interrupter and an attached encoder disc to enable motor distance and speed sensing for closed loop control. |
| `USE_ADAFRUIT_MOTOR_SHIELD` | disabled | PWMDcMotor.h | Use Adafruit Motor Shield v2 connected by I2C instead of simple TB6612 or L298 breakout board.<br/>This disables tone output by using motor as loudspeaker, but requires only 2 I2C/TWI pins in contrast to the 6 pins used for the full bridge.<br/>For full bridge, analogWrite the millis() timer0 is used since we use pin 5 & 6. |
| `USE_OWN_LIBRARY_FOR_`<br/>`ADAFRUIT_MOTOR_SHIELD` | enabled | PWMDcMotor.h | Saves around 694 bytes program memory.<br/>Disable macro=`USE_STANDARD_LIBRARY_`<br/>`FOR_ADAFRUIT_MOTOR_SHIELD` |
| `SUPPORT_RAMP_UP` | enabled | PWMDcMotor.h | Saves around 300 bytes program memory.<br/>Disable macro=`DO_NOT_SUPPORT_RAMP_UP` |

# Default car geometry dependent values used in this library
These values are for a standard 2 WD car as can be seen on the pictures below.
| Macro | Default | File | Description |
|-|-|-|-|
| `DEFAULT_COUNTS_PER_FULL_ROTATION` | 40 | PWMDCMotor.h | This value is compatible with 20 slot encoder discs, giving 20 on and 20 off counts per full rotation. |
| `DEFAULT_MILLIMETER_PER_COUNT` | 5 | PWMDCMotor.h | At a circumference of around 20 cm (21.5 cm actual) this gives 5 mm per count. |
| `FACTOR_CENTIMETER_TO_`<br/>`COUNT_INTEGER_DEFAULT` | 2 | CarMotorControl.h | Exact value is 1.86, but integer saves program space and time. |
| `FACTOR_DEGREE_TO_COUNT_DEFAULT` | 0.4277777 for 2 wheel drive cars, 0.8 for 4 WD cars | CarMotorControl.h | Reflects the geometry of the standard 2 WD car sets. The 4 WD car value is estimated for slip on smooth surfaces. |

# Other default values for this library
These values are used by functions and the first 2 can be overwritten by set* functions.
| Macro | Default | File | Description |
|-|-|-|-|
| `DEFAULT_START_SPEED` | 45/150 for 7.4/6.0 volt supply | PWMDCMotor.h | START_SPEED is the speed PWM value at which car starts to move. |
| `DEFAULT_DRIVE_SPEED` | 80/255(max speed) for 7.4/6.0 volt supply | PWMDCMotor.h | The speed PWM value for going fixed distance. |
| `DEFAULT_DISTANCE_TO_TIME_FACTOR` | 135/300 for 7.4/6.0 volt supply | PWMDCMotor.h | The factor used to convert distance in 5mm steps to motor on time in milliseconds using the formula:<br/>`computedMillisOf`<br/>`MotorStopForDistance = 150 + (10 * ((aRequestedDistanceCount * DistanceToTimeFactor) / DriveSpeed))` |
| `RAMP_UP_UPDATE_INTERVAL_MILLIS` | 16 | PWMDCMotor.h | The smaller the value the steeper the ramp. |
| `RAMP_UP_UPDATE_INTERVAL_STEPS` | 16 | PWMDCMotor.h | Results in a ramp up time of 16 steps * 16 millis = 256 milliseconds. |

### Modifying library properties with Arduino IDE
First use *Sketch/Show Sketch Folder (Ctrl+K)*.<br/>
If you did not yet stored the example as your own sketch, then you are instantly in the right library folder.<br/>
Otherwise you have to navigate to the parallel `libraries` folder and select the library you want to access.<br/>
In both cases the library files itself are located in the `src` directory.<br/>

### Modifying library properties with Sloeber IDE
If you are using Sloeber as your IDE, you can easily define global symbols with *Properties/Arduino/CompileOptions*.<br/>
![Sloeber settings](https://github.com/ArminJo/ServoEasing/blob/master/pictures/SloeberDefineSymbols.png)

# [Examples](tree/master/examples)

## Start
**To check the default values of StartSpeed and DriveSpeed**. One motor starts with StartSpeed for one second, then runs 1 second with DriveSpeed.
After stooping the motor, it tries to run for one full rotation (resulting in a 90 degree turn for a 2WD car). Then the other motor runs the same cycle.
For the next loop, the direction is switched to backwards.

## Square
4 times drive 40 cm, then 90 degree left turn. After the square, the car is turned by 180 degree and the direction is switched to backwards. Then the square starts again.

## RobotCarBasic
Template for your RobotCar control. Currently implemented is: drive until distance too low, then stop, and turn random amount.

## RobotCarFollowerSimple
The car tries to hold a distance between 20 and 30 cm to an obstacle. The measured distance is converted to a pitch as an acoustic feedback.

## RobotCarFollower
The car tries to hold a distance between 20 and 30 cm to an target. If the target vanishes, the distance sensor scans for the vanished or a new target.

## [RobotCarBlueDisplay](https://github.com/ArminJo/Arduino-RobotCar)
Enables autonomous driving of a 2 or 4 wheel car with an Arduino and a Adafruit Motor Shield V2.<br/>
To avoid obstacles a HC-SR04 Ultrasonic sensor mounted on a SG90 Servo continuously scans the environment.
Manual control is implemented by a GUI using a Bluetooth HC-05 Module and the BlueDisplay library.

Just overwrite the function doUserCollisionDetection() to test your own skill.<br/>
You may also overwrite the function fillAndShowForwardDistancesInfo(), if you use your own scanning method.

# Compile options for RobotCar example
To customize the RobotCar example to cover different extensions, there are some compile options available.
| Option | Default | File | Description |
|-|-|-|-|
| `USE_LAYOUT_FOR_NANO` | disabled | RobotCar.h | Use different pinout for Nano board. It has A6 and A7 available as pins. |
| `CAR_HAS_4_WHEELS` | disabled | RobotCar.h | Use modified formula for turning the car. |
| `USE_US_SENSOR_1_PIN_MODE` | disabled | RobotCar.h | Use modified HC-SR04 modules or HY-SRF05 ones.</br>Modify HC-SR04 by connecting 10kOhm between echo and trigger and then use only trigger pin. |
| `CAR_HAS_IR_DISTANCE_SENSOR` | disabled | RobotCar.h | Use Sharp GP2Y0A21YK / 1080 IR distance sensor. |
| `CAR_HAS_TOF_DISTANCE_SENSOR` | disabled | RobotCar.h | Use VL53L1X TimeOfFlight distance sensor. |
| `DISTANCE_SERVO_IS_MOUNTED_HEAD_DOWN` | disabled | Distance.h | The distance servo is mounted head down to detect even small obstacles. |
| `CAR_HAS_CAMERA` | disabled | RobotCar.h | Enables the `Camera` button for the `PIN_CAMERA_SUPPLY_CONTROL` pin. |
| `CAR_HAS_LASER` | disabled | RobotCar.h | Enables the `Laser` button for the `PIN_LASER_OUT` / `LED_BUILTIN` pin. |
| `CAR_HAS_PAN_SERVO` | disabled | RobotCar.h | Enables the pan slider for the `PanServo` at the `PIN_PAN_SERVO` pin. |
| `CAR_HAS_TILT_SERVO` | disabled | RobotCar.h | Enables the tilt slider for the `TiltServo` at the `PIN_TILT_SERVO` pin.. |
| `MONITOR_LIPO_VOLTAGE` | disabled | RobotCar.h | Shows VIN voltage and monitors it for undervoltage. Requires 2 additional resistors at pin A2. |
| `VIN_VOLTAGE_CORRECTION` | undefined | RobotCar.h | Voltage to be subtracted from VIN voltage. E.g. if there is a series diode between LIPO and VIN set it to 0.8. |

#### If you find this library useful, please give it a star.

# Pictures
Connection schematic of the L298 board for the examples. If motor drives in opposite direction, you must flip the motor to L298 connections.
![L298 connections](https://github.com/ArminJo/PWMMotorControl/blob/master/extras/L298_Connections_Steckplatine.png)
Connections on the Arduino and on the L298 board.
![Uno connections](https://github.com/ArminJo/PWMMotorControl/blob/master/pictures/UnoConnections_small.jpg)
![L298 connections](https://github.com/ArminJo/PWMMotorControl/blob/master/pictures/L298Connections_small.jpg)

2 wheel car with encoders, slot-type photo interrupter, 2 LiPo batteries, Adafruit Motor Shield V2, HC-05 Bluetooth module, and servo mounted head down.
![2 wheel car](https://github.com/ArminJo/Arduino-RobotCar/blob/master/pictures/2WheelDriveCar.jpg)
4 wheel car with servo mounted head up.
![4 wheel car](https://github.com/ArminJo/Arduino-RobotCar/blob/master/pictures/4WheelDriveCar.jpg)
Encoder slot-type photo interrupter sensor
![Encoder slot-type photo interrupter sensor](https://github.com/ArminJo/Arduino-RobotCar/blob/master/pictures/ForkSensor.jpg)
Servo mounted head down
![Servo mounting](https://github.com/ArminJo/Arduino-RobotCar/blob/master/pictures/ServoAtTopBack.jpg)
VIN sensing
![VIN sensing](https://github.com/ArminJo/Arduino-RobotCar/blob/master/pictures/SensingVIn.jpg)

# SCREENSHOTS
Start page
![MStart page](https://github.com/ArminJo/Arduino-RobotCar/blob/master/pictures/HomePage.png)
Test page
![Manual control page](https://github.com/ArminJo/Arduino-RobotCar/blob/master/pictures/TestPage.png)
Automatic control page with detected wall at right
![Automatic control page](https://github.com/ArminJo/Arduino-RobotCar/blob/master/pictures/AutoDrivePage.png)
- Green bars are distances above 1 meter or above double distance of one ride per scan whichever is less.
- Red bars are distanced below the distance of one ride per scan -> collision during next "scan and ride" cycle if obstacle is ahead.
- Orange bars are the values between the 2 thresholds.
- The tiny white bars are the distances computed by the doWallDetection() function. They overlay the green (assumed timeout) values.
- The tiny black bar is the rotation chosen by doCollisionDetection() function.

# Revision History
### Version 1.1.1
- Improved examples

### Version 1.1.0
- Added and renamed functions.
- Initial Arduino library version.

