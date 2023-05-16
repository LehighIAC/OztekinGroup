# User's manual

## Windows
Map network drive
```
\\oztekingroup.dept.lehigh.edu\Lab
```
then type your NAS username/password.

If you are on LTS-owned Windows PC, the username should be `WORKGROUP\<username>`

## Linux/macOS
```
smb://<username>@oztekingroup.dept.lehigh.edu/Lab
```
## SSH
```
ssh <username>@oztekingroup.dept.lehigh.edu
```
# Maintenance manual

## System Config
| Item | Model | Remark |
| --- | --- | --- |
| Chassis | Dell T30 | |
| CPU | Xeon E3-1225 v5 | |
| RAM | 2x8GB DDR4 ECC Unbuffered | | 
| SSD | Intel Optane 32GB | Boot drive |
| HDD | 4x WD Red Plus 4TB | Chassis cage |
| HDD | 4x Seagate Ironwolf 4TB | Additional HDD cage |
| OS | TrueNAS Scale Bluefin | |

## Hard Drive Replacement

## System Maintenance

## Rustdesk server
Better not touch it if nothing's wrong.

The Rustdesk server is installed like a Matryoshka doll. The relation is TrueNAS Scale -> docker-compose -> portainer -> rustdesk server

It is suggest to use our own Rustdesk server due to:
1. faster connection speed within the campus.
2. No license required (You might get banned from Teamviewer/Anydesk)
3. Better security
