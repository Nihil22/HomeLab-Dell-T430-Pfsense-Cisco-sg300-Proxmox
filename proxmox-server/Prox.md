This section documents my primary virtualization stack running on a Dell PowerEdge T430. The server hosts all core services in my homelab and integrates with pfSense (routing/firewall) and my Cisco SG300 switch (VLAN segmentation).
----------------------------------------------------------------------------------------------------------------------------------------------------

## Hardware
**Dell PowerEdge T430**
- CPU: Intel Xeon(r) E5-2603  
- RAM: 32 GB  
- Storage:
  - 2x 1 tb ssd in RAID 1 configuration
  - 2x 4tb Western Digital Hard drives NAS targets mounted VIA cifs RAID 1
------------------------------------------------------------------------------------------------------------------------------------------------------
## Active Virtualized devices
| Name | Type | OS | Purpose | VLAN |
|------|------|-----|---------|-------|
| pihole | LXC | Debian | DNS filtering + adblock | VLAN 10 |
| navidrome | LXC | Debian | Music streaming server | VLAN 10 |
| OpenMediaVault | VM | Debian | Storage/NAS integration | VLAN 10 |

## Service Breakdowns

### **1. Proxmox (Host System)**
- Runs all VMs/containers  
- Manages VM storage, VLAN tagging, and network bridges  
- Connects to pfSense and SG300 through a trunk port  
- VLANs used:
  - **10** → Server/Services  
  - **20** → Lab/Test (for Cybersecurity VMs and Labs)
  - **99** VPN connection
- Bridges:
  - `vmbr0` → management/Services VLAN tagging occurs at Vm level  

**Notes:**  
- Uses LXC for lightweight services (Pi-hole, Navidrome) and VMs for more stable (OMV)

---

### **2. Pi-hole (LXC)**
**Purpose:**  
Network-wide DNS filtering and adblocking.

**Configuration Notes:**  
- Runs on Debian LXC  
- VLAN 10  
- Forwarders go through pfSense    
- Acts as DNS server for the LAN + Server VLAN  

**Dependencies:**  
- Requires reliable network connection as it's my networks primary DNS server
- No major storage needs  

---

### **3. Navidrome (LXC)**
**Purpose:**  
Self-hosted music streaming accessible on LAN and over VPN (via Feishin and Arpeggi app).

**Configuration Notes:**  
- Runs in Debian LXC  
- Music library stored on mounted NAS share  
- VLAN 10  
- Web UI available  
- Must have active CIFS mount or playback fails  

**Dependencies:**  
- `/mnt/pve/NAS` must be mounted  
- NAS VM must be online for music to stream

---

### **4. OMV (OpenMediaVault NAS VM)**
**Purpose:**  
Provides SMB shares for Proxmox, Navidrome, backups, and general storage.

**Configuration Notes:**  
- Runs as full VM (Debian + OMV)  
- VLAN 10  
- Exports SMB share  
- CIFS mount used by Navidrome and Proxmox
- Runs in RAID 1 Configuration using 2 4tb WD HDD

**Dependencies:**  
- Needs to boot before Navidrome for stable library access
- If network or user changes occur it will require remount
