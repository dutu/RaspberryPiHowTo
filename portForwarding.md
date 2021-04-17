# How to setup port forwarding on Raspberry Pi OS

## Install ufw
```
$ sudo apt-get install ufw
```

## NAT & Port forwarding

If you needed ufw to NAT the connections from the external interface to the internal, in the file `/etc/default/ufw` change the parameter `DEFAULT_FORWARD_POLICY`.
```
DEFAULT_FORWARD_POLICY="ACCEPT"
```

Configure /etc/ufw/sysctl.conf to allow ipv4 forwarding (the parameters is commented out by default). Uncomment for ipv6 if you want.
```
net.ipv4.ip_forward=1
```

Add NAT to ufw’s configuration. Add the following to `/etc/ufw/before.rules` just before the `filter` rules

```
# NAT table rules
*nat
:PREROUTING ACCEPT [0:0]
:POSTROUTING ACCEPT [0:0]

# Port Forwardings
-A PREROUTING -p tcp --dport 50543 -j DNAT --to-destination 192.168.1.1:443

# Forward traffic through eth0 - Change to match you out-interface
-A POSTROUTING -o eth0 -j MASQUERADE

# don't delete the 'COMMIT' line or these nat table rules won't
# be processed
COMMIT
```

Enable the changes by restarting ufw

```
$ sudo ufw disable && sudo ufw enable
```