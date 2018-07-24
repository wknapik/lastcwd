# What is lastcwd ?

Lastcwd is a shell script that prints the working directory of the foreground
process running in the currently focused X window (including support for tmux).

The main purpose of this tool is to allow the user to open a new terminal with
the working directory set to the one they were already working in, using a key
binding, but it could also be used with file managers, media players, etc.

# Dependencies

bash, coreutils, grep, procps-ng, xorg-xprop.

# Limitations

## WINDOWID
tl;dr - gnome-terminal, lxterminal, terminator and screen will not work as a
source of information about the last working directory for lastcwd.
Aterm, konsole, mate-terminal, sakura, urxvt, xfce4-terminal, xterm and tmux
are known to work.

Some terminal emulators implement optimizations, that cause every one of their
windows to have the same child process. This obscures the path that lastcwd
needs to traverse - from the focused window id, through that window's process
id down to the foreground descendant running in that window.

Some of those terminal emulators, however, implement a way to reconstruct that
connection, in the form of the WINDOWID environment variable, but some don't.

With terminal multiplexers, we need to use an api specific to the given
multiplexer, to figure out the active window/pane and search for their
foreground descendant. Tmux does provide a way to do it, while screen does not.

## Terminals executing non-shells spawning tmux
When launching a terminal with a custom executable/script, that eventually
spawns tmux, that last call to tmux should be `exec`ed.  Otherwise the shell is
needlessly left in the process tree and produces a false positive.

This could be fixed in lastcwd, but the effort/added complexity don't seem to
be worth it.

# How do I use it ?

Save lastcwd somewhere in your $PATH and make it executable.

Example usage:
```
tmux new -c "$(lastcwd)"
```
```
xterm -e 'cd "$(lastcwd)" && "$(getent passwd "$(id -u)"|cut -d: -f7)"'
```
```
urxvt -cd "$(lastcwd)"
```
```
konsole --workdir "$(lastcwd)"
```
```
mate-terminal --working-directory "$(lastcwd)"
```
```
xfce4-terminal --working-directory "$(lastcwd)"
```
also
```
thunar "$(lastcwd)"
```
```
pcmanfm "$(lastcwd)"
```
etc.

This i3 keybinding opens a new xfce4-terminal with a new tmux session:
```
bindsym $mod+Return exec xfce4-terminal -x tmux new -c "$(lastcwd)"
```

# Misc

A bonus hint for tmux users - opening/splitting windows with the current path:
```
bind '"' split-window -c "#{pane_current_path}"
bind % split-window -h -c "#{pane_current_path}"
bind c new-window -c "#{pane_current_path}"
```
