Summary (version 0.1-2)
=======================

This package installs an automatic [PPP](http://en.wikipedia.org/wiki/Point-to-point_protocol) network link on an [RPi900](http://rpi900.com) via its DNT900 radio.

When the RPi900 is serving as a network-connected base station, the package provides an automatic network connection to any remote RPi900s which connect to the base radio. This includes the necessary setup of [routing](http://en.wikipedia.org/wiki/IP_forwarding) and [DNS](http://en.wikipedia.org/wiki/DNS) so that the remote radio may be fully networked using only the DNT900 radio link.

When the RPi900 is serving as a remote radio, the package provides an automatic network connection via the base radio to which the remote radio is connected.

Making the Package
==================

First install the [*rpi900*](../rpi900/) package, then the *rpi900-ppp* package in a like manner:

    cd ~/packages/rpi900
    makepkg --clean
    sudo pacman -U rpi900-ppp-0.1-2-armv6h.pkg.tar.xz

This should be repeated on every RPi900 (base or remote) in your radio network.

Configuration
=============

You must be root to make configuration changes. First, choose a static IP address for each RPi900 on your local network. (This may also require you to put aside some static IP address space in your router.) For the base radio, the IP address should be that already in use. Edit the `/etc/rpi900-ppp.conf` configuration file, setting the `IPADDRESS` variable to the chosen IP address:

    su
    nano /etc/rpi900-ppp.conf

If the RPi900 is operating as a base station, you'll also need to set `PROXY` to the name of the network interface being used to connect to the local network. (This will be `eth0` for ethernet, or `wlan0` for a wifi adapter.) This is required for the base station to act as a proxy for [address resolution](http://en.wikipedia.org/wiki/Proxy_ARP).

Next, configure PPP authentication, which is enabled in Linux by default. It may be preferable to control access using one of the DNT900's authentication methods. In this case, disable PPP authentication by adding `OPTIONS=noauth` to `/etc/rpi900-ppp.conf`.

Alternatively, add lines to `/etc/ppp/chap-secrets` to configure passwords for various connections. For example, to add a universal password of *sesame* for all PPP clients:

    echo "* * sesame 10.1.1.0/24" >> /etc/ppp/chap-secrets

With `10.1.1.0/24` replaced by your [local subnet](http://en.wikipedia.org/wiki/Subnetwork) in [CIDR notation](http://en.wikipedia.org/wiki/CIDR_notation#CIDR_notation).

By default the RPi900 base station will start a PPP link for every radio which connects. If you wish to restrict PPP services to certain radio MAC addresses, whitelist them as follows:

    touch /etc/rpi900-ppp.d/0x00165E.allow  # only start PPP for radio with MAC 0x00165E ...
    touch /etc/rpi900-ppp.d/0x000E3F.allow  # ... or with MAC address 0x000E3F

Alternatively, you can blacklist specific MAC addresses and run PPP on all others:

    touch /etc/rpi900-ppp.d/0x0022A6.deny   # don't start PPP for radio with MAC 0x0022A6

(Don't mix `.allow` and `.deny` configurations, it won't work!)

Operation
=========

Once the package is installed, reboot the RPi900(s). Udev rules provided by the package will automatically start a PPP connection as the radio TTYs appear. If configured correctly, you should be able to `ping` the remote RPi900 via its IP address. Then login to the remote via `ssh` and `ping google.com` to check full network connectivity including DNS. Finally, try rebooting the remote and base RPi900s to confirm that the network connections come back up automatically.

Package Contents
================

The package installs the following files:

* `/usr/lib/systemd/system/rpi900-ppp-base@.service`: this systemd service is a PPP daemon for use by a remote radio connectedn to a base station. The connection is set up as the default route and obtains DNS from the base.
* `/usr/lib/systemd/system/rpi900-ppp-remote@.service`: this systemd service is a PPP daemon for use by a base radio and provides a PPP service to a remote radio. Proxy address resolution is provided for the remote via the internet-facing network interface.
* `/usr/lib/udev/rules.d/99-rpi900-ppp.rules`: this udev rules file monitors the DNT900 service for the connection of new radios. The first rule triggers when `ATTRS{DeviceMode}=="0x01"`, correspondinging to the connection of the base radio, and launches the base PPP service. The second rules triggers when `ATTRS{local/DeviceMode}=="0x01"`--in other words, when the RPi900 is acting as the base (and the connecting radio therefore a remote). In this case, the remote PPP service is launched.
* `/etc/rpi900-ppp.conf`: edit this configuration file to set the static IP address for the RPi900 (and the network interface in the case of a base station).
* `/etc/rpi900-ppp.d/`: for a base station, add `0xXXXXXX.allow` or `0xXXXXXX.deny` files to this directory to allow or deny PPP for specific radio MAC addresses.

Release History
===============

* 2014/04/22: version 0.1-1: initial release.
* 2014/04/28: version 0.1-2: add rpi900 package as a dependency.
