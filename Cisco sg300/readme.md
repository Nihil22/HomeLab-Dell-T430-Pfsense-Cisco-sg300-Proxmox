# Cisco SG300 Managed Switch

This section documents the Cisco SG300 used in my homelab for VLAN segmentation, trunking, and Layer 2 management.  
It acts as the central switch connecting pfSense, Proxmox, NAS, and lab devices.

---

## 🖥️ Hardware Overview

**Switch:** Cisco SG300-52 (Managed L3 Switch)  
- 52 Gigabit Ethernet ports  
- Fan-cooled (louder than typical home switches)  
- Full VLAN support (802.1Q)  
- Trunk/access mode configuration  
- Web GUI + CLI available  

**Role:**  
Primary switch handling all VLANs, Physical connections, interconnections, and Proxmox trunk porting.

---

## 🔀 VLAN Configuration

### **VLANs Used**
| VLAN | Name      | Purpose                    |
|------|-----------|----------------------------|
| 1    | Default   | Management / LAN       |
| 10   | Server   | Proxmox, Pi-hole, Navidrome, NAS |
| 20   | Lab       | Cyber LAB environemt |
| 99   | VPN       | OpenVPN connection |
---

## 🔌 Port Configuration

### **Proxmox Port (Trunk)**
This port carries all VLANs to the Proxmox host.

**Mode:** `Trunk`  
**Tagged VLANs:** 10, 20  
**Untagged VLAN:** 1  

Allows Proxmox to use vmbr0 Bridge only, and each VM/LXC gets its VLAN tag inside Proxmox.

---

### **pfSense Port (Uplink)**
Connects directly to pfSense LAN interface.

**Mode:** `Access`  
**Untagged VLAN:** 1 (or LAN)  
**Tagged VLANs:** None  

pfSense handles VLAN interfaces internally and passes traffic to the SG300.


---

## 📊 VLAN Trunk Logic (Simple Overview)
Switch handles tagging:

- SG300 trunk → Proxmox (`vmbr0`)  
- VM network device → applies VLAN tag  
- pfSense receives tagged traffic and routes accordingly 

