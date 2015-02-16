Summary (version 1.0-1)
=========================

This `PKGBUILD` and related files form a simple package for enabling the [PCF8523](http://www.nxp.com/products/interface_and_connectivity/real_time_clocks/i2c_real_time_clocks_rtc/series/PCF8523.html) real-time clock on RPi900 boards. A service is provided for automatically synchronising the Raspberry Pi system clock to the real-time clock during system startup.

Operation
=========

During package installation your `/boot/config.txt` file will be modified to enable the `pcf8523-rtc` [device-tree overlay](https://github.com/raspberrypi/firmware/tree/master/boot/overlays). With this modification, the PCF8523 chip will be automatically loaded as `/dev/rtc0` when the Raspberry Pi boots.

The package also installs a [*systemd service*](http://crashmag.net/useful-systemd-commands) to synchronise the system time from `/dev/rtc0` once it's loaded. (This typically occurs around the 12-second mark.) The service is enabled to run automatically, so no further action is required.

After rebooting, you can observe synchronisation of the clock by typing `systemctl status rpi900-rtc`:

    * rpi900-rtc.service - RPi900 real-time clock synchronisation
       Loaded: loaded (/usr/lib/systemd/system/rpi900-rtc.service; enabled; vendor preset: disabled)
       Active: inactive (dead) since Sun 2015-02-15 14:08:50 AEDT; 2h 29min ago
      Process: 142 ExecStart=/usr/bin/hwclock --hctosys (code=exited, status=0/SUCCESS)
     Main PID: 142 (code=exited, status=0/SUCCESS)
    
    Jan 01 10:00:13 roof systemd[1]: Starting RPi900 real-time clock synchronisation...
    Feb 15 14:08:50 roof systemd[1]: Started RPi900 real-time clock synchronisation.

(No reverse synchronisation of the hardware clock from the system clock is performed; the `ntpd` time protocol should do this automatically if the Raspberry Pi is network-connected.)

Package Contents
================

The package installs only a single file:

* `/usr/lib/systemd/system/rpi900-rtc.service`: this systemd service file performs a system clock synchronisation (`hwclock --hctosys`) once the hardware clock device becomes available.

Release History
===============

* 2015/02/15: version 1.0-1: initial release.
