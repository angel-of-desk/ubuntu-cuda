##Ubuntu Setup

Create a bootable usb drive with [Ubuntu 20.04](https://releases.ubuntu.com/20.04/) 


Configuring BIOS to boot up from USB


Select `Install with Safe Graphics` when the ubuntu prompt appears.


Once the installation process is underway, in the Updates and Other Software prompt select the following options:

-Minimal Installation
-Install third-party software for graphics and Wi-Fi hardware and additional items 

**NOTE:** NVIDIA drivers installed as a result of this setting will be removed later. This proves less troublesome
than not initially installing them.

Erase disk and install

After installation, display driver might have issues, and screen will not display correctly.  
Briefly press power button to sleep. When display shuts off and power button slowly pulses, press power button again to wake up.

If you have issues logging in (e.g. desktop won’t load) power cycle (don’t restart), repeat previous steps.


##Download CUDA
Navigate to the Downloads folder to pull the CUDA run file.

```
cd ~/Downloads && \
wget https://developer.download.nvidia.com/compute/cuda/11.1.0/local_installers/cuda_11.1.0_455.23.05_linux.run
```

##Uninstall Current NVIDIA Driver

In order to remove the pre-installed NVIDIA driver and associated files, Open Ubunut's `Software & Updates` window
and select the `Additional Drivers` sub-menu.

??????

To uninstall most nvidia files run:
```
sudo apt-get remove --purge '^nvidia-.*'
```

To list any remaining files run:

```
dkpg -l | grep -i nvidia
```

Manually uninstall those packages the same way:

```
sudo apt-get remove --purge <package-name>
```

##Blacklist Nouveau

Create a file at `/etc/modprobe.d/blacklist-nouveau.conf` with the following contents: 
```
blacklist nouveau
options nouveau modeset=0
```

Regenerate the kernel initramfs:
```
$ sudo update-initramfs -u
```


##Configure Ubuntu To Boot Into A Command Console

First make a backup of the grub file in order to restore settings after installing CUDA.
```
sudo cp -n /etc/default/grub /etc/default/grub.backup
```

Edit the configuration file with your favorite editor (vim, gedit, nano, etc):

```
sudo nano /etc/default/grub
```

With the file open:
- Comment out `GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"`
- Set `GRUB_CMDLINE_LINUX="text"`
- Uncomment `GRUB_TERMINAL="console"`

Save your changes, and exit the editor. 

Apply the changes:

```
sudo update-grub

sudo systemctl set-default multi-user.target
```

Reboot:
```
sudo reboot now
```

##Install CUDA

Once you have reached the console prompt, login, and navigate to `~/Downloads` where the CUDA runner should be.

Run the installer:
```
sudo sh cuda_11.1.0_455.23.05_linux.run
```

Once installation completes, restore the original grub file and apply the changes:
```
sudo mv /etc/default/grub.backup /etc/default/grub

sudo update-grub

sudo systemctl set-default graphical.target
```

Run the `device-node-verification.sh` script from the location it was downloaded:

```
sudo ~/Downloads/device-node-verification.sh
```

Reboot:
```
sudo reboot now
```

