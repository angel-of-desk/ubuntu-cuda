

# Installing NVIDIA's CUDA Toolkit With A Fresh Install of Ubuntu

Create a bootable usb drive with [Ubuntu 20.04](https://releases.ubuntu.com/20.04/) 

Configure USB Boot from BIOS

Save and Exit

Select `Install with Safe Graphics` when the ubuntu prompt appears.

Once the installation process is underway, in the `Updates and Other Software` prompt select *only* the following option:

- Minimal Installation

Continue. When prompted, select:

- Erase disk and install

**NOTE:**  This guide assumes you are setting up a Linux-only Lenovo Legion. It is very likley after rebooting, the display driver will have issues,
and the screen will not display correctly.  Try these steps to get a working GUI:
- Leave the garbled display on for about 10 seconds.  
- Briefly press power button to sleep. 
- Wait for the display to shut off and power button to begin slowly pulsing.
- Press power button again to wake up.
- You should now see a working GUI with a login prompt.


## Prerequisites

Install `build-essential` in order to use `gcc` (the CUDA install process depends on it).

```
sudo apt update

sudo apt install build-essential
```

In order for the CUDA installer to run sucessfully, the nouveau driver must be selected in Ubunut's `Software & Updates` window.
Navigate to `Additional Drivers` sub-menu to verify `Using X.Org X server` is selected.  Skip to the **Blacklist Nouveau** section of this
README if it's `X.Org` is already selected.

If NVIDIA's driver is active this implies the NVIDIA driver is installed.  
Select `Using X.Org X server` and apply changes.  We must now remove the installed NVIDIA driver.

To uninstall run:
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

## Blacklist Nouveau

Create a file at `/etc/modprobe.d/blacklist-nouveau.conf` with the following contents: 
```
blacklist nouveau
options nouveau modeset=0
```

Regenerate the kernel initramfs:
```
$ sudo update-initramfs -u
```


## Download CUDA

Navigate to the Downloads folder to pull the CUDA run file.

```
cd ~/Downloads

wget https://developer.download.nvidia.com/compute/cuda/11.1.0/local_installers/cuda_11.1.0_455.23.05_linux.run
```


## Configure Ubuntu To Boot Into A Command Console

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

## Install CUDA

Once you have reached the console prompt, login, and navigate to `~/Downloads` where the CUDA runner should be.

Run the installer:
```
sudo sh cuda_11.1.0_455.23.05_linux.run
```

If the installation went well, you should see success output in the console after some time.  
Post-install actions from [CUDA installation instructions Section 8](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html#post-installation-actions):

```
export PATH=/usr/local/cuda-11.1/bin${PATH:+:${PATH}}

export LD_LIBRARY_PATH=/usr/local/cuda-11.1/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
```

Run the `device-node-verification.sh` script from the location it was downloaded:

```
sudo ~/Downloads/device-node-verification.sh
```

You may see output stating some files already exist.  This is ok to ignore.


Now restore the original grub file and apply the changes to reboot with a GUI:
```
sudo mv /etc/default/grub.backup /etc/default/grub

sudo update-grub

sudo systemctl set-default graphical.target
```

Reboot:
```
sudo reboot now
```

