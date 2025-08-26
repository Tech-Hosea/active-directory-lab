# Active Directory Lab — tech-hosea.local

## Overview
This lab demonstrates a realistic Active Directory environment using **Windows Server 2022** and **Windows 10/11 clients** in a homelab. The goal is to showcase practical IT support and system administration skills, including:

- Active Directory Domain Services (AD DS)
- DNS (Forward/Reverse zones, forwarders)
- DHCP (Scopes, reservations, options)
- Group Policy Objects (GPO) – Password Policy, Drive Mapping, Wallpaper, Screensaver Lockdown
- File sharing with NTFS & Share ACLs
- Client domain join and policy validation
- PowerShell automation & lab documentation
- DC health checks (`dcdiag`) and GPO validation (`gpresult`)

---

## 🧱 Lab Topology
- **Hypervisor:** Proxmox
- **Domain Controller:** DC01.tech-hosea.local (Windows Server 2022)
- **Clients:** CLIENT01, CLIENT02 (Windows 10/11)
- **Domain:** `tech-hosea.local`

| Device       | IP Address        | Roles/Notes                          |
|-------------|-----------------|-------------------------------------|
| DC01        | 192.168.10.10   | AD DS, DNS, DHCP                     |
| CLIENT01    | DHCP (192.168.10.101) | Domain joined                        |
| CLIENT02    | DHCP (192.168.10.102) | Optional domain joined                |
| Gateway     | 192.168.10.1    | Router / pfSense                     |

![Topology](topology.svg)

---

## ✅ Objectives / Key Skills Demonstrated
1. Install Windows Server 2022 → set static IP → rename to `DC01`.
2. Add **AD DS** + **DNS** roles → promote to new forest `tech-hosea.local`.
3. Install **DHCP**, create scope `192.168.10.100-200` → authorize.
4. Create OUs: `Corp/Users`, `Corp/Computers`, `Corp/Groups`.
5. Create users and groups; add test users to groups.
6. Create GPOs:
   - Drive mapping to `\\DC01\Share`
   - Password policy
   - Wallpaper and screensaver lockdown
7. Join CLIENT01 to the domain → verify policy & mapped drives.
8. Validate AD health and client policy application.

---

## ⚡ Quickstart / Reproduce Lab
1. **Proxmox VM creation**
   - Upload Windows Server ISO → create VM (CPU, RAM, Disk, NIC).
   - Screenshot: `images/01-proxmox-vm-create.png`

2. **Server setup**
   - Set static IP: `192.168.10.10`  
   - Rename server: `DC01` → reboot  
   - Screenshot: `images/02-server-rename-staticip.png`

3. **AD DS + DNS**
   - Install roles → Promote server to new forest `tech-hosea.local` → reboot  
   - Screenshot: `images/03-dc-promotion-success.png`

4. **DNS configuration**
   - Forward/Reverse zones, forwarders (8.8.8.8, 1.1.1.1)  
   - Screenshot: `images/04-dns-zones-forwarders.png`

5. **DHCP configuration**
   - Scope: `192.168.10.100-200`, Gateway: `192.168.10.1`, DNS: `192.168.10.10`  
   - Screenshot: `images/05-dhcp-scope-and-reservations.png`

6. **File Share & Permissions**
   - `C:\Shares\CorpShare`, Share: `Share`, NTFS: `Corp-Users` Modify  
   - Screenshot: `images/06-share-ntfs-perms.png`

7. **OU, Users & Groups**
   - OU structure: `Corp → Users / Computers / Groups`  
   - User: `jdoe` in `Corp/Users`  
   - Security Group: `Corp-Users` → add `jdoe`  
   - Screenshot: `images/07-aduc-ou-users-groups.png`

8. **GPOs**
   - Drive mapping `Z:` → `\\DC01\Share` for `Corp-Users`  
   - Password policy, screensaver, wallpaper lockdown  
   - Screenshot: `images/08-gpo-drive-map.png`

9. **Client Join**
   - CLIENT01: set DNS to DC IP → Join domain → Reboot → Login `tech-hosea\jdoe`  
   - Screenshot: `images/09-client-join-domain.png`

10. **Validation**
    - Force GPO: `gpupdate /force`  
    - Check applied GPO: `gpresult /h C:\Users\Public\gpresult-client.html`  
    - DC Health: `dcdiag /v`  
    - Mapped drive: open Explorer, check `Z:` → `\\DC01\Share`  
    - Screenshots:  
      - `images/10-gpresult-client.png` or `results/gpresult-client.html`  
      - `images/11-dcdiag-output.png`  
      - `demo-mapped-drive.gif` (login → mapped drive)

---

## 🧪 Validation & Evidence
- `gpresult /r` → confirms GPO applied
- Access `\\DC01\Share` according to group membership
- DC Health Check (`dcdiag`) shows no errors
- DHCP leases assigned correctly
- Event Viewer logs clean on DC & clients

---

## 📸 Screenshot Checklist
| Filename                          | Description                                        |
|----------------------------------|---------------------------------------------------|
| 01-proxmox-vm-create.png          | Proxmox VM creation summary                        |
| 02-server-rename-staticip.png     | Server renamed + static IP configured             |
| 03-dc-promotion-success.png       | AD DS promotion success                            |
| 04-dns-zones-forwarders.png       | DNS forward & reverse zones + forwarders         |
| 05-dhcp-scope-and-reservations.png| DHCP scope & reservations                          |
| 06-share-ntfs-perms.png           | File share properties (Sharing + Security tabs)  |
| 07-aduc-ou-users-groups.png       | OU structure, users, groups                        |
| 08-gpo-drive-map.png              | GPO linked to OU, drive mapping preference       |
| 09-client-join-domain.png         | Client domain join/login screen                   |
| 10-gpresult-client.png            | GPO applied on client                             |
| 11-dcdiag-output.png              | DC health check results                            |
| demo-mapped-drive.gif             | Login → mapped drive demo                          |

---

## 🔧 Scripts & Automation
- `scripts/00-setup-network.ps1` → Configure IP/DNS on DC  
- `scripts/01-install-roles-promote-dc.ps1` → Install AD DS/DNS, promote DC  
- `scripts/02-config-dns-dhcp.ps1` → Configure DNS forwarders & DHCP scope  
- `scripts/create-users-groups.ps1` → Create OUs, users, groups

> ⚠️ Replace placeholder passwords securely. Do **not** commit plaintext credentials.

---

## 📚 Notes / Troubleshooting
- **Client cannot join domain?** → Check DNS points to DC IP, `ipconfig /all`  
- **GPO not applying?** → `gpupdate /force`, then `gpresult /r`, check Event Viewer  
- **DHCP issues?** → Ensure server is authorized in AD and scope is active  
- **DNS resolution fails?** → Verify forwarders and A records  

**Tags:** `Active-Directory`, `Windows-Server-2022`, `GPO`, `PowerShell`, `DHCP`, `DNS`, `Homelab`, `IT-Support`, `System-Admin`
