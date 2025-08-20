<b>Fresh Arch install to hyprland on Surface Pro 9</b>

archinstall minimal NetworkManager git

reboot

sudo nmcli device wifi connect "SSID_of_your_network" -a

sudo pacman -S git

bash <(curl -s "https://end-4.github.io/dots-hyprland-wiki/setup.sh")

https://github.com/linux-surface/linux-surface/wiki/Installation-and-Setup

Don't forget to update grub!

blacklist intel-ipu6 to /etc/modprobe.d/blacklist.conf

<b>Changes to hyprland configs</b>

.config/hypr/hyprland/general.conf #monitor  = change 1 to auto for dpi

.config/hypr/custom/keybinds.conf #bind change W to B for browser launch
bind = Super, B, exec, ~/.config/hypr/hyprland/scripts/launch_first_available.sh "google-chrome-stable" "zen-browser" "firefox" "brave" "chromium" "microsoft-edge-stable" "opera" "librewolf" # Browser

bind = Super+Shift, Return, exec, [float; center; size 800 600] kitty #floating kitty

.config/hypr/hyprland/keybinds.conf #bind comment out Super+B for quickshell left #is superficial

.config/hypr/custom/keybinds.conf #bind change Super+Alt+<i>n</i> to Super+Shift+<i>n</i> for move to window silently 
#/# bind = Super+Alt, Hash,, # Send to workspace # (1, 2, 3,...)
bind = Super+Shift, 1, exec, ~/.config/hypr/hyprland/scripts/workspace_action.sh movetoworkspacesilent 1 # [hidden]
bind = Super+Shift, 2, exec, ~/.config/hypr/hyprland/scripts/workspace_action.sh movetoworkspacesilent 2 # [hidden]
bind = Super+Shift, 3, exec, ~/.config/hypr/hyprland/scripts/workspace_action.sh movetoworkspacesilent 3 # [hidden]
bind = Super+Shift, 4, exec, ~/.config/hypr/hyprland/scripts/workspace_action.sh movetoworkspacesilent 4 # [hidden]
bind = Super+Shift, 5, exec, ~/.config/hypr/hyprland/scripts/workspace_action.sh movetoworkspacesilent 5 # [hidden]
bind = Super+Shift, 6, exec, ~/.config/hypr/hyprland/scripts/workspace_action.sh movetoworkspacesilent 6 # [hidden]
bind = Super+Shift, 7, exec, ~/.config/hypr/hyprland/scripts/workspace_action.sh movetoworkspacesilent 7 # [hidden]
bind = Super+Shift, 8, exec, ~/.config/hypr/hyprland/scripts/workspace_action.sh movetoworkspacesilent 8 # [hidden]
bind = Super+Shift, 9, exec, ~/.config/hypr/hyprland/scripts/workspace_action.sh movetoworkspacesilent 9 # [hidden]
bind = Super+Shift, 0, exec, ~/.config/hypr/hyprland/scripts/workspace_action.sh movetoworkspacesilent 10 # [hidden]

Cheap transparent (text is dark) in custom/rules.conf add: windowrulev2 = opacity 0.75 0.65,class:^(kitty)$

<b>Changes to quickshell configs</b>
change temp localization to F - set bar.weather.useUSCS to true in ~/.config/illogical-impulse/config.json
