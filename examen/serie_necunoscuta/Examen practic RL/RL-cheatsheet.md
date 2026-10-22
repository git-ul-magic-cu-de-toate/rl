# RL Cheatsheet

Useful calculators:

[IP calculator](https://jodies.de/ipcalc)

[VLSM calculator](http://www.vlsmcalc.com/)

[Cheatsheet official](https://ocw.cs.pub.ro/courses/_media/rl/rl_cheatsheet.pdf)

## Linux cheatsheet

**DON'T FORGET TO ACTIVATE IPv4 FORWARDING**

**`sudo sysctl -w net.ipv4.ip_forward=1`**

**`sudo sysctl -p`**

---
### IP Adress - set and see interface configuration

* `ip a` - show current interface configuration
* `sudo ip address add <IP address>/<Mask> dev <interface>` - set IP address on interface
* `sudo ip address show dev <interface>` - show IP adress on interface
* `sudo ip address flush dev <interface>` - flush IP addresses on interface

---
### IP Link - config 2nd layer interfaces

* `sudo ip link set dev <interface> up` - set interface UP state
* `sudo ip link set dev <interface> down` - set interface DOWN state

---
### IP Neighbour - show ARP table

* `ip neighbour show` - show ARP table
* `ip neighbour add <IP address> lladdr <MAC address> dev <interface>` - add ARP entry

---
### IP Tables - set routing rules and filters

For port ranges use `<first_port>:<last_port>`.

### Available standard chains:

* **INPUT** - incoming packets with destination IP as current host
* **OUTPUT** - outgoing packets with source IP as current host
* **FORWARD** - packets that need routing outside of host

### Available standard actions:

* **REJECT** - reject packets
* **ACCEPT** - accept packets
* **DROP** - no action, just drop packets

### Available NAT chains:

* **PREROUTING** - packets that are modified before routing process
* **POSTROUTING** - packets that are modified after routing process (usually used as backup rule if destination not reachable in current network)
* **INPUT** - incoming packets with destination IP as current host
* **OUTPUT** - outgoing packets with source IP as current host

### Available NAT actions:

* **SNAT** - for packets that leave the local network, so the source IP address will be changed
* **DNAT** - for packets that enter the local network, so the destination IP address will be changed (usually for Port Forwarding)
* **MASQUERADE** - make one single IP address for local network as public for Internet

---
### Standard

* `iptables -A <chain> -s <Src. IP Address> -d <Dest. IP Address> -p <protocol> --dport <port/service> --sport <port/service> -j <Action>` - apply IP Tables rule, some parts of the commands can be ignored
* `iptables -L <chain> -v -n` - show IP Tables rules for chain
* `iptables -nvL` - show all IP Tables rules
* `iptables -A INPUT -p icmp -j ACCEPT` - allow all icmp traffic
* `iptables -A INPUT -p tcp -dport 23 -j ACCEPT` - allow all telnet traffic
* `iptables -A INPUT -j REJECT` - block all other traffic (must be last)

```bash
# for stateful conexions (can send to green from blue, but not from green to blue)
# for telnet
iptables -A INPUT -p tcp -s blue --sport 23 -m state --state ESTABLISHED -j ACCEPT
iptables -A OUTPUT -p tcp -d green --dport 23 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -A INPUT -p tcp -s green --dport 23 -j DROP
```

---
### NAT

* `iptables -t nat -A POSTROUTING -o <interface> -j MASQUERADE` - activate NAT for local network on interface (aka enable network conectivity)
* `iptables -t nat -A PREROUTING -p tcp --dport <local port> -j DNAT --to-destination <Dest. IP Address>:<Dest. port>` - port forward local port to destination IP address and destination port
* `iptables -t nat -A PREROUTING -p tcp --dport 12345 -j DNAT --to-destination blue:22` - forward 12345 to ssh port of blue

---
### IN CASE OF EMERGENCY, FLUSH EVERYTHING AND RUN (this deletes all the iptable rules)

* `iptables -P INPUT ACCEPT`
* `iptables -P FORWARD ACCEPT`
* `iptables -P OUTPUT ACCEPT`
* `iptables -t nat -F`
* `iptables -t mangle -F`
* `iptables -F`
* `iptables -X`

---
### SSH commands

* `ssh <user>@<host IP/name>` - SSH connection to host
* `ssh-copy-id <user>@<host IP/name>` - SSH copy public key to host
* `scp <user>@<host IP/name:<path to file> <path to local file>` - SSH copy a file from host

---
### nmap commands

* `sudo nmap -O <ip_or_hostname>` - check if ipaddress is up and guess the host OS
* `sudo nmap -sV <ip_or_hostname>` - check services running on the machine
* `sudo nmap -sP <ip_or_hostname>` - port scans the machine
* `sudo nmap -sn <ip_or_hostname>` - just ping scan, no ports
* `sudo nmap -p port1,port2 -p portX-portY -p T:21-25 address_or_addresses` - specific port, also works with ranges (T for TCP, U for UDP)
* `sudo nmap -O -sV ocw.cs.pub.ro` - example, work with chaining more commands

---
### tcpdump commands

* `sudo tcpdump -i eth0` - capture on a specific interface
* `sudo tcpdump -A tcp` - all tcp traffic
* `sudo tcpdump -c <packet-number>` - captures only `<packet-number>` packets
* `sudo tcpdump -i eth0 -c 10 dst port 80` - 10 packets from eth0 on destination port 80
* `sudo tcpdump host ocw.cs.pub.ro and tcp` - only level 4-7 packets from ocw.cs.pub.ro (use `curl ocw.cs.pub.ro/course` in another terminal)

---
### Useful port
* `cat /etc/services` - see all ports on your device

```bash
ftp data        20
ftp command     21
ssh             22
telnet          23
smtp            25
dns             53
tftp            69 # (most important one)
http            80
https           443
```

---
### Other important commands

* `sshpass` - pass SSH password to scp
* `wget` - supports recursive access, supports user and password, can access FTP
* `whois` - checks a network address for details
* `host` - DNS check for network addresses
* `curl` - can access FTP, supports user and password, supports upload file
* `mail` - send mails
* `s-nail` - send mails with source and custom address
* `tcpdump` - capture network traffic on interface, can set destination/source etc.
* `nmap` - can scan for open ports/services, can scan for OS and other features
* `netstat` - scan the ports on your device

## PacketTracer

## Modes
### Privileged EXEC mode: 
* `enable` or `en`
---

### Config Mode:
* `config terminal` or `conf t`
---

### Interface config mode:
* `interface <type>/<number>` (eg. fa0/0) or `int fa0/0`
---

### Save the config:
* `exit`
* `write memory` (or just 'wr')
---

## Helpful commands
```bash
?                           displays all the possible commands in the current mode
tab                         autocompletes the rest of the command do
command                     executes the command in the Privileged Exec mode, regardless of the current shell mode
shortcut                    you can execute a command by typing just the first letters of it and press enter
click Fast Forward Time     increases the time of booting the devices
exit                        exits the current mode
end                         exits the current mode and enters the Privileged EXEC mode
CTRL+SHIFT+6                interrupts the execution of the current command
no <command>               cancels the command/ deletes the configuration of that command 
```
---

## Show commands
### View the router's/switch's entire active configuration:
* `show running-config`
---

### View the available interfaces and their brief parameters (IP, active, etc.):
* `show ip interface brief` 
---

### View the routing table:
* `show ip route`
---

### View the CAM table:
* `show mac-address-table`
---

### View STP parameters:
* `show spanning-tree`
---

### View VLAN parameters:
* `show VLAN brief`  
---

### View VLAN’s brief parameters on interfaces:
* `show interface VLAN brief`
---

## Config a static entry for a PC on the interface <type>/<number>:
* `mac address-table static <pc_mac_address> vlan <vlan_id> interface <type>/<number>`
---

## Show the CAM Table:
* `show mac address-table`
---

## Clear the CAM Table:
* `clear mac address-table`
(if you want to clear only the dynamic/static entries, add <dynamic/static>)
---

## Interface commands
### Enable the interfaces (bring it up): 
* `no shutdwon`
---

### Configure an address & mask for an interface:
```
configure terminal
interface <type>/<number>
no shutdown
ip address <address> <mask>
```
---

### Connect to the switch's IP:
``Click on the station -> Desktop -> Command prompt``
* `telnet <switch_IP>`
---

## VLAN commands
### Create a new VLAN:
* `vlan <vlan_id>`
* `name <vlan_name>`
---

### Create a VLAN interface and config an IP address: 
* `interface vlan <vlan_id>`
* `ip address <vlan_ip_addr> <vlan_subnet_mask>`
* `no shutdown`
---

### Deactivate and delete an IP config for a VLAN interface:
* `interface vlan <vlan_id>`
* `shutdown`
* `no ip address`
---

### Configure Access Mode:
* `interface <type>/<number>` 
* `switchport mode access / sw mo acc`
* `switchport access vlan <vlan_id>`
---

### Configure Trunk Mode:
* `interface <type>/<number>`
* `switchport mode trunk / sw mo tr`
* `swithcport trunk allowed vlan <vlan-id>/all`  
---

### Native VLAN:
* `interface <type>/<number>`
* `switchport trunk native vlan <vlan_id>`
---

## STP
### SW priority config:
* `spanning-tree vlan <vlan_number> priority <value>`
---

## Routing
### Activate routing:
* `ip routing`
---

### Default gateway:
* `ip default-gateway <address>`
---

### Static route:
* `ip route <destination network* `<destination_network_mask> <next_hop>`
---

### Default route (all the packets with unknown destinations will be sent through that next-hop):
* `ip route 0.0.0.0 0.0.0.0 <next_hop>`
---

### Inter-VLAN Routing:
* `interface <type>/<number>`
* `encapsulation dot1Q <vlan_id>`
* `ip address <vlan_ip_addr> <vlan_subnet_mask>`
---