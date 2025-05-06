Profiling NVIDIA Devices using Containers
=========================================


CUDA Role Patch for Container Compatibility
===========================================

This document outlines the changes made to the `autoware.dev_env.cuda` Ansible role
to ensure compatibility within a Docker container environment, without modifying host system files.

Goals
-----

- Allow Ansible playbook to complete successfully inside a container.
- Prevent writes to host-mounted or read-only system paths such as `/etc/vulkan/icd.d`.
- Redirect driver registration files to writable paths such as `/tmp`.
- Add optional symlinks for compatibility if container's `/etc` is writable.

Key Changes
-----------

1. **Register Vulkan ICD in `/tmp`**

  - **Original:**
    ::

      dest: /etc/vulkan/icd.d/nvidia_icd.json

  - **Patched:**
    ::

      dest: /tmp/nvidia_icd.json

2. **Register OpenGL EGL vendor file in `/tmp`**

  - **Original:**
    ::

      dest: /etc/glvnd/egl_vendor.d/10_nvidia.json

  - **Patched:**
    ::

      dest: /tmp/10_nvidia_egl.json

3. **Register OpenCL ICD file in `/tmp`**

  - **Original:**
    ::

      path: /etc/OpenCL/vendors/nvidia.icd

  - **Patched:**
    ::

      path: /tmp/nvidia.icdsudo ln -s /usr/bin/python3 /usr/bin/python

4. **Optional: Create symlinks (fail silently if `/etc` is read-only)**

  The following tasks were added to allow runtime compatibility when possible:

  ::

    - name: Symlink Vulkan ICD (if possible)
      become: true
      ansible.builtin.file:
        src: /tmp/nvidia_icd.json
        dest: /etc/vulkan/icd.d/nvidia_icd.json
        state: link
      ignore_errors: true

    - name: Symlink OpenGL EGL vendor file (if possible)
      become: true
      ansible.builtin.file:
        src: /tmp/10_nvidia_egl.json
        dest: /etc/glvnd/egl_vendor.d/10_nvidia.json
        state: link
      ignore_errors: true

    - name: Symlink OpenCL ICD file (if possible)
      become: true
      ansible.builtin.file:
        src: /tmp/nvidia.icd
        dest: /etc/OpenCL/vendors/nvidia.icd
        state: link
      ignore_errors: true

Notes
-----

- These changes ensure that the container setup is self-contained and non-invasive to the host.
- `ignore_errors: true` is critical to allow fallback behavior when the container has a read-only `/etc`.

Usage
-----

Replace the existing `main.yaml` file in:

::

  roles/cuda/tasks/main.yaml

With the patched version described above, or copy the relevant changes directly if combining with other updates.



Autoware Packages
-----------------
389 packages finished [52min 28s]
  23 packages had stderr output: autoware_bag_time_manager_rviz_plugin autoware_cmake autoware_costmap_generator autoware_cuda_dependency_meta autoware_elevation_map_loader autoware_external_velocity_limit_selector autoware_freespace_planning_algorithms autoware_lanelet2_extension_python autoware_lint_common autoware_radar_scan_to_pointcloud2 autoware_remaining_distance_time_calculator autoware_shape_estimation autoware_smart_mpc_trajectory_follower autoware_surround_obstacle_checker autoware_system_monitor autoware_tensorrt_classifier autoware_traffic_light_selector pointcloud_to_laserscan trt_batched_nms yabloc_common yabloc_image_processing yabloc_particle_filter yabloc_pose_initializer


Enabling `ros2_tracing`
-----------------------

git clone -b humble https://github.com/ros2/ros2_tracing.git
https://github.com/ros2/ros2_tracing/issues/77
source /root/ros2_tracing/install/setup.bash

For humble: 
https://docs.ros.org/en/humble/Tutorials/Advanced/ROS2-Tracing-Trace-and-Analyze.html#tracing
ros2 launch autoware_trace_launch trace_e2e_simulator.launch.py
