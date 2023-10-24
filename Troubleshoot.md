# Errors & Troubleshooting

**Here are some errors and how to fix them.** 

# yay - package missing required signature
While installing `yay` you might get this error if you modified your pacman.conf:
<p align=center>
    <img src="images/yay-error.png?raw=true" width=695 height=140>
</p>
To fix it you need to add

```LocalFileSigLevel = Optional```

to your `pacman.conf`'s [options] section.

# error: command failed to execute correctly
Ignore this error. It's usually related to systemd or /proc so just ignore it.

# Authorization required, but no authorization protocol specified
run `xhost +` as "root" of the chroot.

# "Permission Denied" when executing something
Come on, you should know that. Just do `sudo chmod +x` on the file.

# Random PGP nonesense
First of all do
```
pacman-key --init
pacman-key --populate
pacman -Syu
```
Also try installing different packages or installing the particular one that fails outside/inside of the chroot.

Arch Linux keyring is a mess.
