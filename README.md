<b>Fresh Arch install to hyprland on Surface Pro 9</b>

archinstall minimal NetworkManager git

reboot

sudo nmcli device wifi connect "SSID_of_your_network" -a

sudo pacman -S git

bash <(curl -s "https://end-4.github.io/dots-hyprland-wiki/setup.sh")

https://github.com/linux-surface/linux-surface/wiki/Installation-and-Setup

Don't forget to update grub!

blacklist intel-ipu6 to /etc/modprobe.d/blacklist.conf

Changes to hyprland configs
.config/hypr/hyprland/general.conf #monitor  = change 1 to auto for dpi
.config/hypr/hyprland/keybinds.conf #bind change W to B for browser launch
.config/hypr/hyprland/keybinds.conf #bind comment out Super+B for quickshell left #is superficial
.config/hypr/hyprland/keybinds.conf #bind change Super+Alt+<i>n</i> to Super+Shift+<i>n</i> for move to window silently 

Changes to quickshell configs
change temp localization to F
