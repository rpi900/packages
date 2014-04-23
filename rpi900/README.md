Summary (version 0.3.4-1)
=========================

This `PKGBUILD` and related files build a simple package for managing the [DNT900 line discipline](https://github.com/mholling/dnt900) on RPi900. The line discipline allows you to easily access and change register values for the DNT900 radio, as well as direct data to remote DNT900 radios on the network using a separate, virtual *TTY* for each radio.

Making the Package
==================

Some basic tools are required to build the package. Install them as follows:

    sudo pacman -S base-devel abs git linux-raspberrypi-headers

Next, download the package files:

    git clone https://github.com/rpi900/packages.git
    cd packages/rpi900

Finally, make and install the package:

    makepkg --clean
    sudo pacman -U rpi900-0.3.4-1-armv6h.pkg.tar.xz

Operation
=========

During package installation you will receive a reminder to set the serial speed in the `/etc/rpi900.conf` configuration file. Edit this file using your preferred text editor to specify the serial rate you're using:

    sudo nano /etc/rpi900.conf

The default rate of 9600 bits/second corresponds to the default rate of the DNT900 radio.

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

The DNT900 line discipline module is compiled specificically for the kernel currently in use. Upgrading your kernel (as sometimes occurs during `sudo pacman -Syu`) will cause the module to no longer be found. Counter this by rebuilding and reinstalling the *rpi900* package (be sure to reboot with the new kernel first).

Alternatively you can hold back kernel upgrades by adding `IgnorePkg = linux-raspberrypi linux-raspberrypi-headers` to your `/etc/pacman.conf` file.

Package Contents
================

The package installs the following files:

* `/lib/modules/3.10.*-*-ARCH/extra/dnt900.ko`: this is the module file for the DNT900 line discipline.
* `/usr/bin/rpirtscts`: this utility command enables alternate functions on the GPIO30 and GPIO31 pins, allowing them to serve as hardware flow control signals for the `ttyAMA0` serial port.
* `/usr/lib/modules-load.d/dnt900.conf`: loads the DNT900 line discipline module at boot.
* `/usr/lib/systemd/system/rpi900.service`: this systemd service file enables hardware flow control on the `ttyAMA0` serial port, then attaches the DNT900 line discipline. By default, the service is enabled.
* `/usr/lib/udev/rules.d/99-rpi900.rules`: this udev rules file creates a device symlink for each radio's `ttyDNT*` device. So for example, when a radio with MAC address 0x00165F joins the network with tty `/dev/ttyDNT3`, a symlink `/dev/0x00165F` will link to the new tty for easy identification.
* `/etc/rpi900.conf`: this configuratation file contains the serial speed and should be edited according to that used by the DNT900 radio.

Release History
===============

The version number of this package will track the current version of the [DNT900 line discipline](https://github.com/mholling/dnt900). Changes or new features (if any) will induce a new release for the same version (e.g. 0.3.4-1 to 0.3.4-2)

* 2014/04/22: version 0.3.4-1: initial release.
