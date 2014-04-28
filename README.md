RPi900 Packages
===============

This repository hosts *packages* for Arch Linux ARM when used with [Raspberry Pi](http://www.raspberrypi.org/) and the [RPi900](http://rpi900.com) long-range radio board:

* The [*rpirtscts*](rpirtscts/) package installs the [rpirtscts](https://github.com/mholling/rpirtscts) flow-control utility.
* The [*rpi900*](rpi900/) package installs the [DNT900 line discipline](https://github.com/mholling/dnt900) and a systemd service for automatically attaching the line discipline to the Raspberry Pi's `ttyAMA0` serial port at startup.
* The [*rpi900-ppp*](rpi900-ppp/) package provides a set of udev rules and systemd services for starting a [PPP](http://en.wikipedia.org/wiki/Point-to-point_protocol) connection over the DNT900 radio link. Collectively, these files automatically configure an RPi900 system to provide or obtain an internet connection using the radio.

The packages are simple to build using the [Arch Build System](https://wiki.archlinux.org/index.php/Arch_Build_System) and install with the [pacman](https://wiki.archlinux.org/index.php/pacman) package manager. Minimal editing of configuration files is all that is needed to achieve a working system.

Refer to the [*rpirtscts*](rpirtscts/), [*rpi900*](rpi900/) and [*rpi900-ppp*](rpi900-ppp/) directories for detailed instructions on use of the respective packages.

Releases
========

I intend that the master branch always have the latest stable packages, with any new work in a development branch. With any new features I will bump the package version or release number of the package (`pkgver` and `pkgrel` respectively in the `PKGBUILD` file). Find your current package version and release numbers as follows:

    pacman -Q rpirtscts   # => rpirtscts 1.0-1
    pacman -Q rpi900      # => rpi900 0.3.4-2
    pacman -Q rpi900-ppp  # => rpi900-ppp 0.1-2
