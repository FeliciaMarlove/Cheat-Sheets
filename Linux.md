# Linux

## Ubuntu and Gnome

### I can't resize or move a window because the top bar is outside the screen (not visible)

Click on the window 

Press `alt+space` to show the control menu (allows to resize, move, hide...)

### Update Discord on Ubuntu

`wget https://discordapp.com/api/download?platform=linux -O ~/Downloads/discord.deb`  

`cd ~/Downloads`  

`sudo apt install ./discord.deb`

### Resize current window to half the screen and open another window on the other half of the screen

`Windows key + Left Arrow or Right Arrow` (Left pushes the window to the left, right to the right)

You can select one of the proposed windows in the snippet to display next to it.

### Display all opened windows

`Windows key`

### Create permanent aliases

Edit .bashrc or .bash_aliases

`nano ~/.bashrc`

| ℹ️ For .bash_aliases to work, you need the following script in you .bashrc |
|----------------------------------------------------------------------------|

```
if [ -f ~/.bash_aliases ]; then
. ~/.bash_aliases
fi
```

Add your aliases

`alias <myalias>='some command'`

Save the file

Update the source (the file you updated)

`source ~/.bashrc`

### Change Gnome icons theme

[Linux For Devices / Tutorials / Change icon theme in gnome desktop](https://www.linuxfordevices.com/tutorials/linux/change-icon-theme-in-gnome-de)
