# Build CSI Proxy

At the time this document was written the CSI Proxy binary was not available for download in the [CSI Proxy](https://github.com/kubernetes-csi/csi-proxy) repository, so we had to build it

Instructions on how to build the `csi-proxy.exe` can be found [here](https://github.com/kubernetes-csi/csi-proxy#build).

Below are all the steps you need to follow:

1. Clone the repository
```
git clone https://github.com/kubernetes-csi/csi-proxy
```

2. Make any changes you need to do. (Optional)

  You may need to make certain customizations or adjustments to the CSI Proxy code.

  For example, I may want or need to remove the `RequirePrivacy` flag used in the `New-SmbGlobalMapping` mount command, to avoid some errors connecting to Network Storage with annonymous/guest mode enabled. To do this run:
  ```
  vi pkg/os/smb/api.go
  ```

  And remove `-RequirePrivacy $true` from line 76 ( NewSmbGlobalMapping function )

3. Build the CSI Proxy binary
You'll need go (v1.16+) installed and `GOPATH` set.

  Run:
```
sudo make build
```

4. Get the CSI Proxy binary

  Built binary can be found in `./bin/csi-proxy.exe`
