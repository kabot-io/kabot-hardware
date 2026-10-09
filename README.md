# Kabot Hardware

![Kabot robot](img/header-image.png)

Kabot is an open-source, compact educational mobile robot. This repository contains its mechanical, electronic, and hardware production files.

## Project status

The hardware prototype is complete.

## FOSS toolchain

The hardware was designed entirely with free and open-source software (FOSS):

- [FreeCAD 1.1.1](https://www.freecad.org/) for the mechanical design
- [KiCad 10.0.4](https://www.kicad.org/) for the electronic design

Using these versions is recommended for the best compatibility with the source files.

## BOM

The mechanical parts are designed using [FreeCAD](https://www.freecad.org/) and 3D printed with some little exceptions, which were not viable to print due to the low cost of China-sourced parts, that is (amount for 10 robots):

- [Wheels](https://www.aliexpress.com/item/1005006221405033.html) - 20pcs
- [Torx self-tapping screws](https://www.aliexpress.com/item/1005011545930370.html) - 100pcs
- [N20 Gearmotors with 7CPR encoders](https://www.aliexpress.com/item/1005007102441781.html) - 20pcs
- [ToF distance sensor](https://www.aliexpress.com/item/1005008119148487.html) - 10pcs
- [Motor to PCB cables](https://www.aliexpress.com/item/1005007277030322.html) - 20pcs

## Electronics

The electronics has been designed using [KiCad](https://www.kicad.org/) and consist of few distinct sections (however, on a single PCB).

### Processing section  <a name="processing-section"></a>
Based on the [ESP32-S3-N16R8 MCU on Wroom-1 module](https://documentation.espressif.com/esp32-s3-wroom-1_wroom-1u_datasheet_en.pdf), which is responsible for communication with the PC, fetching data from the sensors, and executing control messages - driving the motors.

![Processing section schematic](processing-section.png)

This MCU has a [built-in USB JTAG](https://docs.espressif.com/projects/esp-idf/en/stable/esp32s3/api-guides/jtag-debugging/index.html) interface, which allows for flashing and debugging the robot firmware without the need of *any* external tools, besides USB-C cable. 

![USB-C schematic](usb-c.png)

There is also onboard USB HUB ([CH334F](https://cdn-learn.adafruit.com/assets/assets/000/131/435/original/CH334DS1.PDF?1721660148)) and USB-to-serial adapter ([CH343P](https://cdn-learn.adafruit.com/assets/assets/000/134/549/original/CH343DS1.PDF?1737477957)), which allows for connecting to the robot over the raw UART interface, which exposes [first-stage bootloader](https://docs.espressif.com/projects/esp-idf/en/stable/esp32s3/api-guides/bootloader.html) logs, and a connector which allows for splitting the USB-C lanes, and injecting offboard processing units, such as embedded Linux board (instead of the beefy PC, to make the robot self-contained if needed) 

![USB peripherals schematic](usb-peripherals.png)

### Power section  <a name="power-section"></a>
The USB-C connector that is used to program the robot, is also used to charge the robot built-in battery, which is a single Lithium-Ion 18650 sized cell. The same chip is also responsible for stepping the battery voltage up to 5V, which is then fed to the motor drivers to have somehat stable motor power supply, and stabilized to 3.3V for the MCU. The chip is the thing used in powerbanks - [INJOINIC IP5306](https://www.lcsc.com/datasheet/C181692.pdf).

![Powerbank schematic](powerbank.png)

![LDO schematic](ldo.png)

Motors are driven by two [DRV8837](https://www.ti.com/lit/ds/symlink/drv8838.pdf) motor drivers (up to 1.8A per motor). Each motor driver is supplied via [INA219](https://www.ti.com/lit/ds/symlink/ina219.pdf) I2C Current/Power monitor. The motors are chinese [N20 sized 298:1 micro gearmotors, equipped with magnetic incremental encoders](https://pl.aliexpress.com/item/1005007102441781.html). The power supply branch for the rest of the system also has it's own IN219 sensor. There is also voltage divider connected directly do the battery terminals (or rather semi-directly - via a switch that gets opened when the robot is not powered up, to avoid entirely draining the battery). 

![Current sensor schematic](current-sensor.png)

![Motor driver schematic](motor-driver.png)
### Sensors section  <a name="sensors-section"></a>

Besides the current sensors and encoders, robot has a bunch of on-board sensors:
- 6 Degrees of Freedon Inertial Measurement Unit: [TDK ICM-42670-L](https://datasheet.lcsc.com/datasheet/pdf/874d19cb3cf7cf4a72e58391bfc479df.pdf)
- 3 Degrees of Freedom Magnetometer: [MEMSIC MMC5603NJ](https://www.lcsc.com/datasheet/C404328.pdf)
- dual visible light sensors (left and right): [LITEON LTR-303-ALS](https://www.lcsc.com/datasheet/C364577.pdf)
- distance sensor - [ST VL53L0X](https://www.st.com/resource/en/datasheet/vl53l0x.pdf) on the GY-530 footprint module - this allows for supplying different distance sensors, sucha as 8x8 grid [ST VL53L5X](https://www.st.com/resource/en/datasheet/vl53l5cx.pdf) ToF sensor

![Sensors schematic](sensors.png)
![I2C hub schematic](i2c-hub.png)

These allow for implementing a wide variety of algorithms - from simple [BEAM](https://en.wikipedia.org/wiki/BEAM_robotics)-like photovores, trough IMU sensor fusion up to localization and mapping.

The board also supplies a bunch of addresable RGB LEDs.

![LEDs schematic](leds.png)


## Repository structure

| Directory | Contents |
| --- | --- |
| [`freecad/`](freecad/) | FreeCAD mechanical source files for the chassis, battery holder, motor mounts, wheels, and related parts. |
| [`kicad/`](kicad/) | KiCad schematics, PCB layout, symbols, footprints, and supporting EDA files. |
| [`production/`](production/) | Files used to manufacture the PCB and print the chassis, including Gerber and drill files, a bill of materials (BOM), and pick-and-place data. |

## License

Copyright 2026 Krzysztof Pochwała.

The hardware design files are available under the [Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International license](LICENSE.md) (CC BY-NC-SA 4.0). You may share and adapt the files with attribution, for non-commercial purposes, provided adaptations are distributed under the same license.

## More information

Visit [kabot.io](https://kabot.io/).
