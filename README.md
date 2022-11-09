# PhantomOmniUbuntu
Guide on setting up phantom omni with the IEEE1394 firewire on Ubuntu. Important files are included in this repository. Also added some tips on solving problems which aren't covered widely on websites.

## Preliminary Step
Make sure the following libraries are installed in your computer.
```bash
$ sudo apt-get install freeglut3-dev x11proto-gl-dev libmotif-dev mesa-utils libglw1-mesa-dev libncurses5-dev
```

## PHANToM Device Driver (PDD) Installation
Download the contents from this [folder](https://github.com/TzeLun/PhantomOmniUbuntu/tree/main/OpenHapticsAE_Linux_v3_0). Install the PHANTOM Device Drivers (PDD) via:

For `x86` system:
```bash
$ cd [DOWNLOAD DIR]/OpenHapticsAE_Linux_v3_0/PHANTOM Device Drivers/32-bit/
$ sudo dpkg -i phantomdevicedrivers_4.3-3_i386.deb
```
For `x64` system:
```bash
$ cd [DOWNLOAD DIR]/OpenHapticsAE_Linux_v3_0/PHANTOM Device Drivers/64-bit/
$ sudo dpkg -i phantomdevicedrivers_4.3-3_amd64.deb
```
After this step, download the `Linux_JUJU_PDD` folder available [here](https://github.com/TzeLun/PhantomOmniUbuntu/tree/main/Linux_JUJU_PDD). The PDD is contained within `libPHANToMIO.so.4.3`. Hence, before continuing, make sure the previous PDD files or associated symbolic links are removed. First, check if such files exists.
```bash
$ find /usr/lib/ libPHAN*
```
If the results shows this:
```bash
/usr/lib/libPHANToMIO.so.4.3
/usr/lib/libPHANToMIO.so.4
/usr/lib/libPHANToMIO.so
```
Remove them via:
```bash
$ cd /usr/lib/
$ sudo rm libPHANToMIO.so.4.3
$ sudo rm libPHANToMIO.so.4
$ sudo rm libPHANToMIO.so
```
With that done, copy the provided PDD files in the `Linux_JUJU_PDD` into `/usr/lib`, via:

For `x86` system:
```bash
$ cd [DOWNLOAD DIR]/Linux_JUJU_PDD/Linux_JUJU_PDD_32-bit/
$ sudo cp libPHANToMIO.so.4.3 /usr/lib/
$ sudo cp libPHANToMIO.so.4 /usr/lib/
$ sudo cp libPHANToMIO.so /usr/lib/
```
For `x64` system:
```bash
$ cd [DOWNLOAD DIR]/Linux_JUJU_PDD/Linux_JUJU_PDD_64-bit/
$ sudo cp libPHANToMIO.so.4.3 /usr/lib/
$ sudo cp libPHANToMIO.so.4 /usr/lib/
$ sudo cp libPHANToMIO.so /usr/lib/
```
*NOTE: In most setup guides, only the `libPHANToMIO.so.4.3` is copied to the directory whereas the remaining two files are created via symbolic link. Doing so resulted in a driver initialization error when `PHANToMTest` is launched, in my case. This problem is solved through the direct copy/paste method instead of creating symbolic links.* 

Next, copy the `PHANToMConfiguration` file into `/usr/sbin/`:

For `x86` system:
```bash
$ cd [DOWNLOAD DIR]/Linux_JUJU_PDD/Linux_JUJU_PDD_32-bit/
$ sudo cp PHANToMConfiguration /usr/sbin/
```
For `x64` system:
```bash
$ cd [DOWNLOAD DIR]/Linux_JUJU_PDD/Linux_JUJU_PDD_64-bit/
$ sudo cp PHANToMConfiguration /usr/sbin/
```
At this point, the setup for the PDD is completed. Launch `PHANToMConfiguration` (Three alternatives given below):
```bash
$ PHANToMConfiguration
```
```bash
$ /usr/sbin/PHANToMConfiguration
```
```bash
$ LANG=en_us /usr/sbin/PHANToMConfiguration
```
If the below error appears, for Ubuntu 11.04 and above, when lauching `PHANToMConfiguration`:
```bash
$ /usr/sbin/PHANToMConfiguration
/usr/sbin/PHANToMConfiguration: error while loading shared libraries: libraw1394.so.8: cannot open shared object file: No such file or directory
```
Link the libraw1394.so.8 to libraw1394.so.11:

For `x86` system:
```bash
$ cd /usr/lib/i386-linux-gnu
$ sudo ln -s libraw1394.so.11 libraw1394.so.8
```
For `x64` system:
```bash
$ cd /usr/lib/x86_64-linux-gnu
$ sudo ln -s libraw1394.so.11 libraw1394.so.8
```
A successful launch of `PHANToMConfiguration` should look like this:

![PHANToMConfiguration](https://github.com/TzeLun/PhantomOmniUbuntu/blob/main/Supporting%20documents/phantomconfiguration.png)

Please change the Phantom model to `omni`. If the device connection is correct, the serial number should be detected below. If so, select `Apply` and `Ok`.

Finally, launch `PHANToMTest` to calibrate your device and also to verify that the system can be run successfully.
```bash
$ PHANToMTest
```
If the following error appears:

![GLX_error](https://github.com/TzeLun/PhantomOmniUbuntu/blob/main/Supporting%20documents/GLX_error.png)

But you have verified that GLX is installed and is working in your system. Then this is most likely because GLX wasn't enabled in your graphics driver. To enable it, follow the instructions below:

First, go to the xorg configuration directory.
```bash
$ cd /usr/share/x11/xorg.conf.d/
```
By listing out all the configuration files, you should see something like this:
```bash
$ ls
```
![xorg_config](https://github.com/TzeLun/PhantomOmniUbuntu/blob/main/Supporting%20documents/xorg_config.png)

For example, if you are using nvidia driver, open and edit `10-nvidia.conf`:
```bash
$ sudo gedit 10-nvidia.conf
```
At the end of the script, add this section:
```
Section "ServerFlags"
 	Option "AllowIndirectGLX" "on"
 	Option "IndirectGLX" "on"
EndSection
```
Then save and exit the file.<br/>

If permission is needed to access the firewire card, run the following line in the terminal:
```bash
$ sudo chmod 777 /dev/fw*
```
Relaunch `PHANToMTest` and it should appear successfully as so:
![phantom_test](https://github.com/TzeLun/PhantomOmniUbuntu/blob/main/Supporting%20documents/phantomtest.png)

## OpenHaptics SDK installation
Lastly, install OpenHaptics via:

For `x86` system:
```bash
$ cd [DOWNLOAD DIR]/OpenHapticsAE_Linux_v3_0/OpenHaptics-AE 3.0/32-bit/
$ sudo dpkg -i openhaptics-ae_3.0-2_i386.deb
```
For `x64` system:
```bash
$ cd [DOWNLOAD DIR]/OpenHapticsAE_Linux_v3_0/OpenHaptics-AE 3.0/64-bit/
$ sudo dpkg -i openhaptics-ae_3.0-2_amd64.deb
```
Also add 3D Touch to `/etc/environment` file, by first opening the file in editing mode:
```bash
$ sudo gedit /etc/environment
```
Then add the following lines:
```
3DTOUCH_BASE=/usr/share/3DTouch
```
Once done, save and exit.
