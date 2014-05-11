Summary (version 0.3.4-1)
=========================

The *dnt900* package installs the [DNT900 line discipline](https://github.com/mholling/dnt900) as a kernel module. The line discipline allows you to easily access and change register values for the DNT900 radio, as well as direct data to remote DNT900 radios on the network using a separate, virtual *TTY* for each radio.

**Note:** This package is for use with a *vanilla kernel* (e.g. the *linux-raspberrypi* kernel package). RPi900 users are instead recommended to install the [*linux-rpi900*](../linux-rpi900/) package, a customised kernel for RPi900 with the DTN900 line discipline built in.

Making the Package
==================

Pre-built packages are available&ndash;follow [these instructions](../#package-repository). To build the *dnt900* package yourself, you will first need to install some build tools and the linux kernel headers (required to compile the DNT900 line discipline module code):

    sudo pacman -S base-devel git linux-raspberrypi-headers

Next, download the package files (if you haven't already), change to the *dnt900* directory, and make and install the package:

    git clone https://github.com/rpi900/packages.git
    cd packages/dnt900
    makepkg --clean
    sudo pacman -U dnt900-0.3.4-1-armv6h.pkg.tar.xz

The module will now be installed and available for use.

Operation
=========

Load the DNT900 line discipline module manually:

    sudo modprobe dnt900

Alternately, have the module load automatically during startup:

    echo dnt900 | sudo tee /etc/modules-load.d/dnt900

The `ldattach` utility is typically used to attach the line discipline to a serial port:

    ldattach -8n1 -s 9600 29 /dev/ttyAMA0

On RPi900, the easiest way to achieve the above steps is to install the [*rpi900*](../rpi900/) package. This provides a service for starting the DNT900 line discipline automatically at startup.

Updating the Kernel
===================

The DNT900 line discipline module is compiled specificically for the kernel currently in use. Upgrading your kernel (as sometimes occurs during `sudo pacman -Syu`) will cause the module to no longer be found. (This is one good reason to instead use the [*linux-rpi900*](../linux-rpi900/) kernel, which has the module built in.)

Counter this by rebuilding and reinstalling the *dnt900* package when you update the vanilla kernel. (Be sure to reboot with the new kernel first.) Alternatively you can hold back kernel upgrades by adding `IgnorePkg = linux-raspberrypi linux-raspberrypi-headers` to the `[options]` section of your `/etc/pacman.conf` file.

Release History
===============

The version number of this package will track the current version of the [DNT900 line discipline](https://github.com/mholling/dnt900).

* 2014/05/08: version 0.3.4-1: initial release.
