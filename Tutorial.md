# The tutorial itself!

All of this has been tested on:
- FreeBSD 13.2
- FreeBSD 14.0-BETA4
- FreeBSD 14.0-RC1
- FreeBSD 14.0-RC2
- FreeBSD 14.0
- FreeBSD 14.0-p3
- FreeBSD 14.0-p4
- FreeBSD 15-CURRENT

# So, Where to start?

Before following the tutorial I recommend checking your package integrity with `pkg check --checksums --all` and if something corrupt was found, manually remove and reinstall that package.

Then execute these commands to install the needed packages
```
pkg ins pulseaudio dbus git gtar alsa-lib
service dbus enable
service dbus start
```
If you had archlinux-pacman installed, remove it using `pkg rem archlinux-pacman` and clean up `/compat/archlinux`

----

# Setting up the chroot (part 1)
[download the bootstrap](https://geo.mirror.pkgbuild.com/iso/latest/archlinux-bootstrap-x86_64.tar.gz) and extract it into /compat/archlinux.
First execute the following commands (replace /path/to with the actual path):
```
mkdir -pv /compat/archlinux
gtar xvp --numeric-owner --strip-components=1 -f /path/to/archlinux-bootstrap-*.tar.gz -C /compat/archlinux
```

Now drop the script named `archlinux` into `/usr/local/etc/rc.d` and enable it.
If you want, you can do it like this:
```
git clone https://github.com/mtrkss/archcompat-tutorial.git /tmp/archcompat
cp -v /tmp/archcompat/scripts/archlinux /usr/local/etc/rc.d/
chmod +x /usr/local/etc/rc.d/archlinux
service archlinux enable && service archlinux start
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
If the `/compat/archlinux` directories are mounted, we can continue.

# Installing and fixing pacman
> Be aware that the FreeBSD side pacman is janky, I only recommend using it for installing and removing small packages.

To install pacman, run
```
pkg ins archlinux-keyring archlinux-pacman
```

After installing pacman, run `pacman-key --init && pacman-key --populate` as a safety precaution

After that, copy `pacman.conf` from this repo into `/usr/local/etc/pacman.conf`, [generate a mirrorlist](https://archlinux.org/mirrorlist) and drop it into `/usr/local/etc/pacman.d/mirrorlist`

Now update your repos with `pacman -Sy` and try installing something like tmux with `pacman -S tmux`

If pacman starts screaming about PGP keys, you *may* need to run `pacman-key --refresh-keys`
*(keep in mind that this command takes several hours to finish)*

## Setting up the chroot (part 2)
Configure the chroot
```
export arch="/compat/archlinux"
cp /tmp/archcompat/configs/chroot-pacman.conf $arch/etc/pacman.conf
cp /usr/local/etc/pacman.d/mirrorlist $arch/etc/pacman.d/mirrorlist
echo $(hostname) > $arch/etc/hostname
echo "en_US.UTF-8 UTF-8" >> $arch/etc/locale.gen
echo "LANG=en_US.UTF-8" > $arch/etc/locale.conf
printf "nameserver 1.1.1.1\nnameserver 1.0.0.1\n" > $arch/etc/resolv.conf
```

Chroot into Arch Linux with
```
chroot /compat/archlinux /bin/bash
source /etc/profile
```
Run `locale-gen`.

Now fix pacman here and install some text editors, tools for building [AUR packages](https://aur.archlinux.org/) and pulseaudio using
```
pacman-key --init && pacman-key --populate
pacman -Syu base-devel git sudo vim nano ed pulseaudio alsa-lib
```
At this step you'll get some errors regarding `/proc`, systemd and `/etc/passwd`. Ignore them.

The chroot is done now, it's time to install something cool!

------

# Installing an AUR helper
Inside the chroot, create a new user with the same name and UID as your FreeBSD user using `useradd $name -u $uid -m -s /bin/bash -G wheel,input,audio,video,games` (replace $uid and $name with the appropriate strings)

Set a password for root and the freshly created user with `passwd` and add the user to `/etc/sudoers`.

`su` into the user and install [yay](https://github.com/Jguer/yay) or [paru](https://github.com/Morganamilo/paru) using the instructions provided in their repository.

# Installing Discord
To sync/install Discord, run `yay -S discord`. (or `paru -S discord`)

Now drop the scripts from this repo into /opt/scripts.
```
mkdir /opt/scripts
cp /tmp/archcompat/scripts/* /opt/scripts/
```

Chmod them just in case `chmod -v +x /opt/scripts/*`.

Now let's set up Discord!

Copy the `example-wrapper` script into your Discord directory
```
cp /tmp/archcompat/scripts/example-wrapper /compat/archlinux/opt/discord/wrapper
chmod +x /compat/archlinux/opt/discord/wrapper
```
Then copy `example-exec` into /usr/local/bin
```
cp /tmp/archcompat/scripts/example-exec /usr/local/bin/discord
chmod +x /usr/local/bin/discord
```
*Note that these scripts also work for browsers.*
*If you're using the example scripts on something other than discord, just modify them.*

Now add a .desktop entry to ~/.local/share/applications. Here's an example:
```
[Desktop Entry]
StartupWMClass=discord
Name=Discord
GenericName=Internet Messenger
Comment=All-in-one voice and text chat for gamers that's free, secure, and works on both your desktop and phone.
Icon=/opt/Discord/discord.png
Type=Application
Categories=Network;InstantMessaging;
Exec=discord %F
```

Now you *should* have a working Discord shortcut and an Arch Linux(ulator) which you can use to install and run different linux apps!

To test Discord, just run `discord`.
