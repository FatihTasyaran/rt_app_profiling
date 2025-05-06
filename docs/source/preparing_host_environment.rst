Preparing Host Environment
==========================

For all systems

Install docker: https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository

On host, install necessary packages:

    .. code block:: bash

        apt-get install libvulkan1 qtcreator freeglut3-dev libxkbcommon-x11-0 libxcb-cursor0

For ``NVIDIA`` GPUs
----------------------------


- Install nvidia-container-toolkit **and configure your docker engine**: https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html

- Pull the docker container:

.. code-block:: bash

    sudo docker pull fatihtasyaran/rt_app_profiling:1.0

- Install NVIDIA drivers on host system (Tested with nvidia-drivers-550)


- Enable x server to use gui from within container:
    .. code-block:: bash
        
        sudo xhost +local:docker

Then launch container with: 
    ..code-block:: bash

        sudo docker run --privileged -it --runtime=nvidia --gpus all --device /dev/dri --device /dev/kfd -e DISPLAY=$DISPLAY -v /tmp/.X11-unix:/tmp/.X11-unix -e NVIDIA_VISIBLE_DEVICES=all -e NVIDIA_DRIVER_CAPABILITIES=all --cap-add=SYS_ADMIN --security-opt seccomp=unconfined -v /etc/vulkan/icd.d:/etc/vulkan/icd.d:rw -v /etc/glvnd/egl-vendor.d:/etc/glvnd/egl-vendor.d:rw -v /etc/OpenCL/vendors:/etc/OpenCL/vendors:rw --net host fatih/rtap-06-autoware-install



            