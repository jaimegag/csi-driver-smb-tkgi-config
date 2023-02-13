# CSI SMB Driver configuration for TKGI
To install the SMB CSI Driver on TKGI a few configuration adjustments are needed. Also, before deploying the CSI Driver and if you want to use it from Windows containers, you also need to install the CSI Proxy in the Windows nodes. This is to be able to run privileged storage operations in the windows nodes, as per the info [here](https://github.com/kubernetes-csi/csi-proxy#overview).

## Build CSI Proxy
CSI drivers are recommended to be deployed as containers. Node plugin containers need to run with privileges to perform storage related operations. However, Windows does not support privileged containers currently. With CSIProxy, the CSI node plugins can now be deployed as unprivileged pods that use the proxy to perform privileged storage operations on the node. Kubernetes administrators will need to install and maintain csi-proxy.exe on all Windows nodes.

Follow steps [here](./BuildCSIProxy.md) to build the `csi-proxy.exe` binary.

## Install CSI Proxy

After you build the binary you need to place it in every Windows worker node. The easiest way to do this in TKGI is to install it in the base Windows Server 2019 image and build your stemcell with it. Add the binary during the stemcell building process, while the VM is powered on before powering it off (before step 8 [here](https://docs.vmware.com/en/VMware-Tanzu-Kubernetes-Grid-Integrated-Edition/1.15/tkgi/GUID-create-vsphere-stemcell.html#remove-hidden-devices-5))

Make sure you start it as a web service that starts when the machine boots. Here's the [upstream documentation](https://github.com/kubernetes-csi/csi-proxy#installation). This is a sample of working steps:
```
# Install the csi-proxy.exe binary in this location: “C:\csi\csi-proxy.exe”
# Then we run these commands
$flags = "-windows-service -log_file=C:\csi\logs\csi-proxy.log -logtostderr=false"
sc.exe create csiproxy start= "auto" binPath= "C:\csi\csi-proxy.exe $flags"
sc.exe failure csiproxy reset= 0 actions= restart/10000
sc.exe start csiproxy
```

## Build CSI Driver
This step should be optional and only needed if certain customization is required in the CSI Driver images.
Follow [here](./BuildCSIDriver.md) to build the SMB CSI Driver Container images.

## Deploy CSI Driver
To install the CSI SMB Driver on TKGI a few configuration adjustments are needed.

The [tkgi](/tkgi) folder contains all yaml files with the necessary changes for the CSI SMB Driver run on TKGI. All changes have been tagged with `# TKGI-CHANGE`.

If you built your own CSI Driver Container Images, makes sure to replace the image URI with the right one pointing to your custom built images.

To deploy the Driver, run:

For Linux clusters:
```
kubectl apply -f rbac-csi-smb-controller.yaml
kubectl apply -f csi-smb-driver.yaml
kubectl apply -f csi-smb-controller.yaml
kubectl apply -f csi-smb-node.yaml
```

For Windows clusters:
```
kubectl apply -f rbac-csi-smb-controller.yaml
kubectl apply -f csi-smb-driver.yaml
kubectl apply -f csi-smb-controller.yaml
kubectl apply -f csi-smb-node-windows.yaml
```

The [Upstream](https://github.com/kubernetes-csi/csi-driver-smb/tree/cut-v1.0.0/deploy/v1.0.0) configuration used (v1.0.0) can be found in [upstream](/upstream) folder.
