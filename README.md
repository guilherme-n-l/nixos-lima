# Dependencies

- [Lima](https://lima-vm.io/)
- [QEMU](https://www.qemu.org/)

# Run NixOS on a Lima VM
Heavily inspired from [patryk4815/ctftools](https://github.com/patryk4815/ctftools/tree/master/lima-vm)

## Generating the image
On a linux machine or ubuntu lima vm for example:

```bash
# install nix
sh <(curl -L https://nixos.org/nix/install) --daemon
# enable kvm feature
echo "system-features = nixos-test benchmark big-parallel kvm" >> /etc/nix/nix.conf
reboot

# build image (inside cloned repo)
git clone https://github.com/kasuboski/nixos-lima.git
cd nixos-lima
nix --extra-experimental-features nix-command --extra-experimental-features flakes build .#packages.aarch64-linux.img
cp $(readlink result)/nixos.img /tmp/lima/nixos-aarch64.img
```

On your mac:
* Move `nixos-aarch64.img` under `imgs`

## Running NixOS
```bash
limactl start --name=default nixos.yaml

lima
# switch to this repo directory
nixos-rebuild switch --flake .#nixos --use-remote-sudo
```
## Troubleshooting

### Errors while building image

- Make sure you have enough memory on builder VM

```bash
# on host machine
limactl edit --memory 8 ~/.lima/<YOUR_LIMA_VM_NAME>/lima.yaml # Use `limactl list` to find YOUR_LIMA_VM_NAME (e.g., default)
```

- Make sure you have enough space in `/tmp` directory to build the image

```bash
# inside lima VM
df -h /tmp # Should be about 5GB or more
sudo mount -o remount,size=5G /tmp/
```
