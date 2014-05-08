Summary (version 1.0-1)
=========================

This `PKGBUILD` creates a simple package to install the [*rpirtscts*](https://github.com/mholling/rpirtscts) hardware flow control utility for Raspberry Pi. The utility configures alternate functions for the GPIO30 and GPI031 pins, making them function as hardware flow control signals for the `/dev/ttyAMA0` serial port.

Making the Package
==================

Some basic tools are required to build the package. Install them as follows:

    sudo pacman -S base-devel git

Next, download the package files:

    git clone https://github.com/rpi900/packages.git
    cd packages/rpirtscts

Finally, make and install the package:

    makepkg --clean
    sudo pacman -U rpirtscts-1.0-1-armv6h.pkg.tar.xz

The utility should now be available for use:

    which rpirtscts  # => /usr/bin/rpirtscts

Release History
===============

The version number of this package will track the current version of the [*rpirtscts*](https://github.com/mholling/rpirtscts) software.

* 2014/04/28: version 1.0-1: initial release.
