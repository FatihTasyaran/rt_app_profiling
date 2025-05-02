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

      path: /tmp/nvidia.icd

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


