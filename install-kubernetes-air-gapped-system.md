# Installing Kubernetes on Offline (Air-Gapped) Systems Using Bright

It is easiest to install Kubernetes on a system that is connected to the
Internet. However, it is still possible to install Kubernetes on a system that
does not have a connection to the Internet.

1. To install Kubernetes on a system that is not connected to the Internet (an
   offline or air-gapped) system, first follow these instructions provided by
   [Bright Computing](https://www.brightcomputing.com/):

   **Note:** We use Kubernetes v1.21 as many deprecations have been made since
   Kubernetes v1.22. As of Bright v9.1, a patch is needed to make Kubernetes
   v1.21 available.

   To create the patch, run the following command in a terminal:

   <!-- TODO: Fix the below command. -->
   ```
   cat <<END | base64 -d > k8s.patch
   LS0tIC9jbS9sb2NhbC9hcHBzL2NtLXNldHVwL2xpYi9weXRob24zLjcvc2l0ZS1wYWNrYWdlcy9j
   bXNldHVwL3BsdWdpbnMva3ViZXJuZXRlcy9xdWVzdGlvbnMucHkub3JpZwkyMDIyLTAyLTAyIDIw
   OjA2OjM1LjkxNTg3NDMzNyArMDEwMAorKysgL2NtL2xvY2FsL2FwcHMvY20tc 2V0dXAvbGliL3B5
   dGhvbjMuNy9zaXRlLXBhY2thZ2VzL2Ntc2V0dXAvcGx1Z2lucy9rdWJlcm5ldGVzL3F1ZXN0aW9u
   cy5weQkyMDIyLTAyLTAyIDIwOjA3OjE3LjM0OTU2MDQ1NyArMDEwMApAQCAtMzAwLDcgKzMwMCw3
   IEBACiAgICAgQHByb3BlcnR5CiAgICAgZGVmIGVuYWJsZWQoc2VsZikgLT4gYm9vbDoKICAgICAg
   ICAgIyBFbmFibGUgdGhpcyBvbmNlIHlvdSBoYXZlIG1vcmUgdGhhbiBvbmUgdmVyc2lvbihzKSEK
   LSAgICAgICAgcmV0dXJuIEZhbHNlCisgICAgICAgIHJldHVybiBUcnVlCiAKICAgICBkZWYgcnVu
   KHNlbGYpIC0+IE5vbmU6CiAgICAgICAgIHZlcnNpb25zID0gKAo=
   END
   ```

   To apply the patch, run the following command in a terminal:

       patch -b /cm/local/apps/cm-setup/lib/python3.7/site-packages/cmsetup/plugins/kubernetes/questions.py < k8s.patch

1. Disable Shorewall on the headnodes. To do this, Bright Computing's
   instructions on
   [disabling the Shorewall firewall on the headnode](https://kb.brightcomputing.com/knowledge-base/how-do-i-disable-the-shorewall-firewall-on-the-headnode/).

1. Run the following command in a terminal on each headnode to prevent Shorewall
   from reenabling:

       systemctl mask shorewall && systemctl mask shorewall6

1. If you are not using [Run:ai](https://www.run.ai/), run the following
   command in a terminal to install the
   [NVIDIA Container Toolkit](https://catalog.ngc.nvidia.com/orgs/nvidia/teams/k8s/containers/container-toolkit):

       apt install cm-nvidia-container-toolkit

1. Run the following command:

   ```
   helm install --wait --generate-name \
     -n gpu-operator --create-namespace \
     nvidia/gpu-operator \
     --set driver.enabled=false \
     --set toolkit.enabled=false
   ```

1. For InfiniBand in Kubernetes:

   ```
   nfd:
     enabled: false
   deployCR: true
   sriovDevicePlugin:
     deploy: false
   rdmaSharedDevicePlugin:
     deploy: true
     resources:
       - name: gpu_ib_fabric
         linkTypes: [infiniband]
         vendors: [15b3]
         deviceIDs: [101b]
         ifNames: [ibp69s0f0]
   secondaryNetwork:
     deploy: false
   ```
