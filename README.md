RPi900 Packages
===============

This repository hosts *packages* for Arch Linux ARM when used with [Raspberry Pi](http://www.raspberrypi.org/) and the [RPi900](http://rpi900.com) long-range radio board:

* The [*rpirtscts*](rpirtscts/) package installs the [*rpirtscts*](https://github.com/mholling/rpirtscts) flow-control utility.
* The [*linux-rpi900*](linux-rpi900/) package is a custom linux kernel for RPi900. It tracks the standard *linux-raspberrypi* kernel, adding support for RPi900's real-time clock, high-speed serial and the [DNT900 line discipline](https://github.com/mholling/dnt900).
* The [*rpi900*](rpi900/) package installs a systemd service for automatically attaching the DNT900 line discipline to the Raspberry Pi's `ttyAMA0` serial port at startup.
* The [*rpi900-ppp*](rpi900-ppp/) package provides a set of udev rules and systemd services for starting a [PPP](http://en.wikipedia.org/wiki/Point-to-point_protocol) connection over the DNT900 radio link. Collectively, these files automatically configure an RPi900 system to provide or obtain an internet connection using the radio.
* The [*rpi900-pacman*](rpi900-pacman/) package provides a wrapper for the [*pacman*](https://wiki.archlinux.org/index.php/pacman) package manager, configured specifically for RPi900 remote stations connected via PPP to an RPi900 base station.

The packages are simple to build and install with the [*pacman*](https://wiki.archlinux.org/index.php/pacman) package manager. Minimal editing of configuration files is all that is needed to achieve a working system.

Refer to the [*rpirtscts*](rpirtscts/), [*linux-rpi900*](linux-rpi900/), [*rpi900*](rpi900/) and [*rpi900-ppp*](rpi900-ppp/) directories for detailed instructions on use of the respective packages.

Package Repository
==================

Building the RPi900 packages is not necessary, as pre-built binaries are available. To use the RPi900 package repository, edit your `/etc/pacman.conf` file to add the extra repository as follows:

    [rpi900]
    Server = http://packages.rpi900.com

Refresh the pacman package list to get and view the contents of the new repository:

    $ sudo pacman -Sy
    $ pacman -Sl rpi900
    rpi900 linux-rpi900 3.10.38-1
    rpi900 linux-rpi900-headers 3.10.38-1
    rpi900 rpi900 1.0-1
    rpi900 rpi900-pacman 0.1-2
    rpi900 rpi900-ppp 0.1-3
    rpi900 rpirtscts 1.0-1

To download and install a package:

    $ sudo pacman -S linux-rpi900

If you're using package signing, you'll need to receive and trust my public key:

    $ sudo pacman-key --recv-keys matthew@rpi900.com
    $ sudo pacman-key --finger matthew@rpi900.com
    $ sudo pacman-key --lsign-key matthew@rpi900.com

My key fingerprint is `E091 7248 86A8 6C66 F558  69DD CCAD 90EE 1D35 B2D2` and should match the fingerprint produced above. My public key is also available [here](https://github.com/rpi900/packages/raw/master/rpi900.key).

Releases
========

I intend that the master branch always have the latest stable packages, with any new work in a development branch. With any new features I will bump the package version or release number of the package (`pkgver` and `pkgrel` respectively in the `PKGBUILD` file). Find your current package version and release numbers as follows:

    pacman -Q rpirtscts     # => rpirtscts 1.0-1
    pacman -Q linux-rpi900  # => linux-rpi900 3.10.38-1
    pacman -Q rpi900        # => rpi900 1.0-1
    pacman -Q rpi900-ppp    # => rpi900-ppp 0.1-2
