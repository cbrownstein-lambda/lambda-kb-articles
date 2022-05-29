# Configuring BIOS for Multi-GPU / Distributed / GPUDirect Training

1. Disable PCI Access Control Services (ACS). This can usually be accomplished
   by disabling IOMMU in the BIOS. How to do this varies from manufacturer to
   manufacturer.

1. Run the following command in a terminal to confirm that ACS has been
   disabled:

       sudo lspci -vvv | grep -i asctl

   You should see `SrcValid-` and not `SrcValid+`. If you see `SrcValid+`,
   then ACS might be enabled.

   **Note:** For machines with more than 256 logical cores, disabling IOMMU
   will cause issues. To avoid these issues:

   - On machines with Intel processors, disable HyperThreading.
   - On machines with AMD processors, add to `/etc/default/grub` the following
     parameters:

         amd_iommu=on iommu=pt

     Then run `sudo update-grub`.

1. Make sure that the
   [nvidia-peermem kernel module](http://download.nvidia.com/XFree86/Linux-x86_64/470.42.01/README/nvidia-peermem.html) is loaded. To do  this, run the following command:

       sudo modprobe nvidia-peermem
