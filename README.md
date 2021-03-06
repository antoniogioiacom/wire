# WiRe 0.0.3

## Wifi Reconnector

[ [home](https://github.com/antoniogioiacom/wire) /
[source](https://github.com/antoniogioiacom/wire.git) ]

### what is wire

wire is a command line network manager that:
- keeps tight control of wireless interface(s) and relative connections
- makes sure are properly configured and tweaked
- automatically restore connectivity if something goes wrong
- never stops to check the state of the interface and of the connection until you stop it
- can automatically scan for any network or only a specific one
- shows on screen connection status, constantly updated
- connection activity can be saved on a log file

it's based on `iw` and `wpasupplicant` and connects to the networks available in the wpasupplicant configuration file, check the "how to install" section below to know more.

you can start wire including `interface` (example: wlan0) and `ssid` (example: myhomewifi) in the command:
- `sudo wire wlan0 myhomewifi`

or you can specify only the interface and wire will automatically look for a network:
- `sudo wire wlan0`

or you can manually select an available network with:
- `sudo wire wlan0 m`

or lazy way, you'll have to specify interface soon after
- `sudo wire`

wire runs in terminal, it's written in bash, no window manager/GUI required.

tested on linux debian >7 with a single or more wireless devices connected.


### wire in action

note: until stable release every update and/or commit can slightly change the output

#### output on screen: connection active, save_log=true

    ------------------------------------------------------------------------------------/
     WiRe (0.0.3) ~ 11/07/15 - 03:08:56 PM | Press Ctrl+C to exit
     Log          : /tmp/wire-55823
    ------------------------------------------------------------------------------------/
     Interface    : wlan0
    ------------------------------------------------------------------------------------/
     Connected at : 04:08:10 PM
     Connected to : "WirelessNetwork" (AA:1A:12:AA:AA:AA)
     IP address   : 192.168.103.24/16
     Key          : WPA2-PSK
     Link quality : • 31%
     Signal level : • -86dBm
     Channel      : 5 (2432MHz)
    ------------------------------------------------------------------------------------/
     Received     : 5.23MB
     Sent         : 48.47KB
    ------------------------------------------------------------------------------------/
     Restored     : 3
     Errors       : 2
     Interval     : 120 (seconds)
    ------------------------------------------------------------------------------------/

- data is refreshed every ~second
- link quality and signal level colored level

#### output on screen: connection lost

    ------------------------------------------------------------------------------------/
     WiRe (0.0.3) ~ 11/07/15 - 03:08:47 PM | Press Ctrl+C to exit
    ------------------------------------------------------------------------------------/
     [16:41:24] Connection lost at 04:41:24 PM
     [16:41:24] Attempt to reassociate with lost network....
     [16:41:33] Sent dhcp request.........
     [16:41:49] Scanning: ok
     [16:41:51] Sent dhcp request
     [16:41:54] IP address: 192.168.54.19
     [16:41:54] Send PING (1/3) to google.com from 192.168.54.19 (1): ok

#### output on screen: manual network selection

    ------------------------------------------------------------------------------------/
     WiRe (0.0.3) ~ 11/08/15 - 07:05:17 PM | Press Ctrl+C to exit
     Log          : /tmp/wire-27517
    ------------------------------------------------------------------------------------/
     [19:05:17] System configuration: ok
    ------------------------------------------------------------------------------------/
     [19:05:21] DNS configuration: ok
    ------------------------------------------------------------------------------------/
     [19:05:22] Interface: wlan0
    ------------------------------------------------------------------------------------/
     [19:05:22] Scanning: ok
    ------------------------------------------------------------------------------------/
     [19:05:27] Select a network (type number)
    ------------------------------------------------------------------------------------/
    1) "H363EFGA2C14"                4) "linksys"
    2) "Home.Network"                5) "BarOpenWIFI"
    3) "Sitecom47E11A"
    #?

#### output on screen: errors

    ------------------------------------------------------------------------------------/
     Error log
    ------------------------------------------------------------------------------------/
     Errors              : 26 (0)
    ------------------------------------------------------------------------------------/
     Interface (wlan0)
    ------------------------------------------------------------------------------------/
     Disconnected        : 0
     Disabled            : 1
     Inactive            : 0
     Interface errors    : 0
     WPA state errors    : 0
     No response         : 0
     Network resets      : 6
     Network restarts    : 0
    ------------------------------------------------------------------------------------/
     Connection
    ------------------------------------------------------------------------------------/
     DHCP timeouts       : 7
     Ping timeouts       : 7
     Recover pings       : 0
     Success pings       : 511
     Failed pings        : 28
     Total pings         : 539
    ------------------------------------------------------------------------------------/
     Undefined errors    : 0
    ------------------------------------------------------------------------------------/


### how to install

wire manages the connection in combination with `iw`, `ifconfig`, `dhclient` and `wpasupplicant` (if you miss them install with `sudo apt-get install iw wpasupplicant wireless-tools`, the rest of required dependencies are included by default in any linux distribution).

you -cannot- run wire together with `network manager` or `wicd` or `ConnMan`.

configure `/etc/network/interfaces` to use `wlan0` (or any other interface you are going to use):

    auto wlan0
    iface wlan0 inet manual
    wpa-roam /etc/wpa_supplicant/wpa_supplicant.conf

then configure `/etc/wpa_supplicant/wpa_supplicant.conf` with the necessary connection parameters, you can add as many network blocks as you want:

    network={
      ssid="MyWirelessNetwork"
      psk="myinternetpassword"
    }

tip: with the following network block wire connects to any unsecured network as well

    network={
            key_mgmt=NONE
            priority=-999
    }

- download the code (`git clone https://github.com/antoniogioiacom/wire.git`) or copy the source in a text file (name it `wire`)
- move it in `/usr/local/bin`
- and make it executable with `sudo chmod +x /usr/local/bin/wire`

you can start wire typing `sudo wire` in a console.


### how to use

open the script file with a text editor. at the top there are some variables you might want to change (default configuration is fine anyway):

- `save_log`: `true` if you want to save connection activity to log file
- `save_ssid` : set `true` to save the ssid you connect to
- `log_file`: path of log file
- `ping_default`: a remote host you want to ping to check if connection is working, tip: dns nameservers have faster response
- `nameserver_default`: nameserver used by default if none in `/etc/resolv.conf`
- `interval`: the interval in ~seconds you want to test connectivity
- `wpa_supplicant_file`: path to wpasupplicant configuration file

you have to run wire as `root` or with `sudo`.

you can initiate the script with:
- `sudo wire`

or you can specify the interface:
- `sudo wire wlan0`

or you can specify the interface and the ssid to connect:
- `sudo wire wlan0 myhomenetwork`

or you can specify `m` for manual selection of network:
- `sudo wire wlan0 m`

if you don't specify the network interface at start the program asks you to select one, type for example `wlan0`. no other user interaction is required, unless you want to stop the script with `Ctrl+C`.

if you specify the `ssid` to connect every reconnection attempt targets the same ssid, instead if you only specify the interface every network in the wpasupplicant config will be used.
if you start wire with the `m` option a list of available networks is shown and you have to select one typing relative number.  

the script checks the state of the interface and of the connection automatically, attempting some tweaks. in case of any errors repeats the checks until connection is back. to ensure the connection is really working a DNS lookup is sent every 60 seconds (change the variable `interval` as you prefer), if it's not successful wire checks again interface and connection until are successfully restored.

the current version of wire works fine but is not 100% tested and some code will definitely change in next version.


### log

wire logs connection status on screen and on file.

when connection is active screen reports connection status constantly refreshed: essid, ap, ip, channel and frequency, link quality, signal level, counters, ping attempts. if connection is not working a detailed log of reconnection attempts is shown.

on log file are reported with a date relevant connection events, interface errors and program initiation and exit.
example of saved entries (with `save_ssid`=`true`):

    [11/07/15 - 03:08:47 PM] WiRe initiated
    [03:08:47 PM] Interval: 90 (seconds)
    [03:08:50 PM] Interface: wlan1
    [03:09:55 PM] Connection active after 4 attempt(s)
    [03:09:56 PM] Connected to: "Network2"
    [04:41:12 PM] Connection lost
    [04:41:18 PM] Connection active after 12 attempt(s)
    [04:41:19 PM] Connected to: "Network2"
    [04:41:24 PM] Connection lost
    [04:41:55 PM] Connection active after 3 attempt(s)
    [11/07/15 - 04:41:56 PM] WiRe terminated

To exit wire press `Ctrl+C`, before the program closes it's possible to read error and connection log or check actual network and interface configuration. type `q` to quit and close wire.

    ------------------------------------------------------------------------------------/
    ^C Exit options:
    ------------------------------------------------------------------------------------/
     - e to show error log
     - s to show saved networks
     - n to show network configuration
     - q to quit
    ------------------------------------------------------------------------------------/
     Type an option and press enter:


### todo

- (improve) auto and manual network selection
- (improve) error control
- interface tuning depending on conditions
- download and upload speed test


### why wire?

there are many packages out there with similar / better functionalities. despite this i want to build my solution because:

- i only want to install `wpasupplicant` and `iw` / `wireless-tools` on my laptops / embedded devices, any other network manager is "too much", even more if based on GUI
- i'm too lazy to type always the same commands and prepare the same configs on every device
- i'm inspired by real world case since i currently live in a place with terrible internet uplink and i want to optimize connectivity and keep downloads up all the time, even if it's 1KB/s.
- dealing with terrible internet connection on command line means typing the same commands multiple times. a script is your friend
- programming a script means i can decide to output only what i really want to know about the connection   
- i want to learn more about shell scripting (it's my second script ever but i'm not a linux beginner)


### author

[antoniogioia.com](http://antoniogioia.com) ([@antoniogioiacom](https://twitter.com/antoniogioiacom))

### license

MIT
