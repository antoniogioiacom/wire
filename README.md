# WiRe 0.0.2

## Wifi Reconnector

[ [home](https://github.com/antoniogioiacom/wire) /
[source](https://github.com/antoniogioiacom/wire.git) ]

### what is wire

wire is a bash script that can automatically restore a wireless internet connection if something goes wrong and never stops to check connectivity until you stop it. connection activity can be saved on log file.

it's based on `ip`, `iw`, `wpasupplicant`, `ifconfig`, `dhclient`, `ping` and `rfkill`, all available by default on most linux ditributions.

tested on linux debian >7 with a single or more wireless devices connected.


### wire in action

![wire in action](http://antoniogioia.neocities.org/stuff/wire001a.jpg)


### use case

you have a rpi with `wpasupplicant` installed in some remote place with really bad connectivity, you want to keep it connected as much as possible and you want to know what's going on.

you can run wire in a screen session with `sudo screen wire` and when you `ssh` (or login) into the rpi you can `screen -dr` the running screen session to get the output of wire with all the info you need to know.

additionally you can check the `log` file to know the exact time of any relevant connection activity.


### how to install

wire manages the connection in combination with `iw`, `ifconfig`, `dhclient` and `wpasupplicant` (if you miss them install with `sudo apt-get install iw wpasupplicant wireless-tools`).

you -cannot- run wire together with `network manager` or `wicd` or `ConnMan`.

configure `/etc/network/interfaces` to use `wlan0` (or any other interface you are going to use):

    auto wlan0
    iface wlan0 inet manual
    wpa-roam /etc/wpa_supplicant/wpa_supplicant.conf

then configure `/etc/wpa_supplicant/wpa_supplicant.conf` with the necessary connection parameters, you can add as many network as you want:

    network={
      ssid="MyWirelessNetwork"
      psk="myinternetpassword"
    }

- download the code (`git clone https://github.com/antoniogioiacom/wire.git`) or copy the source in a text file (name it `wire`)
- move it in `/usr/local/bin`
- and make it executable with `sudo chmod +x /usr/local/bin/wire`

you can start wire typing `sudo wire` in a console.


### how to use

open the script file with a text editor. at the top there are some variables you might want to change (default configuration is fine anyway):

- `save_log`: `true` if you want to save connection activity to log file
- `log`: path of log file defaults to user home directory
- `ping_test`: a remote address you want to ping to check if connection is working
- `interval`: the interval in ~seconds you want to test connectivity
- `wpa_supplicant_file`: path to wpasupplicant config file

you have to run wire as `root` or with `sudo`.

you can initiate the script with:
- `sudo wire`

or you can specify the interface:
- `sudo wire wlan0`

if you don't specify the network interface at start the program asks you to select one, type for example `wlan0`. no other user interaction is required, unless you want to stop the script with `Ctrl+C`.

the script checks the state of the interface and of the connection automatically, in case of any errors repeats the checks until connection is back. to ensure the connection is really working a ping is sent every 60 seconds (change the variable `interval` as you prefer), if ping is not successful wire checks again interface and connection until are successfully restored.

it is not possible to choose a network to connect manually yet, the srippt uses wpasupplicant config file networks to try a connection. you have to modify that file to add a network and then restart wire.


### log

wire logs connection status on screen and on file.

when connection is active screen reports connection status constantly refreshed: essid, ap, ip, channel and frequency, link quality, signal level, counters, ping attempts. if connection is not working a detailed log of reconnection attempts is shown.

on log file are reported with a date relevant connection events, interface errors and program initiation and exit.
example of saved entries:

    [11/03/15 - 07:20:07 AM] WiRe initiated
    [07:20:07 AM] Connection active after 1 attempt(s)
    [07:21:47 AM] Connection lost
    [07:21:52 AM] Connection active after 2 attempt(s)
    [07:21:59 AM] Connection lost
    [11/03/15 - 07:22:23 AM] WiRe terminated


### notice

wire is in early stage of development and in some edge cases might not work as expected.


### todo

- auto or manual network selection
- interface tuning depending on conditions


### author

[antoniogioia.com](http://antoniogioia.com) ([@antoniogioiacom](https://twitter.com/antoniogioiacom))

### license

MIT
