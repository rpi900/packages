Summary
=======

This `PKGBUILD` and related files form a package for installing and managing the [DNT900 line discipline](https://github.com/mholling/dnt900) on RPi900. The line discipline module is built and installed for the currently installed kernel, and a service is provided for automatically attaching the line discipline to the Raspberry Pi's `ttyAMA0` serial port during system startup.

Dependencies
==================

The following dependencies should first be installed:

* the [*rpirtscts*](../rpirtscts/) utility

Operation
=========

During package installation you will receive a reminder to set the serial speed in the `/etc/rpi900.conf` configuration file. Edit this file using your preferred text editor to specify the serial rate you're using:

    sudo nano /etc/rpi900.conf

The default rate of 9600 bits/second corresponds to the default rate of the DNT900 radio.

During installation, your `/boot/cmdline.txt` is modified to remove the `console` and `kgdboc` on `ttyAMA0`, and the `serial-getty@ttyAMA0` service is also disabled. (This is required to free up the serial port for use by the DNT900 radio.) An extra setting for `init_uart_clock` is also added to `/boot/config.txt` to enable high-speed serial rates of up to 460.8 kb/s.

The package provides a [*systemd service*](http://crashmag.net/useful-systemd-commands) to control the line discipline. Control the service as follows:

    sudo systemctl start rpi900.service    # start the line discipline service
    sudo systemctl stop rpi900.service     # stop it
    sudo systemctl restart rpi900.service  # stop and restart
    sudo systemctl enable rpi900.service   # start during boot
    sudo systemctl disable rpi900.service  # don't start during boot
    sudo systemctl status rpi900.service   # show whether service is running/enabled

(The service is initially enabled and will start automatically on the next boot.)

You can monitor operation of the line discipline and see the radios it picks up by observing `dmesg`:

    dmesg | grep dnt900

Changing the Serial Rate
========================

Changing the DNT900 `SerialRate` register takes immediate effect and causes a loss of connection, so a particular sequence is required to successfully restablish communication at the new speed:

    su                                                                   # must be root
    echo 0x04 > /sys/devices/virtual/dnt900/ttyAMA0/0x00165F/SerialRate  # (timeout error 110 will be returned)
    sed -i s/SPEED=.*/SPEED=115200/ /etc/rpi900.conf                     # configure new speed as 115200
    systemctl restart rpi900.service                                     # restart the line discipline at the new speed
    cat /sys/devices/virtual/dnt900/ttyAMA0/0x00165F/SerialRate          # confirm 0x04 as the new value
    echo 0x01 > /sys/devices/virtual/dnt900/ttyAMA0/0x00165F/MemorySave  # make the new SerialRate value permanent

Updating the Kernel
===================

The DNT900 line discipline module is compiled specificically for the kernel currently installed. This is reflected in the version number of the *rpi900* package, which matches the kernel it was compiled against. Upgrading your kernel (as sometimes occurs during `sudo pacman -Syu`) will cause the module to no longer be found.

The Arch Linux kernel is upgraded frequently, so I recommend holding back kernel upgrades by adding `IgnorePkg = linux-raspberrypi linux-raspberrypi-headers` to the `[options]` section of your `/etc/pacman.conf` file.

Then, if you decide to upgrade the kernel, use the following sequence of commands:

    sudo pacman -Rdd rpi900                                     # remove rpi900 package without dependencies
    sudo pacman -S linux-raspberrypi linux-raspberrypi-headers  # upgrade kernel and headers
    cd packages/rpi900
    makepkg --clean                                             # rebuild rpi900 package for new kernel ...
    sudo pacman -U rpi900-3.18.7-4-armv6h.pkg.tar.xz            # ... install it ...
    sudo mv /etc/rpi900.conf.pacsave /etc/rpi900.conf           # ... and restore configuration file

This way, when you next reboot the new DNT900 line discipline will be installed and ready to go with the new kernel.

Package Contents
================

The package installs the following files:

* `/usr/lib/modules/3.18.7-4-ARCH/extra/dnt900.ko`: this is the kernel module for the DNT900 line discipline.
* `/usr/lib/systemd/system/rpi900.service`: this systemd service file loads the DNT900 line discipline module, enables hardware flow control on the `ttyAMA0` serial port, then attaches the line discipline. By default, the service is enabled.
* `/usr/lib/udev/rules.d/99-rpi900.rules`: this udev rules file creates a device symlink for each radio's `ttyDNT*` device. So for example, when a radio with MAC address 0x00165F joins the network with tty `/dev/ttyDNT3`, a symlink `/dev/0x00165F` will link to the new tty for easy identification.
* `/etc/rpi900.conf`: this configuratation file contains the serial speed and should be edited to match the serial speed used by the DNT900 radio.

Release History
===============

* 2014/04/22: version 0.3.4-1: initial release.
* 2014/04/28: version 0.3.4-2: move rpirtscts to separate package and add as dependency.
* 2014/05/09: version 1.0-1: extract dnt900 module to separate package.
* 2015/02/16: version 1.0-2: automatically remove conflicting ttyAMA0 usage.
* 2015/02/17: add DNT900 line discipline module itself; change versioning to match kernel version.
