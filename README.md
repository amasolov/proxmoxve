# Proxmox VE Custom Integration Home Assistant


[Proxmox VE](https://www.proxmox.com/en/) is an open-source server virtualization environment. This integration allows you to poll various data and controls from your instance.

## Configuration

<div class='note'>
You should have at least one VM or container entry configured, else this integration won't do anything.
</div>

To use the `proxmoxve` component, add the following configuration to your `configuration.yaml` file:

```yaml
# Example configuration.yaml entry
proxmoxve:
  - host: IP_ADDRESS
    username: USERNAME
    password: PASSWORD
    nodes:
      - node: NODE_NAME
        vms:
          - VM_ID
        containers:
          - CONTAINER_ID
```

Example with multiple VMs, no containers, self-signed certificate and pve realm for the user setup described below:

```yaml
proxmoxve:
  - host: IP_ADDRESS
    username: USERNAME
    password: PASSWORD
    verify_ssl: false
    realm: pve
    nodes:
      - node: NODE_NAME
        vms:
          - VM_ID_1
          - VM_ID_2
```

## Proxmox Permissions

To be able to retrieve the status of VMs and containers, the user used to connect must minimally have the VM.Audit privilege. Below is a guide to how to configure a new user with the minimum required permissions.

### CREATE HOME ASSISTANT GROUP

Before creating the user, we need to create a group for the user. Privileges can be either applied to Groups or Roles.

* Click Datacenter
Open Permissions and click Groups
* Click the Create button above all the existing groups
Name the new group (e.g., HomeAssistant)
* Click Create
ADD GROUP PERMISSIONS TO ALL ASSETS

For the group to access the VMs we need to grant it the auditor role

* Click Datacenter
* Click Permissions
* Open Add and click Group Permission
* Select “/” for the path
* Select your Home Assistant group (HomeAssistant)
* Select the Auditor role (PVEAuditor)
* Make sure Propagate is checked

### CREATE HOME ASSISTANT USER

Creating a dedicated user for Home Assistant, limited to only to the access just created is the most secure method. These instructions use the pve realm for the user. This allows a connection, but ensures that the user is not authenticated for SSH connections. If you use the pve realm, just be sure to add realm: pve to your configuration.

* Click Datacenter
* Open Permissions and click Users
* Click Add
* Enter a username (e.g., homeassistant)
* Set the realm to “Proxmox VE authentication server”
* Enter a secure password (it can be complex as you will only need to copy/paste it into your Home Assistant configuration)
* Select the group just created earlier (HomeAssistant) to grant access to Proxmox
* Ensure Enabled is checked and Expire is set to “never”
* Click Add

In your Home Assistant configuration, use homeassistant@pve for the username and your chosen password for the password.
