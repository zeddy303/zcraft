# 🖥️ Fresh Arch Install to Hyprland on Surface Pro 9

This guide outlines the steps to install Arch Linux with the Hyprland window manager on a Microsoft Surface Pro 9, with specific configurations for the device.

---

## 🚀 Initial Arch Install

1.  Start a minimal Arch installation. Use `archinstall` using `NetworkManager` and `grub` bootloader and add `git` package with minimal set of packages.

2.  Reboot the system after the installation is complete.

3.  Once the system is up, connect to your WiFi network using `nmcli`.

    ```bash
    sudo nmcli device wifi connect "SSID_of_your_network" -a
    ```

4.  Install git if it isn't already there.

    ```bash
    sudo pacman -S git
    ```

5.  Run the setup script for the Hyprland dotfiles.

    ```bash
    bash <(curl -s "https://end-4.github.io/dots-hyprland-wiki/setup.sh")
    ```
    Or Hyde's (requires base-devel)
    ```bash
    git clone --depth 1 https://github.com/HyDE-Project/HyDE ~/HyDE
    ```
    
7.  Follow the official **linux-surface** installation guide to ensure all necessary drivers and configurations are in place for the Surface device.

    * [https://github.com/linux-surface/linux-surface/wiki/Installation-and-Setup](https://github.com/linux-surface/linux-surface/wiki/Installation-and-Setup)

8.  **Don't forget to update your GRUB configuration** after installing the linux-surface kernel.

9.  Blacklist the **`intel-ipu6`** module by adding the following line to `/etc/modprobe.d/blacklist.conf` to fix potential freezing issues when closing cover.

    ```bash
    blacklist intel-ipu6
    ```

---

## 🛠️ Hyprland Configuration Changes

These are specific adjustments made to the Hyprland configurations for the Surface Pro 9 setup.

### **`~/.config/hypr/custom/general.conf`**

* Change the `monitor` setting to `auto` to ensure the DPI is correctly detected. Add: 
    ```
    monitor=,preferred,auto,auto,transform, 0
    ```

### **`~/.config/hypr/custom/keybinds.conf`**

* **Browser Keybind**: Change the default browser launch keybind from `Super + W` to `Super + B`.
    ```
    bind = Super, B, exec, ~/.config/hypr/hyprland/scripts/launch_first_available.sh "google-chrome-stable" "zen-browser" "firefox" "brave" "chromium" "microsoft-edge-stable" "opera" "librewolf" # Browser
    ```
* Remove Super+D habit

   ```bash
  bind = Super+Shift, F, fullscreen, 1 # Maximize - changed to avoid SuperD conflict
  ``` 

* **Floating Kitty**: Add a new keybind to launch a floating, centered `kitty` terminal.
    ```
    bind = Super+Shift, Return, exec, [float; center; size 800 600] kitty #floating kitty
    ```

### **Move Window to Workspace Silently**:

* Change the keybind to move a window to a workspace silently from `Super + Alt + N` to **`Super + Shift + N`**. This is useful for moving windows to hidden workspaces but uses easier access +Shift instead of +Alt.
    ```
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
    ```


### **`~/.config/hypr/hyprland/keybinds.conf`**

* **`Super + B` QuickShell**: Comment out the default `Super + B` keybind to avoid conflicts with the new browser keybind.

### **`~/.config/hypr/custom/rules.conf`**

* Add a rule to make the `kitty` terminal transparent. 
    ```
    windowrulev2 = opacity 0.75 0.65,class:^(kitty)$
    ```

---

## ⚙️ QuickShell Configuration Changes

Modify the QuickShell configuration file to change the weather localization.

* In `~/.config/illogical-impulse/config.json`, change `bar.weather.useUSCS` to `true` to display the temperature in Fahrenheit.
    ```json
    "bar": {
      "weather": {
        "useUSCS": true
      }
    }
    ```

* Change date format to imperial with MM/dd in same json file.

## ✏️ Additional Fine Tuning Changes

* Add `:set mouse=v` to your ~/.vimrc file if you want it to be permanent (create ~/.vimrc if it does not exist yet).

* Edit the /etc/default/grub file and uncomment the `GRUB_SAVEDEFAULT=true` setting.

* Pair Bluetooth the cli way (GUI doesn't work)

  ```
  bluetoothctl
  agent on #adding this seems to help
  default-agent #and this
  scan on
  pair C7:ED:55:17:02:B2 (or C7:ED:55:18:02:B2 if on 2nd bluetooth profile)
  trust C7:ED:55:17:02:B2
  connect C7:ED:55:17:02:B2
  quit
  ```
