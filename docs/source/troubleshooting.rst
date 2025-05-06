Troubleshooting
===============

Frequently experienced problems with iGPU + dGPU systems
--------------------------------------------------------
NVIDIA drivers might be problematic with ubuntu, in case of a hybrid laptop:
Setting rule for primary GPU to launch gnome, 
https://gitlab.gnome.org/GNOME/mutter/-/merge_requests/1562

Another option (preferred) is to switch to text interface and connect through SSH for measurements. 


nvidia-container-toolkit is not able to generate config for docker on Ubuntu
----------------------------------------------------------------------------
For Ubuntu 22.04, remove snap docker package with sudo snap remove --purge docker and reinstall with apt
 

nsys-ui doesn't launched from within container
----------------------------------------------
make sure xhost is set 
sudo xhost +local:docker

if mounting host shared library files with -v /usr/share/vulkan/icd.d:/usr/share/vulkan/icd.d:ro, 
make sure packages qtcreator freeglut3-dev libxkbcommon-x11-0 libxcb-cursor0 are installed on host 


NVIDIA Vulkan drivers present in host but not in container
----------------------------------------------------------
Refer to profiling_nvidia for mounting NVIDIA Vulkan drivers from host system 
Then inside container, verify: 
apt-get install vulkan-tools
vkinfo | grep "GPU id"
This should print vulkan capable NVIDIA GPU, if only LLVM vulkan device is present it means container is not able to access NVIDIA GPU. 
vkcube
vkcube works if integrated GPU is vulkan capable, which is the case with AMD iGPUs and amdpu drivers on Ubuntu. Make sure selected GPU is NVIDIA, if vkcube only present black screen it means none of the GPUs on the system are usable by the container.
For some reason, vulkan drivers expect to find NVIDIA Vulkan ICD at ``/etc/vulkan/icd.d/``, however it is located at ``/usr/share/vulkan/icd.d``

A turn-around for this problem is to:

.. code-block:: bash

    sudo cp /usr/share/vulkan/nvidia_icd.json /etc/vulkan/icd.d/