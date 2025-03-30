# DevuanTweaks
Useful commands for me

xfce4-appmenu-plugin
appmenu-gtk-module-common
appmenu-gtk2-module
appmenu-gtk3-module

export $(dbus-launch)
xfconf-query -c xsettings -p /Gtk/ShellShowsMenubar -n -t bool -s true
xfconf-query -c xsettings -p /Gtk/ShellShowsAppmenu -n -t bool -s true
xfconf-query -c xsettings -p /Gtk/Modules -n -t string -s
"appmenu-gtk-module"

%a %d %b %R

curl -fsSL https://download.opensuse.org/repositories/home:hawkeye116477:waterfox/Debian_12/Release.key | gpg --dearmor | sudo tee /etc/apt/trusted.gpg.d/home_hawkeye116477_waterfox.gpg > /dev/null

echo 'deb https://download.opensuse.org/repositories/home:/hawkeye116477:/waterfox/Debian_12/ /' | sudo tee /etc/apt/sources.list.d/home:hawkeye116477:waterfox.list

