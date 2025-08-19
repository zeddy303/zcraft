Fresh install to hyprland 

archinstall minimal NetworkManager git

reboot

sudo nmcli device wifi connect "SSID_of_your_network" -a

sudo pacman -S git

bash <(curl -s "https://end-4.github.io/dots-hyprland-wiki/setup.sh")

https://github.com/linux-surface/linux-surface/wiki/Installation-and-Setup

Don't forget to update grub!
