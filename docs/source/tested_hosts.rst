Tested Hosts
============

Following settings are tested and images are (will be) available:

``Host1``\\
**CPU**: AMD Ryzen 9 7940HS\\
**GPU1**: AMD Radeon 780M (amdgcn-amd-amdhsa--gfx1103)\\
**GPU2**: NVIDIA GeForce RTX 4060 Max-Q\\

**OS**: Ubuntu 22.04 LTS\\
**Kernel**: 6.8.0-57-generic\\

**NVIDIIA Driver Version**: 550.120\\
**CUDA Version**: 12.4\\

**ROCk Module Version**: 6.10.5\\
**ROCm Version**: 1.14\\ 




For AMD:
sudo docker run --privileged --device /dev/kfd --device /dev/dri --security-opt seccomp=unconfined -it -e DISPLAY=$DISPLAY -e XAUTHORITY=$XAUTHORITY -v /tmp/.X11-unix:/tmp/.X11-unix --net=host fatih/vulkan_profile /bin/bash

For NVIDIA:

For Ubuntu 22.04, remove snap docker package with sudo snap remove --purge docker and reinstall with apt

Install NVIDIA Container toolkit:
https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html

sudo docker run -it --gpus all --privileged --security-opt seccomp=unconfined -it -e DISPLAY=$DISPLAY -e XAUTHORITY=$XAUTHORITY -v /tmp/.X11-unix:/tmp/.X11-unix --net=host nvcr.io/nvidia/k8s/container-toolkit /bin/bash
sudo docker run -it --runtime=nvidia --gpus all -e DISPLAY=$DISPLAY -v /tmp/.X11-unix:/tmp/.X11-unix --cap-add=SYS_ADMIN --security-opt seccomp=unconfined --net host nvidia/cuda:11.6.2-base-ubuntu20.04
sudo docker run -it --runtime=nvidia --gpus all -e DISPLAY=$DISPLAY -v /tmp/.X11-unix:/tmp/.X11-unix --cap-add=SYS_ADMIN --security-opt seccomp=unconfined --net host nvidia/cuda:12.8.1-devel-ubuntu20.04


Install docker: 

For Ubuntu:
https://docs.docker.com/engine/install/ubuntu/
Note: snap docker might become problematic


To be able to share vulkan-nvidia drivers: 

sudo docker run -it --runtime=nvidia --gpus all --device /dev/dri --device /dev/kfd -e DISPLAY=$DISPLAY -v /tmp/.X11-unix:/tmp/.X11-unix -e NVIDIA_VISIBLE_DEVICES=all -e NVIDIA_DRIVER_CAPABILITIES=all -v /usr/share/vulkan/icd.d:/usr/share/vulkan/icd.d:ro -v /usr/lib/x86_64-linux-gnu:/usr/lib/x86_64-linux-gnu:ro --cap-add=SYS_ADMIN --security-opt seccomp=unconfined --net host fatih/rtap-05-demo-profile
This is problematic because cannot install new packages inside container do instead;

sudo docker run --privileged -it --runtime=nvidia --gpus all --device /dev/dri --device /dev/kfd -e DISPLAY=$DISPLAY -v /tmp/.X11-unix:/tmp/.X11-unix -e NVIDIA_VISIBLE_DEVICES=all -e NVIDIA_DRIVER_CAPABILITIES=all -v /usr/share/vulkan/icd.d:/usr/share/vulkan/icd.d:ro --cap-add=SYS_ADMIN --security-opt seccomp=unconfined --net host fatih/rtap-05-demo-profile

Actually, this is enough: 
sudo docker run --privileged -it --runtime=nvidia --gpus all --device /dev/dri --device /dev/kfd -e DISPLAY=$DISPLAY -v /tmp/.X11-unix:/tmp/.X11-unix -e NVIDIA_VISIBLE_DEVICES=all -e NVIDIA_DRIVER_CAPABILITIES=all --cap-add=SYS_ADMIN --security-opt seccomp=unconfined -v /etc/vulkan/icd.d:/etc/vulkan/icd.d:rw -v /etc/glvnd/egl-vendor.d:/etc/glvnd/egl-vendor.d:rw -v /etc/OpenCL/vendors:/etc/OpenCL/vendors:rw --net host fatih/rtap-06-autoware-install
sudo docker run --privileged -it --runtime=nvidia --gpus all --device /dev/dri --device /dev/kfd -e DISPLAY=$DISPLAY -v /tmp/.X11-unix:/tmp/.X11-unix -e NVIDIA_VISIBLE_DEVICES=all -e NVIDIA_DRIVER_CAPABILITIES=all --cap-add=SYS_ADMIN --security-opt seccomp=unconfined --net host fatih/rtap-05-demo-profile
If you mount volumes with only read permissions, setting up new environments will be problematic, just like in the case of autoware

//
//
sudo docker run --privileged -it --runtime=nvidia --gpus all --device /dev/dri --device /dev/kfd -e DISPLAY=$DISPLAY -v /tmp/.X11-unix:/tmp/.X11-unix -e NVIDIA_VISIBLE_DEVICES=all -e NVIDIA_DRIVER_CAPABILITIES=all --cap-add=SYS_ADMIN --security-opt seccomp=unconfined -v /etc/vulkan/icd.d:/etc/vulkan/icd.d:rw -v /etc/glvnd/egl-vendor.d:/etc/glvnd/egl-vendor.d:rw -v /etc/OpenCL/vendors:/etc/OpenCL/vendors:rw --net host fatih/rtap-06-autoware-install