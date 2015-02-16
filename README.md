RPi900 Packages
===============

This repository hosts *packages* for Arch Linux ARM when used with [Raspberry Pi](http://www.raspberrypi.org/) and the [RPi900](http://rpi900.com) long-range radio board:

* The [*rpirtscts*](rpirtscts/) package installs the [*rpirtscts*](https://github.com/mholling/rpirtscts) flow-control utility.
* The [*rpi900-rtc*](rpi900-rtc/) package adds a startup service to synchronise system time with the RPi900's built-in PCF8523 real-time clock.
* The [*dnt900*](dnt900/) package installs the [DNT900 line discipline](https://github.com/mholling/dnt900) as a kernel module.
* The [*rpi900*](rpi900/) package installs a systemd service for automatically attaching the DNT900 line discipline to the Raspberry Pi's `ttyAMA0` serial port at startup.
* The [*rpi900-ppp*](rpi900-ppp/) package provides a set of udev rules and systemd services for starting a [PPP](http://en.wikipedia.org/wiki/Point-to-point_protocol) connection over the DNT900 radio link. Collectively, these files automatically configure an RPi900 system to provide or obtain an internet connection using the radio.
* The [*rpi900-pacman*](rpi900-pacman/) package provides a wrapper for the [*pacman*](https://wiki.archlinux.org/index.php/pacman) package manager, configured specifically for RPi900 remote stations connected via PPP to an RPi900 base station.

The packages are simple to build and install with the [*pacman*](https://wiki.archlinux.org/index.php/pacman) package manager. Minimal editing of configuration files is all that is needed to achieve a working system.

Refer to the [*rpirtscts*](rpirtscts/), [*rpi900-rtc*](rpi900-rtc/), [*dnt900*](dnt900/), [*rpi900*](rpi900/) and [*rpi900-ppp*](rpi900-ppp/) directories for detailed instructions on use of the respective packages.

Building Packages
=================

Building and installing packages is very simple. First, be sure you have an up-to-date Arch Linux system, then install some tools:

    sudo pacman -Syu
    sudo pacman -S --needed base-devel git linux-raspberrypi-headers

Next, use `git` to clone the RPi900 package files:

    git clone https://github.com/rpi900/packages.git

For each package you wish to install, move into the directory, build the package with `makepkg` and install it with `pacman`. For example:

    cd packages/dnt900
    makepkg --clean
    sudo pacman -U dnt900-0.3.5-1-armv6h.pkg.tar.xz

(Some packages will require a reboot before they come into effect.)

Quick-Start Guide
=================

For RPi900 users wishing to get started quickly with their radios, the following commands should be sufficient to have the radio accessible on the command-line:

    sudo pacman -Syu
    sudo pacman -S --needed base-devel git linux-raspberrypi-headers
    git clone https://github.com/rpi900/packages.git
    cd packages/rpirtscts
    makepkg --clean
    sudo pacman -U rpirtscts-1.0-1-armv6h.pkg.tar.xz
    cd ../dnt900
    makepkg --clean
    sudo pacman -U dnt900-0.3.5-1-armv6h.pkg.tar.xz
    cd ../rpi900
    makepkg --clean
    sudo pacman -U rpi900-1.0-2-armv6h.pkg.tar.xz
    sudo nano /etc/rpi900.conf  # if necessary
    sudo reboot
    dmesg | grep dnt900
    cat /sys/devices/virtual/dnt900/ttyAMA0/0x....../MacAddress

Be sure to read to documentation more closely as you familiarise yourself with RPi900 and the radios!

Releases
========

I intend that the master branch always have the latest stable packages, with any new work in a development branch. With any new features I will bump the package version or release number of the package (`pkgver` and `pkgrel` respectively in the `PKGBUILD` file). Find your current package version and release numbers as follows:

    pacman -Q rpirtscts     # => rpirtscts 1.0-1
    pacman -Q dnt900        # => dnt900 .3.5-1
    pacman -Q rpi900        # => rpi900 1.0-2
    pacman -Q rpi900-ppp    # => rpi900-ppp 0.1-2
