# Build SMB CSI Driver

You'll need go (v1.16+) installed and `GOPATH` set. You may also need [golangci-lint](https://golangci-lint.run/usage/install/) installed and in your path.

Instructions gathered from the CSI Driver repo [here](https://github.com/kubernetes-csi/csi-driver-smb/blob/master/docs/csi-dev.md).

```
# Get Repo and checkout v1.0.0 branch
mkdir -p $GOPATH/src/github.com/kubernetes-csi
git clone https://github.com/kubernetes-csi/csi-driver-smb $GOPATH/src/github.com/kubernetes-csi/csi-driver-smb
cd $GOPATH/src/github.com/kubernetes-csi/csi-driver-smb
git checkout cut-v1.0.0
# Make any necessary changes like removing the `RequirePrivacy` flag used in the `New-SmbGlobalMapping` mount command, to avoid some errors connecting to Network Storage with annonymous/guest mode enabled
vi vendor/k8s.io/mount-utils/mount_windows.go # Remove "-RequirePrivacy $true” from line 167 and save
# Build Driver
make
hack/update-gofmt.sh
make verify
# Build continer image and push image to dockerhub
docker login # Insert your credentials
export REGISTRY=jaimegag # replace with your dockerhub handler
export IMAGE_VERSION=latest
make container-all
vi Makefile # edit line 172, add "docker.io" before the two instances of $${manifest_image_folder})
make push-manifest
```

This process will push the resulting images for all os/archs in the registry used. You can find an example of what to expect in this repository: https://hub.docker.com/r/jaimegag/smb-csi/tags
