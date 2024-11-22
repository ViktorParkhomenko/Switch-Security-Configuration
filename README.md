# Switch Security Configuration

Project Overview:
In this project, I will be enhancing the security on two access layer switches (SW-1 and SW-2) in a partially configured network. The goal is to implement a series of security measures to ensure the integrity and stability of the network while preventing unauthorized access and attacks.

This project will cover the following security tasks:

Securing trunk links between switches
Shutting down unused switchports
Implementing port security to control the number of MAC addresses learned
Enabling DHCP Snooping to protect against DHCP spoofing attacks
Configuring PortFast and BPDU Guard to prevent network loops and ensure quick network convergence.
By completing these steps, I will secure the switches' ports, protect against various attack vectors, and ensure optimal network performance.

Objectives:

Part 1: Create a Secure Trunk

Configure trunk links between the two access switches.
Secure the trunk by disabling DTP negotiation and setting VLAN 100 as the native VLAN.

Part 2: Secure Unused Switchports

Shut down unused switchports on SW-1.
Move unused switchports to a "BlackHole" VLAN (VLAN 999) to isolate them.

Part 3: Implement Port Security

Configure port security to limit the number of MAC addresses learned.
Statistically configure the MAC address on certain ports.
Set up port security violation actions.

Part 4: Enable DHCP Snooping

Configure DHCP Snooping to protect against rogue DHCP servers.
Limit DHCP packets on untrusted ports.

Part 5: Configure Rapid PVST PortFast and BPDU Guard

Enable PortFast on access ports to speed up the transition of the port to the forwarding state.
Enable BPDU Guard to protect the network from potential network loops.

![{054A7638-FF61-424F-84C0-4156168CAF03}](https://github.com/user-attachments/assets/7e559e76-cb24-4bb6-9642-5816285e4c9c)


Step-by-Step Configuration:

Step 1: Create a Secure Trunk
Goal: Secure the trunk link between the two access layer switches (SW-1 and SW-2).

Connect the G0/2 Ports: Connect the G0/2 ports of both switches using a straight-through Ethernet cable.

Configure Trunk Ports:
On SW-1, enter configuration mode and set port G0/1 and G0/2 to static trunks:

SW-1# configure terminal

SW-1(config)# interface range g0/1 - 2

SW-1(config-if-range)# switchport mode trunk

SW-1(config-if-range)# switchport nonegotiate

SW-1(config-if-range)# exit

On SW-2, configure G0/1 and G0/2 as static trunks as well:

SW-2# configure terminal

SW-2(config)# interface range g0/1 - 2

SW-2(config-if-range)# switchport mode trunk

SW-2(config-if-range)# switchport nonegotiate

SW-2(config-if-range)# exit

Create VLAN 100 on both switches and set it as the native VLAN:

SW-1(config)# vlan 100
SW-1(config-vlan)# name Native
SW-1(config-vlan)# exit
SW-1(config)# interface range g0/1 - 2
SW-1(config-if-range)# switchport trunk native vlan 100
SW-1(config-if-range)# exit
Did the same on SW-2.

![{8271D13E-3115-42AD-9641-0E8A849F4299}](https://github.com/user-attachments/assets/1fef15ad-7713-4624-a20d-532d319b7725)

![{820C101F-04CB-430B-9FC3-2E7A590384A8}](https://github.com/user-attachments/assets/e5a4352e-1d43-48ed-ad56-17d797f53a40)


Step 2: Secure Unused Switchports
Goal: Prevent unauthorized devices from accessing the network by shutting down unused ports and assigning them to a BlackHole VLAN.

Shutdown Unused Ports on SW-1:

SW-1(config)# interface range fa0/3 - 24

SW-1(config-if-range)# shutdown

SW-1(config-if-range)# exit

Create VLAN 999 (BlackHole VLAN):

SW-1(config)# vlan 999

SW-1(config-vlan)# name BlackHole

SW-1(config-vlan)# exit

Move Unused Ports to BlackHole VLAN:

SW-1(config)# interface range fa0/3 - 24

SW-1(config-if-range)# switchport access vlan 999

SW-1(config-if-range)# exit

![{1FA4AD0C-E265-4E4F-95EB-BA987A46384E}](https://github.com/user-attachments/assets/6cd978f2-19c2-497e-bf63-b289adb008d6)

Step 3: Implement Port Security
Goal: Limit the number of MAC addresses that can be learned on each access port.

Enable Port Security on Active Ports on SW-1:

SW-1(config)# interface range fa0/1 - 2

SW-1(config-if-range)# switchport port-security

SW-1(config-if-range)# switchport port-security maximum 4

SW-1(config-if-range)# switchport port-security violation protect

SW-1(config-if-range)# switchport port-security mac-address sticky

SW-1(config-if-range)# exit

Configure Static MAC Address on F0/1 (for a known device):

SW-1(config)# interface fa0/1

SW-1(config-if)# switchport port-security mac-address 0010.11E8.3CBB

SW-1(config-if)# exit

![{7934505B-188F-418D-8768-CD681B6971AB}](https://github.com/user-attachments/assets/4116619d-16dc-4144-8be1-970a389c047f)

Step 4: Enable DHCP Snooping
Goal: Protect the network from rogue DHCP servers.

Configure Trunk Ports as Trusted:

bash
Copy code
SW-1(config)# interface range g0/1 - 2
SW-1(config-if-range)# ip dhcp snooping trust
SW-1(config-if-range)# exit
Limit DHCP Packets on Untrusted Ports:

bash
Copy code
SW-1(config)# interface range fa0/1 - 2
SW-1(config-if-range)# ip dhcp snooping limit rate 5
SW-1(config-if-range)# exit
Enable DHCP Snooping on SW-2 for VLANs 10, 20, and 99:

bash
Copy code
SW-2(config)# ip dhcp snooping
SW-2(config)# ip dhcp snooping vlan 10,20,99
SW-2(config)# exit
Step 5: Configure PortFast and BPDU Guard
Goal: Ensure fast convergence and prevent loops.

Enable PortFast and BPDU Guard on SW-1:

bash
Copy code
SW-1(config)# interface range fa0/1 - 2
SW-1(config-if-range)# spanning-tree portfast
SW-1(config-if-range)# spanning-tree bpduguard enable
SW-1(config-if-range)# exit
Enable PortFast by Default on SW-2:

bash
Copy code
SW-2(config)# spanning-tree portfast default
SW-2(config)# exit
Conclusion:
This project successfully implemented a range of security measures on two Cisco access switches (SW-1 and SW-2). The following security features were configured:

Trunk security: Secured trunk links between switches by disabling DTP negotiation and assigning a native VLAN.
Unused ports security: Secured unused ports by shutting them down and moving them to a BlackHole VLAN.
Port security: Implemented port security with a limit on MAC addresses per port, and configured violation modes.
DHCP Snooping: Protected against rogue DHCP servers by enabling DHCP Snooping and limiting DHCP packets on untrusted ports.
PortFast and BPDU Guard: Ensured fast port transitions and protection against potential network loops.
By implementing these configurations, we’ve enhanced the network's security and ensured a more stable and reliable environment, effectively preventing common attacks such as DHCP spoofing and unauthorized access via unused switch ports.












???????????????????????????????????????????????????????????????????
Objectives
Part 1: Create a Secure Trunk

Part 2: Secure Unused Switchports

Part 3: Implement Port Security

Part 4: Enable DHCP Snooping

Part 5: Configure Rapid PVST PortFast and BPDU Guard

Background
You are enhancing security on two access switches in a partially configured network. You will implement the range of security measures that were covered in this module according to the requirements below. Note that routing has been configured on this network, so connectivity between hosts on different VLANs should function when completed.

Instructions
Step 1: Create a Secure Trunk.
a.     Connect the G0/2 ports of the two access layer switches.

b.     Configure ports G0/1 and G0/2 as static trunks on both switches.

c.     Disable DTP negotiation on both sides of the link.

d.     Create VLAN 100 and give it the name Native on both switches.

e.     Configure all trunk ports on both switches to use VLAN 100 as the native VLAN.

Step 2: Secure Unused Switchports.
a.     Shutdown all unused switch ports on SW-1.

b.     On SW-1, create a VLAN 999 and name it BlackHole. The configured name must match the requirement exactly.

c.     Move all unused switch ports to the BlackHole VLAN.

Step 3: Implement Port Security.
a.     Activate port security on all the active access ports on switch SW-1.

b.     Configure the active ports to allow a maximum of 4 MAC addresses to be learned on the ports.

c.     For ports F0/1 on SW-1, statically configure the MAC address of the PC using port security.

d.     Configure each active access port so that it will automatically add the MAC addresses learned on the port to the running configuration.

e.     Configure the port security violation mode to drop packets from MAC addresses that exceed the maximum, generate a Syslog entry, but not disable the ports.

Step 4: Configure DHCP Snooping.
a.     Configure the trunk ports on SW-1 as trusted ports.

b.     Limit the untrusted ports on SW-1 to five DHCP packets per second.

c.     On SW-2, enable DHCP snooping globally and for VLANs 10, 20 and 99.

Note: The DHCP snooping configuration may not score properly in Packet Tracer.

Step 5: Configure PortFast, and BPDU Guard.
a.     Enable PortFast on all the access ports that are in use on SW-1.

b.     Enable BPDU Guard on all the access ports that are in use on SW-1.

c.     Configure SW-2 so that all access ports will use PortFast by default.
