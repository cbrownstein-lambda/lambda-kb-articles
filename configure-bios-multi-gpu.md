# Configuring BIOS for Multi-GPU / Distributed / GPUDirect Training

1. Disable PCI Access Control Services (ACS). This can usually be accomplished
   by disabling IOMMU in the BIOS. The location in the BIOS to disable the
   IOMMU depends on your Lambda
   [Vector](https://lambdalabs.com/gpu-workstations/vector),
   [Scalar](https://lambdalabs.com/products/blade), or
   [Hyperplane](https://lambdalabs.com/deep-learning/servers/hyperplane-a100)
   model.

1. Run the following command in a terminal to confirm that ACS has been
   disabled:

       sudo lspci -vvv | grep -i acsctl

   You should see `SrcValid-` and not `SrcValid+`. If you see `SrcValid+`,
   then ACS is enabled.

   **Note:** For machines with more than 255 logical cores, disabling IOMMU
   might make the system unable to recognize the additional cores beyond 255.

   To avoid this issue:

   - On machines with Intel processors, disable Hyper-Threading in the BIOS.
   - On machines with AMD processors, disable Simultaneous Multithreading
     (SMT) in the BIOS.

   **Note:** If SMT is needed on a machine with AMD processors, instead of
   disabling SMT in the BIOS, you can add `amd_iommu=on iommu=pt` to
   `/etc/default/grub`, then run `sudo update-grub`.
