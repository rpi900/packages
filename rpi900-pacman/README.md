Summary (version 0.1-1)
=========================

This `PKGBUILD` provides a wrapper for the [*pacman*](https://wiki.archlinux.org/index.php/pacman) package manager, configured specifically for RPi900 remote stations connected via [PPP](../rpi900-ppp/) to an RPi900 base station.

The slow connection on a remote RPi900 can cause *pacman* to give 404 errors when connecting to the standard Arch Linux repositories. To overcome this problem, *rpi900-pacman* uses the package cache on the RPi900 base station as a repository. Connection to the repository is made via SSH and SSHFS.  No setup is required on the base RPi900.

Making the Package
==================

You will first need to have the [*rpi900-ppp*](../rpi900-ppp/) package and its dependencies running on your RPi900 system. Next, change to the *rpi900-pacman* package directory then build and install the package:

    cd ~/packages/rpi900-pacman
    makepkg --clean
    sudo pacman -U rpi900-pacman-0.1-1-armv6h.pkg.tar.xz

Operation
=========

To make a package available for a remote RPi900 using *rpi900-pacman*, first install or download the package on the base RPi900 using *pacman*. On the remote RPi900, use *rpi900-pacman* with the same options as you would normally use for *pacman*. You will be prompted for the root password for the base RPi900 as part of the connection process.

If your command includes a database refresh (e.g. the `-Sy` option), the repository database on the base RPi900 will be automatically updated with any new packages which are found on the base. (This may take some time, depending on the number of additions.) For example, to upgrade your remote RPi900 system, first upgrade the base:

    sudo pacman -Syu

Then on the remote:

    sudo rpi900-pacman -Syu

The *rpi900-pacman* command uses its own *pacman* configuration file at `/etc/pacman.d/rpi900-pacman.conf`, including the custom repository. Add any *pacman* options you need to this file, just as you would normally with `/etc/pacman.conf`.

Package Contents
================

The package installs the following files:

* `/usr/bin/rpi900-pacman`: the *rpi900-pacman* wrapper script.
* `/etc/pacman.d/rpi900-pacman.conf`: the *pacman* configuration file used by *rpi900-pacman*.
* `/etc/ppp/ip-up.d/99-rpi900-pacman-up.sh`: a script which records the IP address of the base RPi900 when the PPP link comes up.
* `/etc/ppp/ip-down.d/99-rpi900-pacman-down.sh`: a corresponding script for when the link goes down.
* `/usr/share/man/man8/rpi900-pacman.8.gz`: a *man* page.

Release History
===============

* 2014/05/01: version 0.1-1: initial release.
* 2014/05/09: version 0.1-2: renamed pacman configuration file and base repository.
