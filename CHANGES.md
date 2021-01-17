# Changelog

## 1.2.2 (...)
- deal with unexpected glob when looping over files in `/etc/openvpn/server` 
  which could cause the `openvpn-server@\x2a.service` to be enabled when no 
  config files exist in `/etc/openvpn/server`
  
## 1.2.1 (2020-12-08)
- simplify reset scripts
- warn locally added users will be deleted when doing reset
- fix scripts on Fedora >= 33

## 1.2.0 (2020-08-02)
- add `vpn-main-verify-config` to verify the VPN configuration files
- support all PHP versions on Debian (derivatives)
- make `apt-get dist-upgrade` non-interactive (does not ask questions)

## 1.1.0 (2020-05-06)
- introduce `-y` flag for `vpn-maint-update-controller`

## 1.0.0 (2020-05-05)
- initial release
