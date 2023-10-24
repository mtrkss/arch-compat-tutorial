# The tutorial itself!

All of this has been tested on:
- FreeBSD 13.2
- FreeBSD 14.0-BETA4
- FreeBSD 14.0-RC1
- FreeBSD 14.0-RC2

The least problematic one appears to be `FreeBSD 14.0-RC2`.
# So, Where to start?

Before following the tutorial I recommend checking your package integrity with `sudo pkg check --checksums --all` and if something corrupt was found, manually remove and reinstall that package.

Then execute these commands to install the needed stuff:
```
sudo pkg inst pulseaudio dbus git coreutils
sudo service dbus enable
sudo service dbus start 
```
If you had archlinux-pacman installed, remove it using `sudo pkg rem archlinux-pacman`
Same with archlinux-keyring.
After that, copy the `pacman.conf` from this repo into `/usr/local/etc/pacman.conf`, [generate a mirrorlist](https://archlinux.org/mirrorlist) and drop it into `/usr/local/etc/pacman.d/mirrorlist` *(Don't forget to uncomment some mirrors!)*

# Setting up the chroot (part 1)
[download the bootstrap](https://geo.mirror.pkgbuild.com/iso/latest/archlinux-bootstrap-x86_64.tar.gz) and extract it into /compat/archlinux.
First enter the root account with `su`, then execute the following commands (replace /path/to with the actual path):
```
mkdir -pv /compat/archlinux
gtar xvpf /path/to/archlinux-bootstrap-*.tar.gz --xattrs-include='*.*' --numeric-owner --strip-components=1 -C /compat/archlinux
```
Exit the root account.

Now drop the script named `archlinux` into `/usr/local/etc/rc.d` and enable it.
If you want, you can do it like this:
```
git clone https://github.com/mtrkss/archcompat-tutorial.git /tmp/archcompat
sudo cp -v /tmp/archcompat/scripts/archlinux /usr/local/etc/rc.d
sudo chmod +x /usr/local/etc/rc.d/archlinux
sudo service archlinux enable ; sudo service archlinux start
```
Check if the filesystems are mounted with `df`.
The output should look like this:
```
Filesystem  1K-blocks     Used     Avail Capacity  Mounted on
/dev/ada1p2 233596184 16800280 198108212     8%    /
devfs               1        0         1     0%    /dev
/dev/ada1p1    524128      688    523440     0%    /boot/efi
devfs               1        0         1     0%    /compat/archlinux/dev
fdescfs             1        0         1     0%    /compat/archlinux/dev/fd
tmpfs         8828196    18584   8809612     0%    /compat/archlinux/dev/shm
/home       233596184 16800280 198108212     8%    /compat/archlinux/home
linprocfs           8        0         8     0%    /compat/archlinux/proc
linsysfs            8        0         8     0%    /compat/archlinux/sys
/tmp        233596184 16800280 198108212     8%    /compat/archlinux/tmp
fdescfs             1        0         1     0%    /dev/fd
procfs              8        0         8     0%    /proc
```
If the `/compat/archlinux` stuff is mounted we can continue.

# Fixing pacman

Right now `pacman` is broken. We can fix it by executing
```
sudo pacman-key --init
sudo pacman-key --populate
```
Now update your repos with `sudo pacman -Sy` and try installing something like ksh with `sudo pacman -S ksh`

If pacman starts screaming about PGP keys, you *may* need to run `sudo pacman --refresh-keys` 

*(keep in mind that this command takes several hours to finish)*

then initialize and populate the keyring with previous `pacman-key` commands again.

# Setting up the chroot (part 2)
Execute
```
sudo chroot /compat/archlinux /bin/bash
source /etc/profile
pacman-key --init ; pacman-key --populate
pacman -Syu base-devel git wget curl vim nano micro
```
to install some text editors and tools for building AUR packages.
