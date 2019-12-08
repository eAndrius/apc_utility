# APC PDU tool

An APC PDU utility tool for sending commands via telnet, without capability or expectation of any output. Tested on AP7922B and Network Management Card AOS v6.4.4.

Systemd service scripts provided here will a) turn on remote PDU outlet just before server reboot or shutdown (`apc_utility_auto_on.service`) and b) will turn off the remote PDU outlet 3h after it fully boots (`apc_utility_auto_off.timer`).

# Installing
Install required dependencies:
```console
# apt install expect git
```

Clone the repo:
```console
$ git clone git@github.com:eAndrius/apc_utility.git
```

Install scripts in the expected locations:
```console
$ cd apc_utility

# cp apc_utility.expect /usr/bin/apc_utility
# chmod +x /usr/bin/apc_utility
# cp apc_utility_auto_onoff.conf /etc/default/apc_utility_auto_onoff.conf
# cp apc_utility_auto_*.{service,timer} /etc/systemd/system/
```

Customize APC PDU configuration:
```console
# vim /etc/default/apc_utility_auto_onoff.conf
```

Reload Systemd daemon list and start services:
```console
# systemctl daemon-reload
# systemctl enable --now apc_utility_auto_on.service
# systemctl enable --now apc_utility_auto_off.timer
```

Please note that `systemctl --now` flag was only introduced in Systemd version 220. To check your version `systemctl --version`. For outdated versions please use `systemctl start <...>`.

# Using

Example for turning on outlet 4 immediately:
```console
$ apc_utility 192.168.1.2 apc p@assword "olOn 4"
```

Example for turning off outlet named "KVM" immediately:
```console
$ apc_utility 192.168.1.2 apc p@assword "olOff KVM"
```

Example for turning off outlet 6 with a 30 second delay:
```console
$ apc_utility 192.168.1.2 apc p@assword "olOffDelay 6 30"
```

Inspecting Systemd logs:
```console
$ journalctl -e -u apc_utility_auto_on
$ journalctl -e -u apc_utility_auto_off
```

Other device commands include:
```
Device Commands:
---------------------------------------------------------------------------
bkLowLoad   bkNearOver  bkOverLoad  bkReading   bkPeakCurr  bkRestrictn
devStartDly olAssignUsr olCancelCmd olDlyOff    olDlyOn     olDlyReboot
olGroups    olName      olOff       olOffDelay  olOn        olOnDelay   
olRbootTime olReboot    olStatus    olUnasgnUsr phLowLoad   phNearOver  
phOverLoad  phReading   phPeakCurr  phRestrictn prodInfo    userAdd     
userDelete  userList    userPasswd  energyWise  
```
