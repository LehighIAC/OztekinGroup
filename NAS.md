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
# Maintenance Manual

## System Config
| Item | Model | Remark |
| --- | --- | --- |
| Chassis | Dell T30 | |
| CPU | Xeon E3-1225 v5 | |
| RAM | 4x8GB DDR4 ECC Unbuffered | | 
| SSD | Intel Optane 32GB | Boot drive |
| HDD | 4x WD Red Plus 4TB | Chassis cage |
| HDD | 4x Seagate Ironwolf 4TB | Additional HDD cage |
| OS | TrueNAS Core | |

## Hard Drive Replacement