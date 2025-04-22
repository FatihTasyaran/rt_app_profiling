Preparing Host Environment
==========================

For ``NVIDIA`` GPUs
----------------------------

Can get latest signed NVIDIA docker containers from:
https://catalog.ngc.nvidia.com/orgs/nvidia/teams/k8s/containers/container-toolkit/tags

Install nvidia-container-toolkit

Pull the docker container:
sudo docker pull nvidia/cuda:12.8.1-devel-ubuntu22.04

- Install docker for the host system
- Install NVIDIA drivers on host system (Tested with nvidia-drivers-550)



- Enable x server to use gui from within container:
    .. code-block:: bash
        
        sudo xhost +local:docker