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
    - `sudo nmcli device wifi rescan`
    - `sudo nmcli device wifi list`
- Connect to a wifi
    - `sudo nmcli device wifi connect "name1" password "hello"` - using default interface `wlan0`
    - `sudo nmcli device wifi connect "name1" password "hello" ifname wlan1` - using interface `wlan1`
    - `sudo nmcli device wifi connect "gaurav hotspot"` - If already connected in the past using password
    - `sudo nmtui` - GUI
    - `sudo raspi-config`
- Creating the connection profile explicitly with the right security settings, then bring it up
    ```bash
    sudo nmcli connection add type wifi ifname wlan0 con-name "SSID" ssid "SSID"
    sudo nmcli connection modify "SSID" wifi-sec.key-mgmt wpa-psk wifi-sec.psk "pass" connection.autoconnect yes
    sudo nmcli connection up "SSID"
    ```

- If the SSID is hidden
    ```bash
    sudo nmcli connection add type wifi ifname wlan0 con-name "SSID" ssid "SSID"
    sudo nmcli connection modify "SSID" 802-11-wireless.hidden yes wifi-sec.key-mgmt wpa-psk wifi-sec.psk "pass" connection.autoconnect yes
    sudo nmcli connection up "SSID"
    ```
- Show all the properties of any connection 
    ```bash
    sudo nmcli connection show "SSID"
    sudo nmcli connection show "SSID" | grep -E *auto*
    ```
- `SSID.nmconnection` files are stored at location - `cd /etc/NetworkManager/system-connections`

- If you copy paste `*.nmconnection` files directly then do following steps - 
    ```bash
    sudo chown root:root /etc/NetworkManager/system-connections/*.nmconnection
    sudo chmod 600 /etc/NetworkManager/system-connections/*.nmconnection
    sudo nmcli connection reload
    sudo systemctl restart NetworkManager
    ```
    - If you want validation:
        ```bash
        nmcli connection import type wifi file file.nmconnection
        ```

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

## Services
- `ps -p 1 -o comm=` - Determine thte init system your system is using.

### Using SysV init (`service` command)
`service` - This command is commonly used with init systems like SysV init. It is a high-level command that interacts with the init scripts responsible for managing services. If your system uses SysV init, this command should work.

- `sudo service hostapd start` - For starting.
- `sudo service hostapd stop` - For stopping.
- `sudo service --status-all | grep running` - List the services running actively.

### Using systemd (`systemctl` command):
`systemctl` - This command is used with systems that use systemd as the init system. Systemd is becoming increasingly prevalent in modern Linux distributions.

- `sudo systemctl start hostapd` - For starting.
- `sudo systemctl stop hostapd` - For stopping.
- `sudo systemctl enable hostapd` - To enable the service at boot.
- `sudo systemctl disable hostapd` - To disable the service at boot.
- `sudo systemctl list-units --type=service --state=running` - List the services running actively.
- `sudo systemctl restart hostapd` - To restart a service
- `sudo systemctl status hostapd` - Check the status 
- `sudo systemctl cat hostaptd.service` - To check the service file content.
- `/lib/systemd/system/wpa_supplicant.service` - Location of default service file locations.
- `/etc/systemd/system` - Location of modified service files.

## Network services 
### `dhcpcd`
- Description: `dhcpcd` is a DHCP client daemon. DHCP (Dynamic Host Configuration Protocol) is a network protocol used to automatically assign IP addresses and other network configuration information to devices on a network.
- Purpose: `dhcpcd` is responsible for obtaining an IP address dynamically from a DHCP server. It is commonly used to configure the network interface with an IP address, subnet mask, gateway, and DNS servers automatically.

### `wpa_supplicant`
- Purpose: wpa_supplicant is a Wi-Fi client that supports WPA and WPA2 encryption standards. It handles authentication and association with Wi-Fi networks.
- Configuration: Configuration is typically done in the `/etc/wpa_supplicant/wpa_supplicant.conf` file. You define network blocks with SSID and password information.
- Usage: To connect to a Wi-Fi network using wpa_supplicant, you can use the following command:
    ```bash
    sudo wpa_supplicant -c /etc/wpa_supplicant/wpa_supplicant.conf -i wlan0
    ```
    Replace wlan0 with your actual Wi-Fi interface name.
- `wpa_passphrase` tool, which is part of the wpa_supplicant package, to generate a PSK (Pre-Shared Key) hash from a plain-text passphrase.

    Here's an example:
    ```bash
    wpa_passphrase Your_SSID Your_Password
    ```
    Output will be as follows -
    ```bash
    network={
    ssid="Your_SSID"
    #psk="Your_Password"
    psk=53e3c07cf12c8873e3d807237c8e9993935533d3067570569a90fdd65c1a5d72
    }
    ```

### `NetworkManager`
- Description: `NetworkManager` is a service that manages network connections on Linux systems. It provides a high-level interface for configuration and automatic detection of network devices.
- Purpose: Connection Management: `NetworkManager` handles the configuration of wired, wireless, mobile broadband, and other network connections.
Network Profiles: It stores network profiles and automatically connects to known networks.
- Important `nmcli` commands -
    - Show all the devices 
        ```bash
        sudo nmcli device show
        ```
    - List all the wifi in the vicinity
        ```bash
        sudo nmcli device wifi list
        ```
    - List all the wifi connections
        ```bash
        sudo nmcli connection show
        ```
    - Connecting to a wifi
        ```bash
        sudo nmcli device wifi connect "NetGear" password "password"
        ```
    - List all the properties of a connection
        ```bash
        sudo nmcli show NetGear
        ```
        Also visible in following location
        ```bash
        cd /etc/NetworkManager/system-connection
        sudo cat NetGear.nmconnection
        ```
    - Modify property of a connection
        ```bash
        sudo nmcli connection modify NetGear connection.autoconnect-priority 10
        sudo nmcli connection modify NetGear connection.autoconnect yes
        ```
        Check the updated property in the file mentioned in the previous point.
    - Modify and set static ipv4
        ```bash
        nmcli connection modify "YourConnectionName" \
        ipv4.method manual \
        ipv4.address "192.168.1.2/24" \
        ipv4.gateway "192.168.1.1" \
        ipv4.dns "8.8.8.8,8.8.4.4"
        ```
        To get the dns server automatically
        ```bash
        nmcli connection modify "YourConnectionName" \
        ipv4.method manual \
        ipv4.address "192.168.1.2/24" \
        ipv4.gateway "192.168.1.1" \
        ipv4..ignore-auto-dns no
        ```

### `dnsmasq`
- Description: `dnsmasq` is a lightweight DNS and DHCP server. It can provide DNS forwarding, caching, and DHCP services.
- Purpose:
    - DNS: `dnsmasq` can act as a local DNS cache and forwarder, speeding up DNS resolution by caching queries.
    - DHCP: It can also provide DHCP services, assigning IP addresses and network configuration to devices on the local network.

### `hostapd`
- Description: `hostapd` stands for Host Access Point Daemon. It is a user space daemon for access points, including IEEE 802.11 management and authentication.
- Purpose: `hostapd` is commonly used to set up a wireless access point (AP) on a Linux system. It allows a device with a wireless network interface to act as an access point, enabling other devices to connect to it wirelessly.

### `airmon-ng`
`airmon-ng` is a command-line utility used in wireless network security testing and monitoring. It is part of the Aircrack-ng suite, which is a set of tools for assessing and testing the security of Wi-Fi networks. Here's a brief overview of `airmon-ng`:
- Description:`airmon-ng` is used for monitoring wireless interfaces on a system. It allows users to enable or disable monitor mode on wireless interfaces, which is essential for capturing and analyzing wireless network traffic.
- Key Features:Enable Monitor Mode: `airmon-ng` can be used to enable monitor mode on a wireless interface, which allows the interface to capture all wireless traffic in its range.
List Available Interfaces: It provides information about available wireless interfaces on the system.
Show Status: It shows the status of wireless interfaces, indicating whether monitor mode is enabled or disabled.
- Basic Usage:
    - To list available wireless interfaces: `airmon-ng`
    - To enable monitor mode on a specific interface (replace wlan0 with your actual interface name):
        ```
        airmon-ng start wlan0
        ```
    - To disable monitor mode on a specific interface:
         ```
         airmon-ng stop wlan0
         ```
- Use Cases: `airmon-ng` is commonly used by security professionals and ethical hackers for tasks such as packet sniffing, network analysis, and testing the security of wireless networks.

## Use `wpa_supplicant` at startup and disable `nmcli`

To ensure that `wpa_supplicant` uses the specified `wpa_supplicant.conf `file at startup, you need to make sure that the configuration file path is correctly set in the startup scripts or configuration files. Here are the general steps:

- Check wpa_supplicant Configuration File Path: Open the wpa_supplicant configuration file using a text editor. The default location is `/etc/wpa_supplicant/wpa_supplicant.conf`.
    ```bash
    sudo nano /etc/wpa_supplicant/wpa_supplicant.conf
    ```
    Make sure that your network configurations are correctly set in this file.

- Specify Configuration File Path in Startup Scripts: You need to make sure that the wpa_supplicant startup script or service unit file references the correct configuration file. For a system using systemd, you can check the `wpa_supplicant.service` unit file.
    ```bash
    sudo systemctl cat wpa_supplicant.service
    ```
    Look for the ExecStart line, which specifies the command used to start wpa_supplicant. Ensure that it includes the correct configuration file path, like:

    ```bash
    cd /etc/systemd/system
    sudo cp /lib/systemd/system/wpa_supplicant.service .
    sudo chmod 664 wpa_supplicant.service
    ExecStart=/sbin/wpa_supplicant -u -s -c /etc/wpa_supplicant/wpa_supplicant.conf -i wlan0
    ```
    For systems using other init systems (SysV init, Upstart), check the corresponding startup scripts.

- Restart wpa_supplicant:
    
    After making changes, restart the wpa_supplicant servic e to apply the new configuration:
    ```bash
    sudo systemctl restart wpa_supplicant
    ```

- Verify the status of wpa_supplicant to ensure that it's running without errors:
    ```bash 
    sudo systemctl status wpa_supplicant
    ```
    By following these steps, you can ensure that wpa_supplicant uses the specified wpa_supplicant.conf file at startup. Adjust the file paths and configurations based on your system setup and preferences.


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
