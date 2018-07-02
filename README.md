# peapod - EAPOL Proxy Daemon
## Overview
**peapod** is a daemon that proxies **IEEE 802.1X Extensible Authentication Protocol over LAN (EAPOL)** packets between Ethernet interfaces. It supports a few tricks on a per-interface basis, so it may be considered a (highly) rudimentary general-purpose transparent bridging
firewall/rewriting proxy for EAPOL.

**EAPOL** is a **port-based network access control (PNAC)** mechanism ensuring that only authorized devices are allowed to use a network. In a nutshell, EAPOL blocks regular network traffic, such as TCP/IP, from traversing the physical port (e.g. on a switch) to which a client is connected until the client successfully authenticates.

"EAPOL packet" in this sense is an Ethernet frame with the EAPOL EtherType of **0x888e** encapsulating either an EAP packet or certain EAPOL control messages.

Abilities surpassing those of a simple proxy include:

### EAPOL/EAP classification, filtering, and script execution
**Proxy only certain kinds of packets** between certain interfaces and **execute user-defined scripts** when proxying recognized packet types. This is supported for the nine EAPOL Packet Types defined by IEEE Std 802.1X-2010 and the four EAP Codes defined by IETF RFC 2284.
### VLAN priority tag handling
**Add, modify, or remove priority tags** in proxied EAPOL packets. (In fact, more than just the Priority Code Point field in the 802.1Q tag may be manipulated.)
### MAC spoofing
Change interface MAC addresses to a **user-defined address**, or to the **address of an actual supplicant** behind the proxy learned during runtime. This enables the device running **peapod** to masquerade as the supplicant and originate what appears to be authorized network traffic once the supplicant establishes an EAPOL session (as long as MACsec is not in use).

## Getting started
See the manual pages for much more extensive documentation.  
HTML versions of the man pages: [**peapod**(8)](http://htmlpreview.github.io/?https://github.com/kangtastic/peapod/blob/master/doc/peapod.8.html), [**peapod.conf**(5)](http://htmlpreview.github.io/?https://github.com/kangtastic/peapod/blob/master/doc/peapod.conf.5.html)
### Installation
~~Install the latest release for your system (.deb and .rpm packages are available)~~ NOT YET or build and install from source as seen in the next section.

Place a config file at `/etc/peapod.conf`, e.g.:

    iface eth0;
    iface eth1;

This is the minimum required config and silently proxies all EAPOL packets between **eth0** and **eth1**.  
For more complicated requirements, see the man pages.

### Usage
Start **peapod**:

    # systemctl start peapod

Logs are saved to `/var/log/peapod.log` by default. It may be helpful to refer to the log during initial setup to verify that **peapod** is doing its job.

Log verbosity may be controlled by adding the following to the beginning of the config file:

    verbosity N;

Here, `N` is 0, 1, 2, or 3. Verbosity is 0 by default.

Once everything is working properly, tell `systemd` to start **peapod** at boot:

    # systemctl enable peapod

## Building from source
Prerequisites: recent-ish versions of Linux, `systemd` as the service manager, `bison`, `flex`, `pkg-control`, and, of course, `gcc` or similar.

### Executable, man pages, examples, and systemd unit file
#### Build and install

    $ make
    # make install

#### Clean and uninstall

    $ make clean
    # make uninstall

### Source code documentation
Prerequisite: a recent-ish version of `doxygen`.

#### Build/clean and rebuild

    $ make html

The results may be found at `html/index.html` in the program sources.

#### Clean

    $ make cleanhtml