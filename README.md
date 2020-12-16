# OpenBSD TCP Load Balancer

### Objective
Create a simple TCP Load Balancer that filter TCP packet when is being define inside the PF (Packet Filter) config file

## ![OpenBSD-LoadBalancer](https://user-images.githubusercontent.com/60979170/102245072-17814b80-3f38-11eb-8c6f-1bc902d74ad1.jpg)

## Configuration needed for the setup:
### 3 OpenBSD server with a static IP 
+ Load Balancer = 10.8.8.3
+ Server 1 = 192.168.0.3
+ Server 2 = 192.168.1.3

### Load Balancer Configuration Explained

Firstly, we need to define the interfaces that is being use inside the topology. We will need 3 network interface shown:
```markdown
int="em0"
ext="em1"
ext2="em2"
cli_net="10.8.8.3"
server1="192.168.0.3"
server2="192.168.1.3"
```
Next, here comes the part when the load balancer does its job by determine the port to be use and a packet to be filter
```markdown
set skip on lo0
pass out on $ext from $int:network to !$int:network nat-to ($ext)
pass out on $ext2 from $int:network to !$int:network nat-to ($ext2)
pass in on $int proto tcp from any to port 5000 tos 0x20 rdr-to $server1 port 5000
pass in on $int proto tcp from any to port 5500 scrub (min-ttl 100) rdr-t0 $server1 port 5500
```
For security measures, I added pf rules which blocks any incoming ftp packet inside our topology.
```markdown
# Lists of known FTPD attackers
table <ftp-attacks> persist file "/etc/pf.ftp.block.list"
# Blocks all incoming connections from attackers on FTPD
block in quick on $ext_if from <ftp-attacks>
# Allow FTP with bruteforce protection
pass in quick on $ext_if inet proto tcp from any to ($ext_if) port 21 keep state (max-src-conn-rate 5/40, overload <ftp-attacks> flush global) #Block ftp connection more tha 5 times in 40 sec
# Append the config to /etc/rc.shutdown
echo '/sbin/pfctl -t ftp-attacks -T show > /etc/pf.ftp.block.list' >> /etc/rc.shutdown
```
## References
https://man.openbsd.org/pf.conf

