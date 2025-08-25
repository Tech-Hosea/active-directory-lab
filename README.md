# Active Directory Lab (Windows Server 2022)

Domain Services, DNS, DHCP, Group Policy, and client join in a realistic homelab.

## 🧱 Topology
- Proxmox
- 1× Windows Server 2022 (DC01) – AD DS, DNS, DHCP
- 1–2× Windows 10/11 clients (CLIENT01/CLIENT02)

## ✅ Objectives
- Stand up AD DS forest: `tech-hosea.local`
- Configure DNS (A/PTR, forwarders), DHCP scopes, reservations
- Join clients to domain, create OUs and users
- Create GPOs: password policy, drive mapping, wallpaper, screensaver lockdown
- Test permissions with security groups + NTFS/Share ACLs

## 🚀 Quick Steps
1. Install Windows Server → set static IP → rename to `DC01`
2. Add **AD DS** and **DNS** roles → promote to new forest `tech-hosea.local`
3. Install **DHCP** → create scope (e.g., `192.168.10.100-200`) → authorize
4. Create OUs: `Corp/Users`, `Corp/Computers`, `Corp/Groups`
5. Create users and groups; add test users to groups
6. Create a **Drive Mapping GPO** to `\DC01\Share`
7. Join CLIENT01 to domain → verify policy & drive map

## 🧪 Validation
- `gpresult /r` (policy applied)
- Access to `\DC01\Share` according to group membership
- Event Viewer clean on DC01 and clients

## 📸 Screenshots
- `images/` folder with DC promotion, DHCP scope, GPO results

## 📚 Notes
- Consider enabling **BitLocker** via GPO and storing recovery keys in AD.
