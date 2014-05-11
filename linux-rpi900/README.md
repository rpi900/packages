Summary
=======

The *linux-rpi900* package provides a custom linux kernel for RPi900. The kernel is derived from (and tracks) the standard [*linux-raspberrypi*](https://github.com/archlinuxarm/PKGBUILDs/tree/master/core/linux-raspberrypi) package provided by the Raspberry Pi [Arch Linux](http://archlinuxarm.org/platforms/armv6/raspberry-pi) distribution. Additional patches build in the following features:

* the [PCF8523](http://www.nxp.com/products/interface_and_connectivity/i2c/rtcs_with_i2c_interface/series/PCF8523.html) real-time clock
* high-speed serial support on `ttyAMA0`
* the [DNT900 line discipline](https://github.com/mholling/dnt900)

Making the Package
==================

Pre-built packages are available&ndash;follow [these instructions](../packages#package-repository). This is recommended for must users, since building the kernel package can be tedious!

Some dependencies are required to build the package. Install them as follows:

    $ sudo pacman -S base-devel git xmlto docbook-xsl kmod inetutils uboot-mkimage python2 bc

Download the package files (if you haven't already), then start the package build:

    $ git clone https://github.com/rpi900/packages.git
    $ cd packages/linux-rpi900
    $ makepkg --clean

On a Raspberry Pi the process is very slow, easily taking twelve hours or more. On a Raspberry Pi model A, you may run into memory issues which stop the build. (If this happens, typing `makepkg --noextract` will resume where you left off.) Decreasing GPU memory allocation in `/boot/config.txt` can help. Cross-compiling using [distcc](http://archlinuxarm.org/developers/distcc-cross-compiling) or some other arrangement can reduce compile times, but is not for beginners!

When the kernel package has been built, install it (and optionally the headers package) as follows:

    $ sudo pacman -U linux-rpi900-3.10.18-1-armv6h.pkg.tar.xz
    $ sudo pacman -U linux-rpi900-headers-3.10.18-1-armv6h.pkg.tar.xz

Since the package conflicts with the standard *linux-raspberrypi* kernel, you will be prompted to remove it. To run the new kernel, a reboot is required following installation.

Operation
=========

With the new kernel your Raspberry Pi should still operate as normal. The kernel version can be verified as follows:

    $ uname -r
    3.10.38-1-ARCH

The DNT900 line discipline (number 29) should also be available for use:

    $ cat /proc/ldiscs
    n_tty       0
    dnt900     29

If you have not previously set the real-time clock, you will first need to set it from system time via NTP:

    $ sudo ntpd -q
    $ sudo hwclock --systohc

After a reboot, the real-time clock should be recognised and used to set system time:

    $ dmesg | grep rtc
    [    2.237031] rtc-pcf8523 1-0068: rtc core: registered rtc-pcf8523 as rtc0
    [    2.293433] rtc-pcf8523 1-0068: setting system clock to 2014-05-07 10:03:01 UTC (1399456981)

The DNT900's maximum serial rate of 460.8 kbps should now also be useable. Installation of the *linux-rpi900* package adds the following lines to your `/boot/config.txt` file:

    # linux-rpi900
    init_uart_clock=12000000

**Do not alter this value**, as it's required for the high-speed serial feature to work.

Release History
===============

This package will be versioned as per the [*standard kernel*](https://github.com/archlinuxarm/PKGBUILDs/tree/master/core/linux-raspberrypi), however updates are likely to be less frequent.

* 2014/05/09: version 3.10.18-1: initial release.
