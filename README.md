# Learning-Networking

## General Networking Commands
- Show all devices
    - `sudo iw dev`
    - `sudo iwconfig`
    - `sudo ifconfig`
    - `sudo airmon-ng`
    - `sudo nmcli device`
    - `sudo nmcli device show`
    - `sudo airmon-ng`
    - `ip addr show`
    - `lsusb`
    - `lspci`
    - `netstat -i`
    - `cat /proc/net/dev`
    - `sudo ethtool wlan1` - more information about device
    - `sudo mii-tool` - to show or manipulate the status of network interfaces, deprecated on some systems
    - `cat /etc/network/interfaces`
- List all wifi in the vicinity
    - `sudo nmcli c`
    - `sudo nmcli connection show` 
    - `sudo nmcli device wifi list`
    - `sudo nmcli -f BSSID,SSID,CHAN,FREQ,SIGNAL,SECURITY dev wifi list`
    - `sudo iwlist wlan0 scan | grep 'ESSID\|Address'`
    - `sudo iw dev wlan0 scan | grep SSID`
    - `sudo airodump-ng wlan1` - monitor/ capture mode
    - `sudo aireplay-ng -9 wlan1`
    - `sudo wash -i wlan1`
- Connect to a wifi
    - `sudo nmcli device wifi connect "name1" password "hello"` - using default interface `wlan0`
    - `sudo nmcli device wifi connect "name1" password "hello" ifname wlan1` - using interface `wlan1`
    - `sudo nmcli device wifi connect "gaurav hotspot"` - If already connected in the past using password
    - `sudo nmtui` - GUI
    - `sudo raspi-config`
- Set `wlan1` to monitor mode
    - `iw`
        ```bash
        iw dev
        sudo ip link set wlan1 down
        sudo iw dev wlan1 set type monitor
        sudo ip link set wlan1 up
        ```
    - `airmon-ng`
        ```bash
        sudo airmon-ng check wlan1
        sudo airmon-ng start wlan1
        ```
    - `ifconfig`
        ```bash
        sudo ifconfig wlan1 down
        sudo iwconfig wlan1 mode monitor
        sudo ifconfig wlan1 up

        ```

## Kali useful commands
- If the wi-fi hotspot is running disable it -
    ```bash
    sudo systemctl stop hostapd
    sudo systemctl stop dnsmasq
    ```

- Put Wireless Card in Monitor Mode
    ```bash
    sudo airmon-ng start wlan1
    ```
- Capture Packets - This command will show a list of nearby Wi-Fi networks along with associated devices (stations).
    ```bash
    sudo airodump-ng wlan1
    ```
- Filter for your home network -
    ```bash
    sudo airodump-ng --bssid BSSID --channel channel -w outputfile wlan1
    ``` 
- List all adapters
    ```bash
    sudo airmon-ng
    ```
- Check the mode -
    ```bash
    sudo airmon-ng check wlan1
    ```
- Deauthorization attack
    ```bash
    sudo aireplay-ng --deauth 0 -a <wifi_mac> -c <device_mac> -D wlan1
    # 0 - keep kicking them out
    # 10 - kick them out 10 times
    ```