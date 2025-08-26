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

<img width="536" height="324" alt="Topology" src="https://github.com/user-attachments/assets/c21ffd5c-6795-4703-8d40-14e3cfa45f62" />


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
   - <img width="461" height="212" alt="Window Server 2022" src="https://github.com/user-attachments/assets/da750f8f-5a01-47e5-9003-a953dd953936" />


2. **Server setup**
   - Set static IP: `192.168.1.11`  
   - Rename server: `DC01` → reboot  
   - <img width="535" height="305" alt="05-dhcp-scope-and-reservations (2)" src="https://github.com/user-attachments/assets/5d5f729c-2005-4aee-8280-45434275b535" />

   - <img width="592" height="258" alt="03_server-rename" src="https://github.com/user-attachments/assets/ccb85271-b864-4cb1-a946-127a8ff3fb5e" />

3. **AD DS + DNS**
   - Install roles → Promote server to new forest `tech-hosea.local` → reboot  
   - <img width="518" height="336" alt="03-dc-promotion-success" src="https://github.com/user-attachments/assets/13737cb3-f61d-4057-a0ab-8b0b8e3c4a20" />
   - <img width="573" height="324" alt="04_add-roles-ad-ds" src="https://github.com/user-attachments/assets/cddfd349-cc64-44a4-9dfd-9752988da707" />


4. **DNS configuration**
   - Forward/Reverse zones, forwarders (8.8.8.8, 1.1.1.1)  
   - <img width="548" height="292" alt="04-dns-zones-forwarders" src="https://github.com/user-attachments/assets/65a2baef-66a0-49dc-bce5-7980ee92840c" />
   - <img width="532" height="333" alt="06_dns-forwarders" src="https://github.com/user-attachments/assets/ae3c0e36-5f00-47dc-ae55-792484bcc3ad" />



5. **DHCP configuration**
   - Scope: `192.168.10.100-200`, Gateway: `192.168.10.1`, DNS: `192.168.10.10`  
   - <img width="535" height="305" alt="05-dhcp-scope-and-reservations (2)" src="https://github.com/user-attachments/assets/9099cd2e-4402-477d-b1e3-f0f1543c9a84" />
   - <img width="543" height="294" alt="05-dhcp-scope-and-reservations" src="https://github.com/user-attachments/assets/2de45716-a49f-4f01-8ed9-b8d5c198ce66" />



6. **File Share & Permissions**
   - `C:\Shares\CorpShare`, Share: `Share`, NTFS: `Corp-Users` Modify  
   - <img width="543" height="305" alt="06-share-ntfs-perms" src="https://github.com/user-attachments/assets/65ef587d-7a35-4afa-a932-abb474c77080" />


7. **OU, Users & Groups**
   - OU structure: `Corp → Users / Computers / Groups`  
   - User: `jdoe` in `Corp/Users`  
   - Security Group: `Corp-Users` → add `jdoe`  
   - <img width="517" height="318" alt="10_ou-users-groups" src="https://github.com/user-attachments/assets/5cf0bf5b-3cf7-4e66-ac11-40650e6f601b" />


8. **GPOs**
   - Drive mapping `Z:` → `\\DC01\Share` for `Corp-Users`  
   - Password policy, screensaver, wallpaper lockdown  
   - <img width="519" height="310" alt="Password policy" src="https://github.com/user-attachments/assets/801d3397-886c-4234-99f4-500da17c8c4b" />
   - <img width="523" height="316" alt="Screensaver-lockdown" src="https://github.com/user-attachments/assets/ac05ddbe-38ef-4a1e-b422-d3269158dcc3" />



9. **Client Join**
   - CLIENT01: set DNS to DC IP → Join domain → Reboot → Login `tech-hosea\jdoe`  
   - <img width="425" height="302" alt="Screenshot 2025-08-26 122350" src="https://github.com/user-attachments/assets/88728683-b955-4dd4-9f14-2e04eb95c4e5" />

10. **Validation**
    - Force GPO: `gpupdate /force`  
    - Check applied GPO: `gpresult /h C:\Users\Public\gpresult-client.html`  
    - DC Health: `dcdiag /v`  
    - Mapped drive: open Explorer, check `Z:` → `\\DC01\Share`  

---

## 🧪 Validation & Evidence
- `gpresult /r` → confirms GPO applied
- Access `\\DC01\Share` according to group membership
- DC Health Check (`dcdiag`) shows no errors
- DHCP leases assigned correctly
- Event Viewer logs clean on DC & clients

## 🔧 Scripts & Automation
- `scripts/00-setup-network.ps1` → Configure IP/DNS on DC  
- `scripts/01-install-roles-promote-dc.ps1` → Install AD DS/DNS, promote DC  
- `scripts/02-config-dns-dhcp.ps1` → Configure DNS forwarders & DHCP scope  
- `scripts/create-users-groups.ps1` → Create OUs, users, groups


---

## 📚 Notes / Troubleshooting
- **Client cannot join domain?** → Check DNS points to DC IP, `ipconfig /all`  
- **GPO not applying?** → `gpupdate /force`, then `gpresult /r`, check Event Viewer  
- **DHCP issues?** → Ensure server is authorized in AD and scope is active  
- **DNS resolution fails?** → Verify forwarders and A records  

**Tags:** `Active-Directory`, `Windows-Server-2022`, `GPO`, `PowerShell`, `DHCP`, `DNS`, `Homelab`, `IT-Support`, `System-Admin`
