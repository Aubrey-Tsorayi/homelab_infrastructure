## Setting up Terraform user and roles
root@proxmox:~# pveum acl mod / -user terraform-prov@pve -role TerraformProv-privs
ACL update failed: role 'TerraformProv-privs' does not exist
### Fix
- `pveum role list`
- Role was not succefully created becuase used the wrong comand
	- updated command: `pveum role add TerraformProv-privs --privs "VM.Allocate VM.Clone VM.Config.CDROM VM.Config.CPU VM.Config.Cloudinit VM.Config.Disk VM.Config.HWType VM.Config.Memory VM.Config.Network VM.Config.Options VM.Audit VM.PowerMgmt" (corrected in the final docs)
		- Note: 
			- the `--privs` is needed before adding the privileges
			- the privileges are entered as individuals not one long string
- 
`

