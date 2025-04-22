Tested Hosts
============

Following settings are tested and images are (will be) available:

``Host1``\\
**CPU**: AMD Ryzen 9 7940HS\\
**GPU1**: AMD Radeon 780M (gfx1103)\\
**GPU2**: NVIDIA GeForce RTX 4060 Max-Q\\

**OS-1**: Fedora Linux 40\\
**Kernel-1**: 6.13.8-100.fc40.x86_64\\

**OS-2**: Ubuntu 24.04 LTS\\
**Kernel-2**: 6.8.0-53-generic.x86_64\\


NVIDIA drivers might be problematic with ubuntu, in case of a hybrid laptop:
Setting rule for primary GPU to launch gnome, 
https://gitlab.gnome.org/GNOME/mutter/-/merge_requests/1562

Another option (preferred) is to switch to text interface and connect through SSH for measurements. 


Can get latest signed NVIDIA docker containers from:
https://catalog.ngc.nvidia.com/orgs/nvidia/teams/k8s/containers/container-toolkit/tags

Pull the docker container:
sudo docker pull nvcr.io/nvidia/k8s/container-toolkit:v1.17.5-ubuntu20.04 //Instead, install nvidia-container-toolkit using app
sudo docker pull nvidia/cuda:12.8.1-devel-ubuntu22.04

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