Summary (version 0.3.5-1)
=========================

The *dnt900* package installs the [DNT900 line discipline](https://github.com/mholling/dnt900) as a kernel module. The line discipline allows you to easily access and change register values for the DNT900 radio, as well as direct data to remote DNT900 radios on the network using a separate, virtual *TTY* for each radio.

Operation
=========

After you have built and installed the package, the DNT900 line discipline module will be available to load manually:

    sudo modprobe dnt900

Alternately, have the module load automatically during startup:

    echo dnt900 | sudo tee /etc/modules-load.d/dnt900

The `ldattach` utility is typically used to attach the line discipline to a serial port:

    ldattach -8n1 -s 9600 29 /dev/ttyAMA0

On RPi900, the easiest way to achieve the above steps is to install the [*rpi900*](../rpi900/) package. This provides a service for starting the DNT900 line discipline automatically at startup.

Updating the Kernel
===================

The DNT900 line discipline module is compiled specificically for the kernel currently in use. Upgrading your kernel (as sometimes occurs during `sudo pacman -Syu`) will cause the module to no longer be found.

Counter this by rebuilding and reinstalling the *dnt900* package when you update the `linux-raspberrypi` kernel. (Be sure to reboot with the new kernel first.) Alternatively you can hold back kernel upgrades by adding `IgnorePkg = linux-raspberrypi linux-raspberrypi-headers` to the `[options]` section of your `/etc/pacman.conf` file.

Release History
===============

The version number of this package will track the current version of the [DNT900 line discipline](https://github.com/mholling/dnt900).

* 2014/05/08: version 0.3.4-1: initial release.
* 2015/02/15: version 0.3.5-1: update for linux 3.18 kernel.
