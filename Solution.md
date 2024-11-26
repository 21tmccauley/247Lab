# Lab 8: DHCP Snooping and Switch Security - Solutions Guide

## Complete Configuration Steps

### Distribution Switch (ESWODD & ESWEVEN) Configuration

1. Enable DHCP Snooping Globally:
```
ESWODD# conf t
ESWODD(config)# ip dhcp snooping
ESWODD(config)# ip dhcp snooping vlan 10,20,30,40
ESWODD(config)# exit
```

2. Configure Trusted Ports (Uplink Ports):
```
ESWODD# conf t
ESWODD(config)# interface range f1/0 - 1
ESWODD(config-if-range)# ip dhcp snooping trust
ESWODD(config-if-range)# exit
```

3. Configure Rate Limiting on Untrusted Ports:
```
ESWODD# conf t
ESWODD(config)# interface range f1/2 - 24
ESWODD(config-if-range)# ip dhcp snooping limit rate 5
ESWODD(config-if-range)# exit
```

4. Configure Port Security:
```
ESWODD# conf t
ESWODD(config)# interface range f1/2 - 24
ESWODD(config-if-range)# switchport port-security
ESWODD(config-if-range)# switchport port-security maximum 2
ESWODD(config-if-range)# switchport port-security violation restrict
ESWODD(config-if-range)# switchport port-security mac-address sticky
ESWODD(config-if-range)# exit
```

5. Enable BPDU Guard:
```
ESWODD# conf t
ESWODD(config)# interface range f1/2 - 24
ESWODD(config-if-range)# spanning-tree bpduguard enable
ESWODD(config-if-range)# exit
```

### Access Switch Configuration
Repeat similar configuration on access switches, adjusting interface ranges as needed.

## Verification Steps

### DHCP Snooping Verification
```
show ip dhcp snooping
show ip dhcp snooping binding
show ip dhcp snooping statistics
```

Expected Output:
```
Switch# show ip dhcp snooping
Switch DHCP snooping is enabled
DHCP snooping is configured on following VLANs:
10,20,30,40
Insertion of option 82 is enabled
Interface                Trusted     Rate limit (pps)
-------------------     -------     ----------------
FastEthernet1/0         yes         unlimited
FastEthernet1/1         yes         unlimited
FastEthernet1/2         no          5
[...]
```

### Port Security Verification
```
show port-security
show port-security address
show port-security interface fastEthernet 1/2
```

Expected Output:
```
Switch# show port-security interface fastEthernet 1/2
Port Security              : Enabled
Port Status               : Secure-up
Violation Mode            : Restrict
Aging Time               : 0 mins
Aging Type               : Absolute
SecureStatic Address Aging : Disabled
Maximum MAC Addresses      : 2
Total MAC Addresses       : 1
Configured MAC Addresses   : 0
Sticky MAC Addresses      : 1
Last Source Address:Vlan   : 0000.0000.0001:1
Security Violation Count   : 0
```

## Common Issues and Solutions

### Problem 1: DHCP Clients Not Receiving Addresses
Symptoms:
- PCs cannot obtain IP addresses
- DHCP discover messages not forwarded

Solutions:
1. Verify DHCP snooping is enabled on correct VLANs:
```
show ip dhcp snooping
```
2. Check trusted port configuration:
```
show ip dhcp snooping | begin Interface
```
3. Verify DHCP server is connected to trusted port
4. Check rate limiting isn't too restrictive

### Problem 2: Port Security Violations
Symptoms:
- Ports shutting down
- Devices cannot connect

Solutions:
1. Check violation mode:
```
show port-security interface fastEthernet 1/2
```
2. Verify MAC address count:
```
show port-security address
```
3. Clear sticky addresses if needed:
```
clear port-security sticky interface fastEthernet 1/2
```

## Question Solutions

1. DHCP Snooping Protection:
DHCP snooping protects against rogue DHCP servers and man-in-the-middle attacks by creating a trusted boundary in the network. It only allows DHCP server responses (OFFER and ACK messages) from trusted ports while permitting client requests (DISCOVER and REQUEST messages) from untrusted ports. This prevents attackers from setting up unauthorized DHCP servers that could provide malicious default gateway addresses or exhaust the DHCP address pool.

2. Rate Limiting Purpose:
Rate limiting DHCP packets on untrusted interfaces prevents DHCP starvation attacks where an attacker floods the network with DHCP requests using spoofed MAC addresses. By limiting the number of DHCP packets per second, it ensures legitimate clients can still obtain addresses while preventing denial of service attacks.

3. Trusted vs Untrusted Ports:
Correctly identifying trusted and untrusted ports is crucial because it establishes the security boundary in your network. Trusted ports should only be ports connected to legitimate DHCP servers or uplinks to trusted network segments. Misconfiguring a port as trusted could create a security hole allowing rogue DHCP servers to operate.

4. Sticky Learning Advantages/Disadvantages:
Advantages:
- Automatically learns and locks MAC addresses
- Survives switch reboots
- Reduces manual configuration
Disadvantages:
- Can be inflexible when legitimate devices need to be replaced
- Requires manual clearing for hardware upgrades
- May require more management overhead

5. BPDU Guard Benefits:
BPDU guard enhances network security by preventing unauthorized switches from being connected to access ports. This prevents topology changes that could disrupt the network and potential man-in-the-middle attacks through rogue switches. When a BPDU is received on a BPDU guard enabled port, the port is immediately disabled.
