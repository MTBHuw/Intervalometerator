# Intervalometerator (aka "intvlm8r")

The Intervalometerator (aka "intvlm8r") is a flexible, low-cost time-lapse camera controller for DSLRs.

It came into being after we were unable to find a suitable time-lapse controller for a planned time-lapse shoot for which we wanted the image quality of a DSLR. The first unit deployed is recording the construction of our friends' house in rural New Zealand, off-grid and with no network connectivity.

At its heart are two micro-controller boards, each serving a specific purpose. An <a href="https://www.sparkfun.com/products/11114https://www.sparkfun.com/products/11114" target="_blank">Arduino Pro Mini</a> is the master, with a battery-backed external real-time clock signalling it to wake the camera and fire the shutter. A <a href="https://www.raspberrypi.org/products/raspberry-pi-zero-w/" target="_blank">Raspberry Pi Zero W</a> provides a web-based "front-end" where all configuration and management takes place. An I2C communication bus between the micros handles the higher-level intercommunication, with 3 digital IO lines between them providing 'wake' and 'shutdown' signalling. The digital IO reduces the risk of drive corruption as the power-hungry Pi's power is applied and removed under the Arduino's control.

The Arduino's code ('sketch') is largely bespoke, whilst the Pi is totally reliant on and derived from both <a href="http://gphoto.org/" target="_blank">the gPhoto2 project</a>, and <a href="https://github.com/jim-easterbrook/python-gphoto2" target="_blank">python-gphoto2</a>, Jim Easterbrook's Python interface to same. 


## Features

### Use your choice of DSLR
With gPhoto and python-gphoto2 providing all the camera interfacing, the intent is that any camera compatible with these projects should be able to be used.

Note that in its initial published version, the intvlm8r has been developed with Canon 6D and 60D cameras. The unit deployed in NZ contained a 60D - read why on the [Design Decisions](DESIGNDECISIONS.md) page.

gPhoto (running on the Pi) is only used for the camera management, with the Arduino handling the core role of waking the camera and taking the shot using the camera's manual shutter input.

### Flexible interval settings

From the web interface you select the days of the week to shoot, within what span of hours, and how many shots per hour. A typical building construction might shoot Monday through Friday, from 6am to 6pm, and take 4 shots an hour.

### Low-Cost
Using commonly-available components, an intvlm8r controller with charger, battery, solar panel, waterproof <a href="https://www.pelican.com/us/en/" target="_blank">Pelican case</a> & mounts should see you with plenty of change from $AUD600 ($US440), a fraction of the cost of commercially-available equivalents. Add your DSLR and a lens and you're in business! See the [Shopping list](SHOPPINGLIST.md) for the specifics.

### Remote Management
Assuming the installation has web connectivity, the setup can be monitored and tweaked remotely. From your web-browser you can confirm that the camera is firing, backup the photos to the Pi, take a test image and adjust the available camera settings.

In an isolated setup, come within range and connect to the Pi Zero W's inbuilt WiFi Access Point for on-site management, without needing to crack the weatherproof seal on the Pelican case.

### Low-power
The invtlm8r is designed for low-power operation. At rest the entire project consumes less than 1mA, enabling it to be powered by a common <a href="https://simple.wikipedia.org/wiki/Sealed_lead_acid_battery" target="_blank">sealed lead acid ("SLA")</a> battery and solar panel. Current consumption rises as the camera is woken to take a photo, and peaks when the Raspberry Pi is powered-on for management and file transfers.

## Easy assembly
Using pre-built micro-controller, clock and power supply boards reduces the complexity of assembly and the risk of errors. It also means you don't need a perfect set of eyes, nor the precision SMD soldering skills of a microsurgeon. The only discrete components on the veroboard shown here are resistors, a LED and the opto-isolators that electrically isolate the Arduino from the camera's shutter input - and none of that's surface-mount.

### Battery-backed clock
The intvlm8r's master clock is a SparkFun "<a href="https://www.sparkfun.com/products/10160" target="_blank">DeadOn RTC Breakout</a>". This high-accuracy real-time clock board carries a coin-cell battery to ensure it maintains the time even if the power supply fails.

### Common 12V power supply
The intvlm8r is powered from a standard 12V SLA battery and charger, providing a wide range of powering and charging options. Separate high-efficiency <a href="https://www.pololu.com/category/136/voltage-regulatorshttps://www.pololu.com/category/136/voltage-regulators" target="_blank">Pololu</a> switched-mode power regulators run each operating unit on its own supply. Three separate on-board regulators provide:
* 3.3V for the Arduino & DeadOn clock
* 5.0V for the Raspberry Pi. An IO pin from the Arduino holds this supply's /SHUTDOWN input at 0V when the Pi is not running, significantly reducing quiescent power consumption.
* 7.5V for the Canon camera. The lower this voltage, the lower the battery can discharge without affecting the operation of the intvlm8r. The tested cameras run fine at this lower voltage, although report a battery at around 50% charge.

In theory, the 12V battery can discharge to ~8V before the camera will not be able take a photo, and much lower still before the Arduino and DeadOn clock lose power.

### Auto-recovery after power-loss
Should an extended period of bad weather result in the battery discharging to a point that it can no longer sustain operation, the intvlm8r will retain its settings in EEPROM (in the Arduino). Once the battery has sufficiently recharged, any pending alarms will be serviced (by taking a photo) and normal operation resumed.

Should the backup battery in the DeadOn RTC fail while the intvlm8r is not powered, the Arduino will lose its real-time clock. This will be detected on power-up and the Arduino default to an emergency program, shooting 4 images every hour of the day until the <a href="https://en.wikipedia.org/wiki/Real-time_clock" target="_blank">RTC</a> can be reset through manual intervention.


# Where to next?

Check out these documents for more information:
* [Design Decisions](DESIGNDECISIONS.md). This provides a detailed explanation of some of the design decisions and compromises.
* [Shopping list](SHOPPINGLIST.md). All the items you need to make this.
* [Construction](CONSTRUCTION.md). Some images of the assembly of the Pelican case.
* [Board assembly](BOARDASSEMBLY.md). Putting the PCB together.

The Arduino's Sketch is in its own folder, and the Raspberry Pi's Python script and HTML pages can be found in their respective folders under RaspberryPi.


# Input welcome!

Please contribute to the project! It's coded in the Arduino's flavour of C++, with the Pi in Python, HTML & CSS, and there are improvements to be made across the board. Check out [CONTRIBUTING.md](CONTRIBUTING.md) for more info.

\
\
\- Greig and Rocky.



