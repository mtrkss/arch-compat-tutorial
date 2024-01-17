# Errors & Troubleshooting

## yay - package missing required signature
While installing `yay` you might get this error if you modified your pacman.conf:
<p align=center>
    <img src="images/yay-error.png?raw=true" width=695 height=140>
</p>
To fix it you need to add

```LocalFileSigLevel = Optional```

to your `pacman.conf`'s [options] section.

## error: command failed to execute correctly
Ignore this error. It's usually related to systemd or /proc so just ignore it.

## Authorization required, but no authorization protocol specified
run `xhost +` as your FreeBSD user + the chroots root user.

## "Permission Denied" when executing something
Come on, you should know that. Just do `chmod +x` on the file.

## Random PGP/GPG/WTF nonesense
First of all do
```
pacman-key --init
pacman-key --populate
pacman -Syu
```
Also try installing different packages or installing the particular one that fails outside/inside of the chroot.

Arch Linux keyring is a mess.

## No internet
Add the following to `/etc/resolv.conf` in the chroot:
```
nameserver 1.1.1.1
nameserver 1.0.0.1
```

If this doesn't work then try different DNS resolvers like `9.9.9.9` (Quad9) or `8.8.8.8` (Google)
