# layer-0-research

Part 1:

- Create/Update/Delete VM

1. Create

- Works fine
attached instance-create-scripts verify this.
to test
```
virtualenv env -p /usr/bin/python3
source env/bin/activate
pip install -r requirements.txt
openstack stack create -t <template-file.yaml>
```

2. Update VMs
Updating the stack will sometimes cause things to be updated
see [here](https://docs.openstack.org/developer/heat/template_guide/openstack.html) for a list of resources
and whether or not resources will be replaced on update. 
(This only updates the effected resource, not anything else)

Tests with updates crashed when it tried to replace. Ala: 
```
(env) aabdi@andreas-linux:layer0(C3TP-196**)> openstack stack update -t ./instance-5-create.yaml test-stack
+---------------------+------------------------------------------------------------+
| Field               | Value                                                      |
+---------------------+------------------------------------------------------------+
| id                  | cc1ef094-069d-4824-956d-ba2bfca9e140                       |
| stack_name          | test-stack                                                 |
| description         | Updated template 3 with flavor change. Should not replace. |
| creation_time       | 2017-05-26T23:27:33                                        |
| updated_time        | 2017-05-26T23:57:59                                        |
| stack_status        | UPDATE_IN_PROGRESS                                         |
| stack_status_reason | Stack UPDATE started                                       |
+---------------------+------------------------------------------------------------+
(env) aabdi@andreas-linux:layer0(C3TP-196**)> openstack stack show test-stack
+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------+
| Field                 | Value                                                                                                                                       |
+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------+
| id                    | cc1ef094-069d-4824-956d-ba2bfca9e140                                                                                                        |
| stack_name            | test-stack                                                                                                                                  |
| description           | Updated template 3 with flavor change. Should not replace.                                                                                  |
| creation_time         | 2017-05-26T23:27:33                                                                                                                         |
| updated_time          | 2017-05-26T23:57:59                                                                                                                         |
| stack_status          | UPDATE_IN_PROGRESS                                                                                                                          |
| stack_status_reason   | Stack UPDATE started                                                                                                                        |
| parameters            | OS::project_id: 3bbebcd6ffec48b19f1d8dbdf31c237d                                                                                            |
|                       | OS::stack_id: cc1ef094-069d-4824-956d-ba2bfca9e140                                                                                          |
|                       | OS::stack_name: test-stack                                                                                                                  |
|                       |                                                                                                                                             |
| outputs               | []                                                                                                                                          |
|                       |                                                                                                                                             |
| links                 | - href: https://east.cloud.computecanada.ca:8004/v1/3bbebcd6ffec48b19f1d8dbdf31c237d/stacks/test-stack/cc1ef094-069d-4824-956d-ba2bfca9e140 |
|                       |   rel: self                                                                                                                                 |
|                       |                                                                                                                                             |
| notification_topics   | []                                                                                                                                          |
| stack_user_project_id | f43b3ee0fdd94286b5344f043ac239c9                                                                                                            |
| parent                | None                                                                                                                                        |
| capabilities          | []                                                                                                                                          |
| disable_rollback      | True                                                                                                                                        |
| stack_owner           | None                                                                                                                                        |
| tags                  | None                                                                                                                                        |
| timeout_mins          | None                                                                                                                                        |
+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------+
(env) aabdi@andreas-linux:layer0(C3TP-196**)> openstack stack show test-stack
+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Field                 | Value                                                                                                                                                         |
+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------+
| id                    | cc1ef094-069d-4824-956d-ba2bfca9e140                                                                                                                          |
| stack_name            | test-stack                                                                                                                                                    |
| description           | Updated template 3 with flavor change. Should not replace.                                                                                                    |
| creation_time         | 2017-05-26T23:27:33                                                                                                                                           |
| updated_time          | 2017-05-26T23:57:59                                                                                                                                           |
| stack_status          | UPDATE_FAILED                                                                                                                                                 |
| stack_status_reason   | BadRequest: resources.nova_server: Invalid volume: volume '9e094137-6453-4cc5-b80e-38e410f2c727' status must be 'available'. Currently in 'in-use' (HTTP 400) |
|                       | (Request-ID: req-dcd45267-0cd9-4d64-9807-5cd2ff46f0a6)                                                                                                        |
| parameters            | OS::project_id: 3bbebcd6ffec48b19f1d8dbdf31c237d                                                                                                              |
|                       | OS::stack_id: cc1ef094-069d-4824-956d-ba2bfca9e140                                                                                                            |
|                       | OS::stack_name: test-stack                                                                                                                                    |
|                       |                                                                                                                                                               |
| outputs               | []                                                                                                                                                            |
|                       |                                                                                                                                                               |
| links                 | - href: https://east.cloud.computecanada.ca:8004/v1/3bbebcd6ffec48b19f1d8dbdf31c237d/stacks/test-stack/cc1ef094-069d-4824-956d-ba2bfca9e140                   |
|                       |   rel: self                                                                                                                                                   |
|                       |                                                                                                                                                               |
| parent                | None                                                                                                                                                          |
| tags                  | None                                                                                                                                                          |
| capabilities          | []                                                                                                                                                            |
| timeout_mins          | None                                                                                                                                                          |
| disable_rollback      | True                                                                                                                                                          |
| notification_topics   | []                                                                                                                                                            |
| stack_user_project_id | f43b3ee0fdd94286b5344f043ac239c9                                                                                                                              |
| stack_owner           | None                                                                                                                                                          |
+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------+

```

Essentially what that means is that if you try to update an instance that's been started from a volume, it
gets funky. 

It might be wise to follow Cedric's suggestion of not updating when using cfn like software, and just update manually. 
Doesn't seem feasible to update in all cases. 
An example issue that may also happen:  
`https://ask.openstack.org/en/question/101154/heat-update-failed-on-block-volume-operation/`


3. Delete VMs

```
openstack stack delete <stack-name>
```
The prompt to verify deletion is funky. 
It will seem like its hanging, but what's happening is that their document is hiding a prompt
`Are you sure you want to delete this stack(s) [y/N]? `

So if you type `y` or `N` you can proceed. 
We can try fixing this if its important. 
<-- can fix it by writing command with  -y flag, or just modifying the stdout code to run print instead.  
- what happens with resource overallocations? 
It will create the stack, then it will fail. 
If you check with 
```
openstack stack show <stack-name>

```

It will show why it failed
```
+---------------------+-----------------------------------------------------------------------------+
| Field               | Value                                                                       |
+---------------------+-----------------------------------------------------------------------------+
| id                  | aee01aef-6108-438a-a669-f0254ade5ed6                                        |
| stack_name          | test-stack-2                                                                |
| description         | Template to create a basic nova server with ip attached from booted volume. |
| creation_time       | 2017-05-26T23:49:26                                                         |
| updated_time        | None                                                                        |
| stack_status        | CREATE_IN_PROGRESS                                                          |
| stack_status_reason | Stack CREATE started                                                        |
+---------------------+-----------------------------------------------------------------------------+
(env) aabdi@andreas-linux:layer0(C3TP-196**)> openstack stack show test-stack-2
+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------+
| Field                 | Value                                                                                                                                         |
+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------+
| id                    | aee01aef-6108-438a-a669-f0254ade5ed6                                                                                                          |
| stack_name            | test-stack-2                                                                                                                                  |
| description           | Template to create a basic nova server with ip attached from booted volume.                                                                   |
| creation_time         | 2017-05-26T23:49:26                                                                                                                           |
| updated_time          | None                                                                                                                                          |
| stack_status          | CREATE_FAILED                                                                                                                                 |
| stack_status_reason   | Resource CREATE failed: OverQuotaClient: resources.new_floating_ip: Quota exceeded for resources: ['floatingip'].                             |
|                       | Neutron server returns request_ids: ['req-eec4959a-f5ea-429c-b316-bd8b5a493505']                                                              |
| parameters            | OS::project_id: 3bbebcd6ffec48b19f1d8dbdf31c237d                                                                                              |
|                       | OS::stack_id: aee01aef-6108-438a-a669-f0254ade5ed6                                                                                            |
|                       | OS::stack_name: test-stack-2                                                                                                                  |
|                       |                                                                                                                                               |
| outputs               | []                                                                                                                                            |
|                       |                                                                                                                                               |
| links                 | - href: https://east.cloud.computecanada.ca:8004/v1/3bbebcd6ffec48b19f1d8dbdf31c237d/stacks/test-stack-2/aee01aef-6108-438a-a669-f0254ade5ed6 |
|                       |   rel: self                                                                                                                                   |
|                       |                                                                                                                                               |
| capabilities          | []                                                                                                                                            |
| stack_user_project_id | 90ebcf4f59664289bfd6d888fa683aaf                                                                                                              |
| parent                | None                                                                                                                                          |
| stack_owner           | None                                                                                                                                          |
| timeout_mins          | None                                                                                                                                          |
| disable_rollback      | True                                                                                                                                          |
| notification_topics   | []                                                                                                                                            |
| tags                  | None                                                                                                                                          |
+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------+

```
- Attach Volumes
- Status graphs
- Exportation of status? 

Part 2:
- ACLs/Security Groups/Firewalls
- DNS fixtures. 
- Ports/Floating IP Allocations. 
