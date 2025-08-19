Fresh install to hyprland 

archinstall minimal
reboot
sudo nmcli device wifi connect "SSID_of_your_network" -a
sudo pacman -S git
bash <(curl -s "https://end-4.github.io/dots-hyprland-wiki/setup.sh")

