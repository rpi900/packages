Summary
=======

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

The DNT900 line discipline module is compiled specificically for the kernel currently installed. This is reflected in the version number of the package, which matches the kernel it was compiled against. Upgrading your kernel (as sometimes occurs during `sudo pacman -Syu`) will cause the module to no longer be found.

The Arch Linux kernel is upgraded frequently, so I recommend holding back kernel upgrades by adding `IgnorePkg = linux-raspberrypi linux-raspberrypi-headers` to the `[options]` section of your `/etc/pacman.conf` file. Then, if you decide to upgrade the kernel, use the following sequence of commands:

    sudo pacman -Rdd dnt900                                     # remove DNT900 package without dependencies
    sudo pacman -S linux-raspberrypi linux-raspberrypi-headers  # upgrade kernel and headers
    cd packages/dnt900
    makepkg --clean                                             # rebuild DNT900 package for new kernel ...
    sudo pacman -U dnt900-3.18.7-4-armv6h.pkg.tar.xz            # ... and install it

This way, when you next reboot the new DNT900 line discipline will be installed and ready to go with the new kernel.

Release History
===============

* 2014/05/08: version 0.3.4-1: initial release.
* 2015/02/15: version 0.3.5-1: update for linux 3.18 kernel.
* 2015/02/17: change versioning to track linux-raspberrypi package version.
