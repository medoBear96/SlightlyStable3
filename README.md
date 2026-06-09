# SlightlyStable3

**SlightlyStable3** is an experimental quadcopter flight-controller project developed as part of the **Integrated and Mobile Systems** course in the Master's degree programme in Electronic Engineering at the University of Trieste.

The project was supervised by **Prof. S. Carrato** and developed using components and laboratory tools provided by the University of Trieste.

The code is structured and complete enough to run, but the aircraft is not meant to be considered flight-ready without proper tuning, testing and safety validation.

---

## Project goal

The goal of the project was to build a complete embedded control system for a quadcopter using a low-level microcontroller platform instead of relying on a ready-made commercial flight controller.

The firmware handles:

- IMU initialization and raw sensor acquisition
- accelerometer, gyroscope and magnetometer calibration
- pitch, roll and yaw estimation
- complementary filtering
- RF receiver input reading and calibration
- PID-based attitude correction
- PWM output generation for four ESCs
- LED and buzzer feedback for setup and calibration states

This was mainly an educational and experimental project, focused on understanding what happens inside a basic flight controller rather than producing a polished consumer drone.

---

## Hardware

Main components:

- **FRDM KL25Z** microcontroller board by NXP
- **MPU9150** 9-axis IMU by InvenSense
- 4 brushless motors
- 4 ESCs
- RF receiver and remote controller
- custom-developed shield with LEDs, buzzer, switches and buttons
- quadcopter frame
- power generator / power supply system

---

## Firmware overview

The project is written in **C++** and based on the **Mbed** environment.

Main firmware file:

```text
main.cpp
```

Relevant modules include:

```text
MPU9150.h
Calibration.h
Lights.h
FIFO_register.h
```

The measured cycle time is approximately **12 ms**, which means that the loop could theoretically be raised up to around **80 Hz**. The code notes that most of the delay comes from magnetometer reading in FUSE mode. Reading the magnetometer directly through the auxiliary I2C interface could reduce the cycle time and allow higher control-loop frequencies.

---

## Control logic

The firmware estimates attitude using:

- accelerometer vector for low-frequency pitch and roll reference
- gyroscope integration for high-frequency angular motion
- magnetometer heading for yaw estimation
- complementary filters for angle stabilization
- a moving-average filter for yaw-related correction

The PID controller calculates correction terms for:

- yaw
- pitch
- roll

Those corrections are then mixed into the four ESC PWM outputs.

Motor layout used by the firmware:

```text
      0   1
       \ /
        X
       / \
      2   3

0 and 3: clockwise
1 and 2: counter-clockwise
```

Axis convention:

```text
nose down       -> positive pitch
right wing down -> negative roll
```

---

## Setup and calibration switches

The custom shield uses switches to control setup behavior.

```text
SW1 -> serial output on/off
SW2 -> accelerometer and gyroscope calibration
SW3 -> magnetometer manual calibration
SW4 -> radio calibration
SW5 -> main loop activation / shutdown gate
SW6 -> sound on/off
```

The firmware uses LED and buzzer signals to make setup and calibration states easier to recognize during testing.

---

## Pin mapping

### MPU9150 to FRDM KL25Z

```text
VDD -> 3.3 V
SDA -> PTE0
SCL -> PTE1
GND -> GND
```

### RF receiver to FRDM KL25Z

```text
CH1 -> PTD3
CH2 -> PTD2
CH3 -> PTD0
CH4 -> PTD5
```

### ESC PWM outputs

```text
ESC1 -> PTB0
ESC2 -> PTB1
ESC3 -> PTB2
ESC4 -> PTB3
```

### Switches

```text
SW1 -> PTD4
SW2 -> PTA12
SW3 -> PTA4
SW4 -> PTA5
SW5 -> PTC8
SW6 -> PTC9
```

### LEDs and buzzer

```text
Green LED -> PTE20
Blue LED  -> PTE21
Red LED   -> PTE22
Buzzer    -> PTE23
```

---

## Current status

The firmware is complete as an educational implementation, but the drone should not be considered ready for real flight.

The main missing work is tuning and validation:

- PID tuning
- yaw correction refinement
- complementary-filter parameter tuning
- magnetometer reading-time optimization
- safer motor arming logic
- empirical testing under controlled conditions

In short: the code exists, the math exists, the hardware exists. Stability is still only slightly implied.

---

## Safety note

This is not a plug-and-play flight controller.

Running untuned motor-control firmware on a real quadcopter can be dangerous. Any test should be performed with propellers removed first, then with proper restraints, and only later in a controlled open area.

---

## License

The original source comment marks the project as **freeware**.
