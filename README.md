# CSI SMB Driver configuration for TKGI

To install the CSI SMB Driver on TKGI a few configuration adjustments are needed.

The [tkgi](/tkgi) folder contains all yaml files with the necessary changes for the CSI SMB Driver run on TKGI. All changes have been tagged with `# TKGI-CHANGE`.

To deploy the the Driver, run:

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
