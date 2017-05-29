## Update 
1. Attempt to modify a **root** volume that has been attached to a VM.
- Failed in general. No workaround available. 
- Attempted turning off instance, deleting the instance, cannot detach the volume. 
- (instance-4-create.yaml vs instance-6-create.yaml) 
- Update required an extra volume to create. 
2. Attempt to change the keypair for a VM with a root volume. 
- worked when I turned off the VM that it was running off of.
- (instance-4-create.yaml vs instance-5-create.yaml) 
3. Attempt to remove a boot volume. 

4. Attempt to modify an attached volume down in size.
- NotSupported: resources.attached_volume: Shrinking volume is not supported.                                         
- (instance-8-create.yaml vs instance-7-create.yaml) 
5. Attempt to modify an attached volume up in size. 
- Error: resources.attached_volume: Volume in use
- Worked after detaching the volume. New sized volume needs to be reformatted to be used, so not plug and play. 
- (instance-7-create.yaml vs instance-8-create.yaml) 
5. Attempt to add an attached volume.
- BadRequest: resources.nova_server: Invalid volume: volume status must be 'available'. Currently in 'in-use' (HTTP 400)
- Worked if I turned off the instance it was running off of. 
- (instance-6-create.yaml vs instance-7-create.yaml)  
6. Attempt to remove an attached volume with a boot volume existing.
- BadRequest: resources.nova_server: Invalid volume: volume status must be 'available'. Currently in 'in-use' (HTTP 400) 
- Worked if I turned off the instance it was running off of. 
- (instance-7-create.yaml vs instance-6-create.yaml)  


(Perhaps we overlay update calls with a shutdown to all instances related first?)
(We could also try overlaying with detach all known volumes in stack call)

## Delete
1. Attempt to delete a volume that has been attached to another VM. 
- Perform manually by deleting a vm and then attaching the bootable volume to something else. 
- Probably not a good use case but it happens. 
- Fails. 
