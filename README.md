# What is lastcwd ?

Lastcwd is a shell script that prints the working directory of the foreground
process running in the currently focused X window.

It works correctly with tmux, but not screen.

The main purpose of this tool is to allow the user to open a new terminal with
the working directory set to the one they were already working in.

# What are the dependencies ?

bash, coreutils, grep, procps-ng, xorg-xdpyinfo, xorg-xprop.

# How do I use it ?

E.g.:
```
tmux new -c "$(lastcwd)"
```
```
xterm -e 'cd "$(lastcwd)" && $(getent passwd "$(id -u)"|cut -d: -f7)'
```
```
urxvt -cd "$(lastcwd)"
```
```
gnome-terminal --working-directory "$(lastcwd)"
```
```
xfce4-terminal --working-directory "$(lastcwd)"
```
```
lxterminal --working-directory="$(lastcwd)"
```
etc.

This i3 keybinding opens a new xfce4-terminal with tmux:
```
bindsym $mod+Return exec xfce4-terminal -x tmux new -c "$(lastcwd)"
```
