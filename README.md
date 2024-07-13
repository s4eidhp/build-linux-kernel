## How to build
- install dependencies
```
sudo apt install build-essential libncurses-dev bison flex libssl-dev libelf-dev fakeroot dwarves
```
- download latest kernel from kernel.org in tarball format and extract it
```
wget https://cdn.kernel.org/pub/linux/kernel/v6.x/linux-6.9.9.tar.xz
tar -xf linux-6.9.9.tar.xz
rm linux-6.9.9.tar.xz
```
- configure kernel using current kernel config file
```
cd linux-6.9.9
cp -v /boot/config-$(uname -r) .config
```
- change config file so that just current loaded kernel modules included in the build to reduce build time
```
make localmodconfig
```
- some modification into config file to prevent build failure
```
scripts/config --disable SYSTEM_TRUSTED_KEYS
scripts/config --disable SYSTEM_REVOCATION_KEYS
scripts/config --set-str CONFIG_SYSTEM_TRUSTED_KEYS ""
scripts/config --set-str CONFIG_SYSTEM_REVOCATION_KEYS ""
```
- build the kernel
```
fakeroot make -j $(nproc)
```
- check build process completed successfully
```
echo $?
```
## How to install
```
sudo make modules_install
sudo make headers_install
sudo make install
```
- get currently installed kernels in grub menu
```
sudo grub-mkconfig | \
     grep -iE "menuentry 'Ubuntu, with Linux" | \
     awk '{print i++ " : "$1, $2, $3, $4, $5, $6, $7}'
```
- update /etc/default/grub file (suppose want to set 4th boot entry as default option)
```
GRUB_DEFAULT="1>4"
```
- update grub
```
sudo update-grub
```
- reboot system to boot new kernel

## How to customize
- change OS name in include/linux/uts.h file
```
#define UTS_SYSNAME "s4eidhp"
```
- recompile the kernel, install it and reboot
