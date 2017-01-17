---
layout: post
title: Ubuntu 16.04 on Razer Blade 2016 QHD 1060 GTX
---

The Razer Blade is the best small notebook for machine learning available in the market, so here you have the steps I have follow to deal with some of the issues that the standard Ubuntu installation has.

---

### Installing Ubuntu from Windows

- First, in Windows, go to Disk Management -> Shrink volume, and free around 30-50Gb
- Download Ubuntu 16.04
- Flash it into an USB using Rufus
- Reboot & keep F1 pressed to enter BIOS
- Disable Secure Boot & Save
- Reboot & keep F12 pressed
- Select UEFI: (your externar_usb_name)
- Install Ubuntu
- Connect to your wifi
- Install Third Party & Download updates
- Choose manually partitioning (Something else)
- Select the new partition, check the format box, and add an Ext4 file system. Mount point: / We do it in this way to avoid the creation of a swap partition that could damage the SSD due to a high ratio of Writes/Reads. Discard that warning.

-----------

### Configuring Ubuntu 16.04

```bash
sudo apt update
sudo apt-upgrade
```
Could be a warning from the last kernel that is not supporting the intel i915 firmware if that enter here:
[Linux Intel Firmwares](https://01.org/linuxgraphics/intel-linux-graphics-firmwares)
1.Download the Skylake GUC v6 if it has a tar_x file, rename it to .tar, extract the folder and run sudo bash ./install.sh --install
reboot
2.Download the Kabylake DMC v1 if it has a tar_x file, rename it to .tar, extract the folder and run sudo sh install.sh
reboot
-----------
If the keyboard is not working, try rebooting multiple times from Windows, (or use the on-screen keyboard in the top right menu) it is a power issue that can be sol
add this line before exit 0:
echo -1 /sys/bus/usb/devices/3-1/power/autosuspend_delay_ms
---------------------
if you need an external bluetooth mouse and it is not working when it is connected:
sudo hciconfig hci0 sspmode 1
sudo hciconfig hci0 down
sudo hciconfig hci0 up
------------------------
I prefer to install Cinnamon Desktop due to its support to high DPI displays, and I think it looks much better than the standard Unity:
sudo add-apt-repository ppa:embrosyn/cinnamon
sudo apt update && sudo apt install cinnamon
Log-off and re login clicking on the ubuntu logo next to your username, select Cinnamon
-----------------------
INSTALL CUDA
(Check if this is still the latest version)
wget http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/cuda-repo-ubuntu1604_8.0.44-1_amd64.deb
sudo dpkg -i cuda-repo-ubuntu1604_8.0.44-1_amd64.deb
sudo apt-get update (here you could get an "Invalid Date" warning, you can ignore it)
sudo apt-get install cuda (Wait, around 2Gb will be downloaded)
gedit .bashrc
Add these 2 lines at the end:
export PATH=/usr/local/cuda-8.0/bin${PATH:+:${PATH}}
export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
------
INSTALL NVIDIA CUDA® Deep Neural Network library (cuDNN)
Go to: https://developer.nvidia.com/cudnn
Download cuDNN v5.1 Library for Linux (could be newer versions to try when you read this)
tar xvzf cudnn-8.0-linux-x64-v5.1.tgz
sudo cp cuda/include/cudnn.h /usr/local/cuda/include
sudo cp cuda/lib64/libcudnn* /usr/local/cuda/lib64
sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib64/libcudnn*
----
Now you can check the details about the card and test the installation:
nvidia-smi
cd /usr/local/cuda/samples/5_Simulations/nbody
sudo make
./nbody -benchmark -numbodies=256000 -device=0
-----
If like to me, the sound is not working:
sudo apt-get install pulseaudio pavucontrol

























Installing multiple deep learning frameworks in a single system is a dependency hell. Docker provides a solution to this issue.

[Here](https://www.tensorflow.org/versions/r0.11/get_started/os_setup.html#docker-installation) is the instructions to install Tensorflow with Docker.

There is a bug in the gcr.io/tensorflow/tensorflow:latest-gpu docker image: 

>I tensorflow/stream_executor/dso_loader.cc:99] Couldn't open CUDA library libcudnn.so. LD_LIBRARY_PATH: /usr/local/nvidia/lib:/usr/local/nvidia/lib64:
>
>I tensorflow/stream_executor/cuda/cuda_dnn.cc:1562] Unable to load cuDNN DSO

The solution is to excute this command in Docker instance:

```bash
ln -s /usr/lib/x86_64-linux-gnu/libcudnn.so.4 /usr/lib/x86_64-linux-gnu/libcudnn.so
```

For devel verison, the following instructions are needed:

```bash
export LD_LIBRARY_PATH=/usr/local/nvidia/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
```