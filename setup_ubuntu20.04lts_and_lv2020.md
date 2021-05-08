
# Setup a test system based on Ubuntu 20.04 LTS and LabVIEW

## Create Ubuntu install media 
1. Download the iso image from 
https://releases.ubuntu.com/20.04/ubuntu-20.04.1-desktop-amd64.iso
2. Create a bootable USB stick
https://wiki.centos.org/HowTos/InstallFromUSBkey
I used Fedora Disk Writer on Windows to stay on the safe side, however, with dd this is as simple as:
```
dd if=ubuntu-20.04.1-desktop-amd64.iso of=/dev/sdb
```
3. Install Ubuntu
* Boot up the system with the USB stick and 
* Use default disk partitioning scheme (do not manually partition disk unless you know what you're doing)
* Add keyboard layouts as desired and select locale
* Create a user with admin privileges, eg. kukauser
* Disable desktop animations
https://www.itsupportwale.com/blog/disable-gnome-animation-on-ubuntu/
```
gsettings set org.gnome.desktop.interface enable-animations false
```

## Install NI LabVIEW 2020
1. Download LabVIEW 2020 for Linux
https://www.ni.com/hu-hu/support/downloads/software-products/download.labview.html#344888
LabVIEW 2020 for Linux readme - https://www.ni.com/pdf/manuals/374718n.html
2. Convert packages for Ubuntu with alien
* Mount lv2020pro-linux-mac.iso
* Open the media and copy /labview/linux/lv2020pro-linux.iso to a folder
* Extract the lv2020pro-linux.iso file to linux2020pro-linux folder
* convert rpm packages, use "--scripts" to process scripts too 
```
fakeroot alien labview-2020-appbuild-20.0.0-1.x86_64.rpm --scripts
fakeroot alien labview-2020-core-20.0.0-1.x86_64.rpm --scripts
fakeroot alien labview-2020-desktop-20.0.0-1.x86_64.rpm --scripts
fakeroot alien labview-2020-examples-20.0.0-1.x86_64.rpm --scripts
fakeroot alien labview-2020-exe-20.0.0-1.x86_64.rpm --scripts
fakeroot alien labview-2020-help-20.0.0-1.x86_64.rpm --scripts
fakeroot alien labview-2020-pro-20.0.0-1.x86_64.rpm --scripts
fakeroot alien labview-2020-ref-20.0.0-1.x86_64.rpm --scripts
fakeroot alien labview-2020-rte-20.0.0-1.x86_64.rpm --scripts
fakeroot alien lvsupport2020-vianalyzer-20.0.0-f32.x86_64.rpm --scripts
fakeroot alien nicurli-20.0.0-f0.x86_64.rpm --scripts
fakeroot alien niexfinder-base-1.0-59.noarch.rpm --scripts
fakeroot alien niexfinder-exe-1.0-59.x86_64.rpm --scripts
fakeroot alien niexfinder-labview-2020-20.0.0-1.noarch.rpm --scripts
fakeroot alien niexfinder-lib-2020-20.0.0-1.x86_64.rpm --scripts
fakeroot alien nijsonmapi-20.0.0-1.noarch.rpm --scripts
fakeroot alien nilvcli-3.0.0.49156-f4.x86_64.rpm --scripts
fakeroot alien nilvcompare-20.0.0-1.noarch.rpm --scripts
fakeroot alien nilvmerge-20.0.0-1.noarch.rpm --scripts
fakeroot alien ni-python-interface-20.0.0.49154-0+f2.x86_64.rpm --scripts
fakeroot alien ni-service-locator-20.0.0.49152-0+f0.x86_64.rpm --scripts
fakeroot alien nisslcerts-20.0.0-3.0.noarch.rpm --scripts
fakeroot alien nissli-20.0.0-f0.x86_64.rpm --scripts
fakeroot alien nitdmsi-19.0.0.49152-f0.x86_64.rpm --scripts
```
3. install ni-syscfg-runtime first 
4. Install the .deb packages
```
sudo dpkg -i *.deb
```

https://forums.ni.com/t5/LabVIEW/LabView-Installation-in-Linux-Ubuntu-16/td-p/3957701?profile.language=en

## Install NI drivers for Linux
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


