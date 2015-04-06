#Raspberry Pi Setup Instructions
###Author: [gibsjose](www.gibsjose.com) – 2 April 2015

##Introduction
These notes describe the initial setup of the Raspberry Pi, once a successful install of Raspbian Wheezy has been completed.

##Initial Configuration: `raspi-config`
If the Pi is plugged into a monitor, you should see the `raspi-config` menu show up on the first boot. If not, invoke it with:
```bash
sudo raspi-config
```

In this menu, you can set things like:
* Overclocking
* Change the default password
* Language/Locale/Keyboard info
* ...

Browse through the menus and change what you will. I recommend changing the locale and keyboard settings, since, if you are not in the UK, your keyboard layout will be different. I selected all of the `en_US` locales with the `space` key, and deselected the `en_GB` locales, and then proceeded. I also chose an `EN US` keyboard layout.

I also chose to overclock at 950MHz, but that is up to you.

##WiFi

###Power Management Options
You may want to improve the stability of the WiFi by disabling power management:

1. Create a new `8192cu.conf` file in `/etc/modprobe.d/`:
```bash
sudo nano /etc/modprobe.d/8192cu.conf
```

2. Add this line and save it:
```bash
options 8192cu rtw_power_mgnt=0 rtw_enusbss=0
```

###Static IP Address
You may also want to give the Pi a static IP address for easier SSHing. **Note:** See below where I describe a different (and I would say **better**) method of SSHing into your Pi.

1. Open the `interfaces` file:
```bash
sudo nano /etc/network/interfaces
```

2. Change:
```bash
iface wlan0 inet dhcp
```
to
```bash
auto wlan0
iface wlan0 inet static
address <ADDRESS>
netmask <NETMASK>
gateway <GATEWAY>
wpa-ssid "<SSID NAME>"
wpa-psk "<SSID PASSPHRASE>"
```
Where:
    * `<ADDRESS>` is your desired IP address
    * `<NETMASK>` is your netmask address (almost always `255.255.255.0`)
    * `<GATEWAY>` is your router's IP address (usually `192.168.0.1` or `10.0.0.1`)
    * `<SSID NAME>` is the name of your SSID
    * `<SSID PASSPHRASE>` is the plaintext (yikes... this is why I recommend the next approach) password to connect to the SSID

3. Restart the networking services (you may also need to `sudo reboot` if the changes do not take effect)
```bash
sudo service networking restart
```

##Hostname Approach
Previously, we looked at setting up a static IP so that we can always know the address to SSH into (i.e. `ssh pi@10.0.0.8`).

A different/better way to SSH into the Pi is to use `avahi` to use the Pi's **hostname** to connect to it, instead of the IP address.

1. To change the hostname (the default is `raspberrypi`):
```bash
sudo nano /etc/hosts
```
and change the line:
```bash
...
127.0.1.1       raspberrypi
...
```
to
```bash
...
127.0.1.1       new_hostname
...
```

2. Install and configure `avahi` to use the hostname instead of the IP address:

    1. Install `avahi`:
    ```bash
    sudo apt-get install avahi-daemon
    ```
    2. Set it to start on boot:
    ```bash
    sudo insserv avahi-daemon
    ```
    3. Restart the daemon:
    ```bash
    sudo /etc/inid.d/avahi-daemon restart
    ```

3. Now you can connect to the Pi by using the hostname:
```bash
ssh pi@<hostname>.local
```

Don't forget the `.local` after the hostname!

##Update
Update and upgrade packages (the upgrades can take a while):
```bash
sudo apt-get update
sudo apt-get upgrade
```

##Install Useful Programs
Install some useful programs (if you so please):

**Emacs**
```bash
sudo apt-get install emacs
```

**`lsof` for checking what processes are using a specific port**
```bash
sudo apt-get install lsof

sudo lsof -i TCP:8888
```
