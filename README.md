# OpenBSD TCP Load Balancer

### Objective
+ Create a simple TCP Load Balancer that filter TCP packet when is being define inside the PF (Packet Filter) config file

## ![OpenBSD-LoadBalancer](https://user-images.githubusercontent.com/60979170/102245072-17814b80-3f38-11eb-8c6f-1bc902d74ad1.jpg)

## Configuration needed for the setup:
### 3 OpenBSD server with a static IP 
+ Load Balancer = 10.8.8.3
+ Server 1 = 192.168.0.3
+ Server 2 = 192.168.1.3

### Load Balancer Configuration
