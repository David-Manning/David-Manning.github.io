+++
title = 'Fix Wifi Connection'
draft = false
toc = true
type = 'docs'
+++

If you use nmcli and you cannot change to a new Wifi setup, deleting the WiFi connection and creating a new one will usually allow you to connect.

First, delete the connection
```bash
nmcli connection delete "wifi_name"
```

Then, recreate the connection. 

This version will ask you for the password and give you the option to enter it without it being visible.
```bash
nmcli device wifi connect "wifi_name" -a
```

This version will ask you for the password with it typed into the command. The Wifi has to be visible.
```bash
nmcli device wifi connect "wifi_name" password "wifi_password"
```

This version will let you save a uuid, if you know it and are not in range of the WiFi connection. Note that it also autoconnects.
```bash
nmcli connection add \
	type wifi \
	con-name "wifi_name_visible" \
	ifname wlan0 \
	ssid "wifi_ssid" \
	wifi-sec.key-mgmt wpa-psk \
	wifi-sec.psk "wifi_password" \
	connection.uuid "wifi_uuid" \
	connection.autoconnect yes
```

* `con-name` is the name you see, this can be anything.
* `wifi_ssid` is the SSID, which is specific to the connection.
