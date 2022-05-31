# Configuring BIOS for Multi-GPU / Distributed / GPUDirect Training

1. Disable PCI Access Control Services (ACS). This can usually be accomplished
   by disabling the IOMMU in the BIOS. The location in the BIOS to disable the
   IOMMU depends on your Lambda
   [Vector](https://lambdalabs.com/gpu-workstations/vector),
   [Scalar](https://lambdalabs.com/products/blade), or
   [Hyperplane](https://lambdalabs.com/deep-learning/servers/hyperplane-a100)
   model.

1. Run the following command in a terminal to confirm that ACS has been
   disabled:

       sudo lspci -vvv | grep -i acsctl

   You should see `SrcValid-` and not `SrcValid+`. If you see `SrcValid+`,
   then ACS might be enabled.

   **Note:** For machines with more than 255 logical cores, disabling the IOMMU
   might make the system unable to recognize the additional cores beyond 255.

   To avoid this issue:

   - On machines with Intel processors, disable Hyper-Threading in the BIOS.
   - On machines with AMD processors, disable Simultaneous Multithreading (SMT)
     in the BIOS.

   On machines with AMD processors, you can alternatively disable SMT by adding
   to add `/etc/default/grub` the following parameters:

         amd_iommu=on iommu=pt

   Then, run `sudo update-grub`.
1. Make sure that the
   [nvidia-peermem kernel module](http://download.nvidia.com/XFree86/Linux-x86_64/470.42.01/README/nvidia-peermem.html) is loaded. To do  this, run the following command:

       sudo modprobe nvidia-peermem

   If you are using
   [Bright Cluster Manager](https://www.brightcomputing.com/brightclustermanager),
   you can create the following `systemd` unit file to load `nvidia-peermem`:

   ```
   [Unit]
   Description=NVIDIA Peermem Module
   Wants=syslog.target
   After=cuda-driver.service
   ConditionPathExistsGlob=/dev/nvidia[0-9]*

  [Service]
  Type=oneshot
  ExecStart=modprobe nvidia-peermem
  TimeoutSec=300

  [Install]
  WantedBy=multi-user.target
  ```
