# Test CSI Driver setup

## Test on Windows

### Set up a Samba Server on a Linux Kubernetes cluster
Instructions based on the [upstream docs](https://github.com/kubernetes-csi/csi-driver-smb/tree/master/deploy/example/smb-provisioner)

Deploy Docker secret, to avoid hitting download limits:
```bash
DOCKER_HUB_USER=<REDACTED>
DOCKER_HUB_PASSWORD=<REDACTED>
DOCKER_HUB_EMAIL=<REDACTED>
kubectl create secret docker-registry docker-hub-creds \
--docker-server=docker.io \
--docker-username=$DOCKER_HUB_USER \
--docker-password=$DOCKER_HUB_PASSWORD \
--docker-email=$DOCKER_HUB_EMAIL
kubectl patch serviceaccount default -p '{"imagePullSecrets": [{"name": "docker-hub-creds"}]}'
```

Deploy Meetal LB or orther LB solution:
- https://metallb.universe.tf/installation/#installation-by-manifest
- https://metallb.universe.tf/configuration/#layer-2-configuration

Create SMB Server secret:
```bash
kubectl create secret generic smbcreds --from-literal username=smbadmin --from-literal password="gonative"
```

Deploy SMB Server:
```bash
kubectl create -f https://raw.githubusercontent.com/kubernetes-csi/csi-driver-smb/master/deploy/example/smb-provisioner/smb-server-lb.yaml
```

Get the SMB Server LB/EXTERNAL IP:
```bash
kubectl get svc smb-server 
```
You will use this IP in the next section

### Use SMB server as network storage using CSI SMB Driver from a Windows container in a Windows cluster

Deploy Docker secret, to avoid hitting download limits:
```bash
DOCKER_HUB_USER=<REDACTED>
DOCKER_HUB_PASSWORD=<REDACTED>
DOCKER_HUB_EMAIL=<REDACTED>
kubectl create secret docker-registry docker-hub-creds \
--docker-server=docker.io \
--docker-username=$DOCKER_HUB_USER \
--docker-password=$DOCKER_HUB_PASSWORD \
--docker-email=$DOCKER_HUB_EMAIL
kubectl patch serviceaccount default -p '{"imagePullSecrets": [{"name": "docker-hub-creds"}]}'
```

Create secret to authenticate with SMB Server:
```bash
kubectl create secret generic smbcreds --from-literal username=smbadmin --from-literal password=“gonative"
```

Create storage class referencing secret and SMB server IP. Using a file from this repo, adjust the IP to the SMB Server LB/EXTERNAL IP:
```bash
kubectl apply -f storage-class-win.yaml
```

Deploy Test app and PVC that uses storage class. Using a file from this repo:
```bash
kubectl apply -f deployment-win.yaml
```

Check the pod is writing rows with a timestamp in the mounted folder:
```bash
kubectl exec -it busybox-smb-fdd66879f-xtnpz -- cat C:\\mnt\\smb\\data.txt
```

Check that the SMB server has that file in the correspomding folder:
```bash
kubectl exec -it smb-server-589b6679cb-l98c2 -- cat /smbshare/pvc-bd384f88-8eaf-4289-9b17-106e388e613c/subPath/data.txt
```

## Test on Linux

(Coming soon)