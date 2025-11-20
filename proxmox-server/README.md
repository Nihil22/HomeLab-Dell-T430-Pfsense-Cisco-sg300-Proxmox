This section documents my primary virtualization stack running on a Dell PowerEdge T430. The server hosts all core services in my homelab and integrates with pfSense (routing/firewall) and my Cisco SG300 switch (VLAN segmentation).
----------------------------------------------------------------------------------------------------------------------------------------------------

Hardware
**Dell PowerEdge T430**
- CPU: Intel Xeonr E5-2603  
- RAM: 32 GB  
- Storage:
  - 2 1 tb ssd in RAID 1 configuration
  - 2 4tb WD Hard drives NAS targets mounted VIA cifs
------------------------------------------------------------------------------------------------------------------------------------------------------
Active Virtualized devices
| Name | Type | OS | Purpose | VLAN |
|------|------|-----|---------|-------|
| pihole | LXC | Debian | DNS filtering + adblock | VLAN 10 |
| navidrome | LXC | Debian | Music streaming server | VLAN 10 |
| omv / truenas | VM | Debian/TrueNAS | Storage/NAS integration | VLAN 10 |
