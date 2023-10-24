# Errors & Troubleshooting

**Here I show some errors and how to fix them.** 

# yay - package missing required signature
While installing `yay` you might get this error if you modified your pacman.conf:
<p align=center>
    <img src="images/yay-error.png?raw=true" width=695 height=140>
</p>
To fix it you need to add

```LocalFileSigLevel = Optional```

to your `pacman.conf`'s [options] section.
