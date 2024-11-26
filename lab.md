# Lab 8: DHCP Snooping and Switch Security

In this lab, you will enhance the security of your network by implementing DHCP snooping and additional security measures. Building on the dynamic routing setup from Lab 7, you'll learn how to protect against DHCP-based attacks and implement various switch hardening techniques.

## Lab Outline

### Lab Objective
The objective of this lab is to secure your network against common Layer 2 attacks by implementing DHCP snooping and additional security features. You will learn how to identify trusted DHCP sources, configure DHCP snooping, and implement best practices for switch security.

### Associated Learning Outcomes
- **Network Security:** Identify and describe common network vulnerabilities and recommend appropriate mitigations in line with security policy and best practices.
- **Computer Networks:** Design and implement networks suitable for the flow of information in line with organizational needs.
- **Diagnose and Solve Network Problems:** Troubleshoot and diagnose common network problems and identify solutions.

**Estimated Time:** 2 hours, you have 1 week from lab kickoff until the due date

## Instructions

### Part 1: Understanding DHCP Snooping

DHCP snooping is a Layer 2 security feature that acts like a firewall between untrusted DHCP servers and clients. It builds and maintains a DHCP snooping binding database that is used to validate subsequent DHCP client requests.

1. Enable DHCP snooping globally on all switches:
```
Switch(config)# ip dhcp snooping
Switch(config)# ip dhcp snooping vlan 10,20,30,40
```

2. Configure trusted interfaces (ports connected to legitimate DHCP servers or uplinks):
```
Switch(config)# interface range f1/0 - 1
Switch(config-if-range)# ip dhcp snooping trust
```

3. Enable DHCP snooping rate limiting on untrusted interfaces:
```
Switch(config)# interface range f1/2 - 24
Switch(config-if-range)# ip dhcp snooping limit rate 5
```

### Part 2: Additional Security Measures

#### Configure Port Security

1. Enable port security on access ports:
```
Switch(config)# interface range f1/2 - 24
Switch(config-if-range)# switchport port-security
Switch(config-if-range)# switchport port-security maximum 2
Switch(config-if-range)# switchport port-security violation restrict
Switch(config-if-range)# switchport port-security mac-address sticky
```

#### Setup BPDU Guard

1. Enable BPDU guard on all access ports:
```
Switch(config)# interface range f1/2 - 24
Switch(config-if-range)# spanning-tree bpduguard enable
```

#### Configure Dynamic ARP Inspection

1. Enable DAI on VLANs:
```
Switch(config)# ip arp inspection vlan 10,20,30,40
```

2. Configure trusted interfaces for DAI:
```
Switch(config)# interface range f1/0 - 1
Switch(config-if-range)# ip arp inspection trust
```

### Part 3: Testing and Verification

1. Verify DHCP snooping configuration:
```
Switch# show ip dhcp snooping
Switch# show ip dhcp snooping binding
```

2. Verify port security:
```
Switch# show port-security
Switch# show port-security address
```

3. Verify DAI configuration:
```
Switch# show ip arp inspection
Switch# show ip arp inspection interfaces
```

## Deliverables

### Screenshots (35 points)
1. Screenshot showing the DHCP snooping configuration on each switch (show ip dhcp snooping)
2. Screenshot showing the port security configuration and current MAC addresses (show port-security address)
3. Screenshot showing the DAI configuration (show ip arp inspection)
4. Screenshot showing successful DHCP address assignment for PC1 and PC5
5. Screenshot showing blocked DHCP offers from unauthorized sources (console messages)
6. Screenshot of your running configuration focusing on security features
7. Screenshot showing port security violations (if any occurred during testing)

### Questions (15 points)
1. Explain how DHCP snooping protects against rogue DHCP servers and man-in-the-middle attacks.
2. What is the purpose of rate limiting DHCP packets on untrusted interfaces?
3. How does Dynamic ARP Inspection (DAI) protect against ARP spoofing attacks?
4. What are the advantages and disadvantages of using "sticky" learning for port security?
5. Why is it important to enable BPDU guard on access ports?

## Troubleshooting and Helpful Tips

### General Commands
- Show DHCP snooping status: `show ip dhcp snooping`
- Show DHCP bindings: `show ip dhcp snooping binding`
- Show port security status: `show port-security`
- Show port security addresses: `show port-security address`
- Show DAI status: `show ip arp inspection`
- Show interface security settings: `show running-config interface f1/0`

### Common Issues
1. DHCP clients not receiving addresses:
   - Verify DHCP snooping is enabled on the correct VLANs
   - Check trusted interface configuration
   - Verify rate limiting isn't too restrictive

2. Port security violations:
   - Check the maximum number of allowed MAC addresses
   - Verify violation mode settings
   - Review sticky MAC address configuration

3. DAI blocking legitimate traffic:
   - Verify trusted interface configuration
   - Check DHCP snooping binding database
   - Review rate limiting settings

Remember to save your configurations using `copy running-config startup-config` after making changes!
