<!--

The contents of this Documentation are subject to the Public Documentation License Version 1.01
 (the "License"); you may only use this Documentation if you comply with the terms of this License.
A copy of the License is available at http://illumos.org/license/PDL.


The Original Documentation is _________________.

The Initial Writer of the Original Documentation is Carsten Grzemba Copyright (C) 2020.
All Rights Reserved. (Initial Writer contact(s): cgrzemba@opencsw.org).

Contributor(s): ______________________________________.

Portions created by ______ are Copyright (C)_________[Insert year(s)].
All Rights Reserved. (Contributor contact(s):________________[Insert hyperlink/alias]).

-->

<img src = "../../Openindiana.png">

# Hipster Handbook - Sun Ray Software

Some notes for installation of Sun Ray Software on OpenIndiana Hipster. Sun Ray Software is used to drive desktop sessions on SunRay thin clients (desktop unit: DTU).

<i class="fa fa-info-circle fa-lg" aria-hidden="true"></i> **NOTE:**
<div class="well">
This configuration is not supported by OI nor by Oracle.
</div>
<i class="fa fa-info-circle fa-lg" aria-hidden="true"></i> **NOTE:**
<div class="well">
Since GNOME is replaced by Mate in Hipster, installation is a little bit more difficult. For Sun Ray it is still necessary to use GNOME GDM and some other GNOME applications. But it is possible to use Mate desktop with its applications like pluma or atril.

</div>

## Install Prerequisites 

If you have installed new Hipster, it is necessary to install GNOME packages for GDM and its greeter-session. For user session 'Mate Session' will be used, it is no longer necessary to install other packages needed by Gnome Session (only Gnome packages which Mate session still uses).

Install packages from publisher openindiana.org which is nessesary to run SunRay Software. This will do the package sunray-essential for you.

```
# pkg install sunray-essential
```
This will install all necessary packages for using GDM and Sun Ray Software tools on OI Hipster.

## Install Sun Ray Server Software

Sun Ray Software still can be downloaded at http://edelivery.oracle.com (registration required). Download the Software for Solaris x86-64 and for Linux x86-64. From the Linux software we take scripts for configure ISC DHCP server described later.

Extract the archives.

Install IPS packages of the Sun Ray Software from the local repository

```shell
# pkg set-publisher -g <extractdirectory>/srs_5.4.5.0-Solaris_11plus.i386/IPS.i386/ sunray
# pkg install SUNWut-srss SUNWut-srwc
```

Do **not** use `utinstall` script, because its will setup a sunray publisher by its own which will interfere with our publisher of GNOME packages.

Due to package dependencies following packages will be installed:

```
SUNWut-srss (sunray)                              5.4.5.0.38-0.0             i--
SUNWuta (sunray)                                  4.5.4.0.38-0.0             i--
SUNWutdso (sunray)                                3.5.0.0.2-0.0              i--
SUNWutdsr (sunray)                                3.5.0.0.2-0.0              i--
SUNWutesa (sunray)                                4.5.0.0.44-0.0             i--
SUNWutgsm (sunray)                                4.5.0.0.44-0.0             i--
SUNWuti (sunray)                                  4.5.5.0.38-0.0             i--
SUNWutid (sunray)                                 4.5.5.0.38-0.0             i--
SUNWutk (sunray)                                  4.5.0.0.44-0.0             i--
SUNWutm (sunray)                                  4.5.4.0.38-0.0             i--
SUNWuto (sunray)                                  4.5.4.0.38-0.0             i--
SUNWutps (sunray)                                 4.5.0.0.44-0.0             i--
SUNWutr (sunray)                                  4.5.0.0.44-0.0             i--
SUNWutref (sunray)                                4.5.0.0.44-0.0             i--
SUNWutscr (sunray)                                4.5.4.0.38-0.0             i--
SUNWutsrs (sunray)                                4.5.5.0.38-0.0             i--
SUNWutstk (sunray)                                4.5.0.0.44-0.0             i--
SUNWutsto (sunray)                                4.5.0.0.44-0.0             i--
SUNWutstr (sunray)                                4.5.0.0.44-0.0             i--
SUNWutsvt (sunray)                                1.1.0.0.3-0.0              i--
SUNWuttsc (sunray)                                2.6.4.0.11-0.0             i--
SUNWuttscd (sunray)                               2.6.4.0.11-0.0             i--
SUNWuttscr (sunray)                               2.6.4.0.11-0.0             i--
SUNWutu (sunray)                                  4.5.0.0.44-0.0             i--
SUNWutwa (sunray)                                 4.5.2.0.5-0.0              i--
SUNWutwar (sunray)                                4.5.0.0.44-0.0             i--
SUNWutwh (sunray)                                 4.5.0.0.44-0.0             i--
SUNWutwl (sunray)                                 4.5.0.0.44-0.0             i--
```

### Configure ISC DHCP server

On Hipster ISC DHCP server replaces the Sun DHCP server, but that's not a problem as ISC DHCP server for Sun Ray Server was used on Linux already.

So Sun Ray Server on Hipster has to use DHCP scripts like on Linux. But it is also possible to configure DHCP manually and to skip the `utadm` step. Here an example:

| Key | Value |
|-------------------------------|------------|
| Sunray Server Hostname: |  oi-sr-bge1 |
| Sunray Server IF name: | bge1 |
| SunRay Server LAN domain name: |  sunray.lan |
| Sunray Server IP address: | 192.168.128.1 |
| DNS servers: | 192.168.128.6, 192.168.128.1 |

```
ddns-update-style none;

# option definitions common to all supported networks...
option domain-name "sunray.lan";
option domain-name-servers 192.168.128.6, 192.168.128.1;
option subnet-mask 255.255.255.0;
option broadcast-address 192.168.128.255;
option routers 192.168.128.2;
option space SunRay;
option SunRay.Intf code 33 = text;
option SunRay.Intf "bge1";
option SunRay.AuthSrvr code 21 = ip-address;
option SunRay.AuthSrvr 192.168.128.1;
option SunRay.AltAuth code 35 = array of ip-address;
option SunRay.AltAuth 192.168.128.2;
option SunRay.FWSrvr code 31 = ip-address;
option SunRay.FWSrvr 192.168.128.2;
option SunRay.NewTVer code 23 = text;
option SunRay.NewTVer "11.1.7.0_38_2016.10.27.13.09";
option SunRay.AuthPort code 22 = integer 16;
option SunRay.AuthPort 7009;
option SunRay.LogHost  code 24 = ip-address;
option SunRay.LogHost 192.168.1.2;
option SunRay.LogKern code 25 = integer 8;
option SunRay.LogKern 6;
option SunRay.LogNet code 26 = integer 8;
option SunRay.LogNet 6;
option SunRay.LogUSB code 27 = integer 8;
option SunRay.LogUSB 6;
option SunRay.LogVid code 28 = integer 8;
option SunRay.LogVid 6;
option SunRay.LogAppl code 29 = integer 8;
option SunRay.LogAppl 6;
option SunRay.sunray-servers code 68 = text;
option SunRay.sunray-servers "oi-sr-bge1.sunray.lan";
option SunRay.sunray-config-servers code 67 = text;
option SunRay.sunray-config-servers "oi-sr-bge1.sunray.lan";

default-lease-time 86400;
max-lease-time 86400;
authoritative;

class "sun" {
  match if substring (option vendor-class-identifier, 0, 4) = "SUNW";
}

log-facility local7;

host sr1.sunray.lan {
  hardware ethernet 00:14:4f:57:a0:c1;
  fixed-address 192.168.128.50;
  vendor-option-space SunRay;
}

subnet 192.168.128.0 netmask 255.255.255.0 {
    pool { 
        allow members of "sun";
        range 192.168.128.51 192.168.128.59;
    }
}
```

Vendor specific DHCP options are described in the [Sun Ray Software: Alternate Client Initialization Reqs Using DHCP](https://docs.oracle.com/cd/E25749_01/E25745/html/Alternate-Client-Initialization-Reqs-Using-DHCP.html#Alternate-Vendor-Specific-DHCP-Options) document.
The config file `/etc/dhcp/dhcpd.conf` has to link to `/etc/inet/dhcp4.conf` so that the `svc:/network/dhcp/server:ipv4` service can leverage it.

To enable Sun Ray Software utilities to use ISC DHCP server, we take scripts located in path `/opt/SUNWut/utils` from the package sunray/oi-utils.

```shell
# pkg install sunray/oi-utils
# /opt/SUNWut/utils/apply_patches
```

### Note on Java Runtime Environment

The Sun Ray Software needs the Sun/Oracle JRE 1.7 (32 bit) which is shipped with the Sun Ray Software or can be download from the Oracle Website. The link /etc/opt/SUNWut/jre should set to the JRE install path.

```
# ln -s /usr/jdk/jre1.7.0_80 /usr/j2se
```

## Basic Configuration

You can run `utconfig` and `utadm` the way described in the [Sun Ray Software](https://docs.oracle.com/cd/E35310_01/index.html) document.

run utconfig and choose the defaults
```
# utconfig

```

Configuring SunRay network on a dedicated network interface, e.g bge1, use the following command. This will configure the ISC DHCP server.

```
# utadm -a bge1
```

If you have to use a shared network, use the command

```
# utadm -a <sever IP address>
```
This will no configure ISC DHCP server, but you can use the example in this document for add the configuration of SunRay options and subnetwork to an existing DHCP server.

run utpolicy
```
# utpolicy -a -z both -m -D
```

Now the Sun Ray server installation is ready to serve your Sun Ray DTU. For more configuration options you can use the Oracle/Sun documentation.


### GDM

the GDM service has to be enabled. lightdm should not run however.

```
# svcadm enable graphical-login/gdm
# svcadm disable graphical-login/lightdm
```
### some Panel applets crash

If some panel applets crash at session startup, e.g. clock, trash, timeslider, change panel property - background and select 'no'.

### Login Screen (gdm-greeter) won't reapear after logout

After logout the gdm-login won't reappear. That's why the script in `/opt/SUNWut/lib/gdm/revivesrsession.py` is installed, and is called by the `/etc/opt/SUNWut/gdm/SunRayPostSession/helpers/revivesession` helper script.

### Multihead group configuration

Up 16 SunRay Thin Clients can be grouped to a multihead setup, where multiple monitors will be used for a single user session. For two clients this can be done with:

```
$ /opt/SUNWut/bin/utxconfig -m on -R 2x1 -S 0,1
```

Since GTK 3.10 multi screen setup is not supported anymore. The command line option `--display` whith a dot value greate 0 will not work any more (e.g.: earlier the command `firefox --display=:11.1` has started firefox on the second display).

metacity or marco will manage only the default screen. For multihead setup it is now necessary to enable 'xinerama' mode, so that the Window manager expands the screen across all DTU's in a group.

```
$ opt/SUNWut/bin/utxconfig -x on
```

Refer to the original documentation from Oracle for configuration in general: [Multiple Monitor Configurations](https://docs.oracle.com/cd/E35310_01/E35309/html/MultipleMonitor.html)
