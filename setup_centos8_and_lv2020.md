
# Setup a test system based on Cent OS 8 and LabVIEW

## Install CentOS 8 Stream 
1. Download the iso image from https://www.centos.org/centos-stream/
CentOS-Stream-8-x86_64-20201211-dvd1.iso
2. Create a bootable USB stick
https://wiki.centos.org/HowTos/InstallFromUSBkey
I used Fedora Disk Writer on Windows to stay on the safe side, however, with dd this is as simple as:
```
dd if=CentOS-Stream-8-x86_64-20201211-dvd1.iso of=/dev/sdb
```
3. Install Cent OS 8
* Boot up the system with the USB stick and 
* Use default disk partitioning scheme (do not manually partition disk unless you know what you're doing)
* Add keyboard layouts as desired and select locale
* Create a root user
* Create a user with admin privileges (super user)
* At software selection page, select Workstation (and select add-ons as desired)
A generic step-by-step Cent OS 8 installation tutorial can be found here: https://linoxide.com/distros/how-to-install-centos/
4. Disable annoying Gnome animations (optional)
```
gsettings set org.gnome.desktop.interface enable-animations false
```

## Install NI LabVIEW 2020
1. Download LabVIEW 2020 for Linux
https://www.ni.com/hu-hu/support/downloads/software-products/download.labview.html#344888
(requires an account associated with an active SSP subscription)
LabVIEW 2020 for Linux readme - https://www.ni.com/pdf/manuals/374718n.html
2. Download and Install NI Driver Software on Linux Desktop, follow steps for Cent OS 8
https://www.ni.com/hu-hu/support/documentation/supplemental/18/downloading-and-installing-ni-driver-software-on-linux-desktop.html
NI Linux Device Drivers Summer 2020 Readme - https://www.ni.com/pdf/manuals/378353a.html
Driver readme files - https://download.ni.com/ni-linux-desktop/2020.07/readme/drivers/
* make sure that you not only install the driver packages but also *-labview-2020-support
* also, if ni-daqmx fails to install ni-syscfg as dependency, install manually before ni-daqmx
```
sudo dnf install ni-syscfg-runtime
sudo dnf install ni-syscfg-labview-2020-support
sudo dnf install ni-daqmx
sudo dnf install ni-daqmx-labview-2020-support
```
* for dmm and dcpower also install the daqmx packages
```
sudo dnf install ni-dmm
sudo dnf install ni-dmm-daqmx
sudo dnf install ni-dmm-labview-2020-support
sudo dnf install ni-dcpower
sudo dnf install ni-dcpower-daqmx
sudo dnf install ni-dcpower-labview-2020-support
```
* since many common instrument drivers (eg. Pickering) rely on VISA, install ni-visa as well
```
sudo dnf install ni-visa
sudo dnf install ni-visa-labview-2020-support
```
* install PXI platform services
```
sudo dnf install ni-pxiplatformservices
sudo dnf install ni-pxiplatformservices-labview-2020-support
```
* finally run dkms to install the required kernel modules, this may take for a while ...
```
sudo dkms autoinstall
```

## Integrate AD account
https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/windows_integration_guide/introduction
SSSD should be the right way to go, need to check this with IT
https://sssd.io/docs/users/ldap_with_ad.html

## Cent OS 8 real-time kernel from RHEL
To be able to subscribe to Stream-RT repo, need to register a redhat account first
then buy subscription, which I won't do abviously .. so eventually, the subscription process will fail with 
> 'NoneType' object is not subscriptable
```
sudo subscription-manager register --username <username> --password <password> --auto-attach
sudo subscription-manager repos --enable rhel-8-for-x86_64-rt-rpms
```
credentials: dXNlcm5hbWUgcGVudGFsMDBuLCBwYXNzd29yZCBlVGQ0Mm1uS2tDRGdxdTM=
if you get the error: 
> Unable to verify server's identity: [SSL: CERTIFICATE_VERIFY_FAILED] 
the workaround is:
`sudo nano /etc/rhsm/rhsm.conf` then set `insecure = 1` to disable certificate validation:
related articles:
* https://access.redhat.com/discussions/4487561?tour=8
* https://blog.centos.org/2020/07/centos-community-newsletter-july-2020-2007/
* https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux_for_real_time/8/html/installation_guide/index

## Optimizations tips for low latency
https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux_for_real_time/8/html-single/tuning_guide/index


## Cent OS 8 kernel update
https://vitux.com/how-to-upgrade-the-kernel-on-centos-8-0/

## Known issues
Partial support for Pickering modules (PILPXI only)


