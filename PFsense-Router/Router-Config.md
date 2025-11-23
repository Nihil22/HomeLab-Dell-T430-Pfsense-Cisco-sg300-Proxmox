# pfSense Router & Firewall (Lenovo M720q Tiny)

This section documents my pfSense installation running on a Lenovo M720q Tiny.  
pfSense serves as the primary router, firewall, VLAN manager, and DHCP/DNS controller for the entire homelab.

---

## Hardware Overview

**Device:** Lenovo ThinkCentre M720q Tiny  
- **CPU:** Intel Core i5-8400T  
- **RAM:** 16GB  
- **Networking:** Intel i350-T4 Quad-port NIC   
- **Storage:** 128 gb SSD  
- **Role:** Main router, firewall, VLAN gateway, DHCP server, VPN endpoint
- **Additions** Removed hdmi adaptor to install Intel i350 quadport NIC with PCI riser.

---

## Network Interfaces

### **WAN**
- Connected to ISP modem  
- DHCP from ISP  
- NAT out to all internal network including VLANs  

### **LAN (Parent Interface)**
- Connected to Cisco SG300  
- Used as the parent for all VLANs  
- Carries VLAN tags to the switch  

---

## VLAN Configuration

### **Current VLANs**
| VLAN | Name      | Subnet            | Purpose                         |
|------|-----------|-------------------|----------------------------------|
| 1    | Default   | 192.168.1.0/24    | Management / default             |
| 10   | Server   | 192.168.10.0/24   | Proxmox, Pi-hole, Navidrome, NAS |
| 20   | Lab       | 192.168.20.x/24   | Windows/Kali lab machines        |
| 99   | VPN | 192.168.99.0/24   | VPN connection   |

> VLAN 20 is reserved for cyber practice labs and offensive tooling.

---

## DHCP Configuration

pfSense provides DHCP for all VLANs:

### **VLAN 10 (Server)**
- Range: `192.168.10.0 -192.168.10.200`  
- Devices: Proxmox VMs, Pi-hole, Navidrome, OMV  

### **VLAN 20 (Lab)**
- Range: `192.168.20.x`  
- Devices: Windows 10, Kali, lab machines  

### **VLAN 99 (VPN)**
- Range: `192.168.99.x`  
- Devices: Iphone (future devices will require Certificates)  

### *
- Static mappings used for all services (Pi-hole, Proxmox, OMV)  
- DNS server for all VLANs points to Pi-hole first, then pfSense as backup  

---

## Firewall Rules

### **General Philosophy**
- Server VLAN (10) should NOT be reachable from Lab VLANs  
- Lab VLANs (99) should be isolated from LAN and only able to acess Server VLAN  
- DNS permitted through Pi-hole  
- Internet allowed for all VLANs unless testing isolation  

### **Sample Rulesets**

#### **VLAN 10 (Server) Rules**
![image alt]() 

#### **VLAN 20 (Lab) Rules**
![image alt]()  

#### **VLAN 99 (VPN) Rules**
![image alt]()

---

## DNS + Pi-hole Integration

### **Setup**
- pfSense uses Pi-hole as the primary DNS resolver  
- Pi-hole forwards upstream to pfSense or external resolvers  
- IPv6 enabled to prevent resolution failures  
- DHCP handled by pfSense (Pi-hole **not** running DHCP mode)

### *
- Allows for Centralalized DNS filtering and domain adblocking  


---

## VPN

### **OpenVPN **
- Single VPN endpoint  
- Only exposes OpenVPN port (1195)  
- Allows access to server VLAN and services:
  - Server and it's services   

### **Security Approach**
- Two-Factor Authentication
- Certificate & Password based authentication  
- IP whitelist to limit connection sources  


