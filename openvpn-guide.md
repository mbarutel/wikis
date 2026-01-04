# ExpressVPN + OpenVPN on Artix (runit)

## File Locations

| Purpose | Path |
|---------|------|
| OpenVPN configs | `/etc/openvpn/client/` |
| Credentials file | `/etc/openvpn/client/auth.txt` |
| runit service | `/etc/runit/sv/openvpn-expressvpn/` |
| Service logs | `/var/log/openvpn-expressvpn/` |

---

## Adding a New Server Config

1. Download `.ovpn` file from [ExpressVPN Manual Config](https://www.expressvpn.com/setup#manual)

2. Move to config directory:
   ```bash
   sudo mv ~/Downloads/expressvpn-*.ovpn /etc/openvpn/client/
   ```

3. Edit the file to use stored credentials:
   ```bash
   sudo nano /etc/openvpn/client/expressvpn-newserver.ovpn
   ```
   Change `auth-user-pass` to:
   ```
   auth-user-pass /etc/openvpn/client/auth.txt
   ```

4. Test it:
   ```bash
   sudo openvpn --config /etc/openvpn/client/expressvpn-newserver.ovpn
   ```

---

## Switching Servers

Edit the runit run script:
```bash
sudo nano /etc/runit/sv/openvpn-expressvpn/run
```

Change the config path:
```bash
#!/bin/sh
exec 2>&1
exec openvpn --config /etc/openvpn/client/expressvpn-newserver.ovpn --verb 3
```

Restart the service:
```bash
sudo sv restart openvpn-expressvpn
```

---

## Managing the Service

```bash
# Start
sudo sv start openvpn-expressvpn

# Stop
sudo sv stop openvpn-expressvpn

# Restart
sudo sv restart openvpn-expressvpn

# Check status
sudo sv status openvpn-expressvpn

# View logs
sudo tail -f /var/log/openvpn-expressvpn/current
```

---

## Verify VPN is Working

```bash
# Check tunnel interface exists
ip a | grep tun

# Check routing
ip route | grep tun

# Verify IP (use -4 to force IPv4)
curl -4 ifconfig.me
```

---

## Troubleshooting

### TUN module not loaded
```bash
sudo modprobe tun
```

If module not found, reboot — kernel/modules mismatch:
```bash
uname -r           # running kernel
ls /lib/modules/   # installed modules
sudo reboot
```

### IPv6 leak (real IP showing)
IPv6 bypasses the tunnel. Disable it:
```bash
sudo sysctl -w net.ipv6.conf.all.disable_ipv6=1
sudo sysctl -w net.ipv6.conf.default.disable_ipv6=1
```

Or permanently in `/etc/sysctl.d/99-disable-ipv6.conf`:
```
net.ipv6.conf.all.disable_ipv6=1
net.ipv6.conf.default.disable_ipv6=1
```

### DNS leak
Add to your `.ovpn` file:
```
script-security 2
up /etc/openvpn/update-resolv-conf
down /etc/openvpn/update-resolv-conf
```

Test at: https://dnsleaktest.com

---

## runit Service Files

### /etc/runit/sv/openvpn-expressvpn/run
```bash
#!/bin/sh
exec 2>&1
sysctl -w net.ipv6.conf.all.disable_ipv6=1
exec openvpn --config /etc/openvpn/client/expressvpn-brisbane.ovpn --verb 3
```

### /etc/runit/sv/openvpn-expressvpn/finish
```bash
#!/bin/sh
sysctl -w net.ipv6.conf.all.disable_ipv6=0
```

### /etc/runit/sv/openvpn-expressvpn/log/run
```bash
#!/bin/sh
exec svlogd -tt /var/log/openvpn-expressvpn
```

---

## Updating Credentials

If your ExpressVPN credentials change:
```bash
sudo nano /etc/openvpn/client/auth.txt
```

Format (two lines):
```
username
password
```

Then restart:
```bash
sudo sv restart openvpn-expressvpn
```
