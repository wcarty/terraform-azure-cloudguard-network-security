# Check Point CloudGuard VMSS Module - Existing VNet

This Terraform module deploys Check Point CloudGuard Network Security VMSS solution into an existing VNet in azure.
As part of the deployment the following resources are created:
- Resource group
- Role assignment - conditional creation


For additional information,
please see the [CloudGuard Network for Azure Virtual Machine Scale Sets (VMSS) Deployment Guide](https://sc1.checkpoint.com/documents/IaaS/WebAdminGuides/EN/CP_VMSS_for_Azure/Default.htm) 

This solution uses the following modules:
- common - used for creating a resource group and defining common variables.

## Usage
Follow best practices for using CGNS modules on [the root page](https://registry.terraform.io/modules/CheckPointSW/cloudguard-network-security/azure/latest).

**Example:**
```
provider "azurerm" {
  features {}
}

module "example_module" {

  source  = "CheckPointSW/cloudguard-network-security/azure//modules/vmss_existing_vnet"
  version = "1.0.4"

    subscription_id                 = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
    source_image_vhd_uri            = "noCustomUri"
    resource_group_name             = "checkpoint-vmss-terraform"
    location                        = "eastus"
    vmss_name                       = "checkpoint-vmss-terraform"
    vnet_name                       = "checkpoint-vmss-vnet"
    vnet_resource_group             = "existing-vnet"
    frontend_subnet_name            = "frontend"
    backend_subnet_name             = "backend"
    backend_lb_IP_address           = 4
    admin_password                  = "xxxxxxxxxxxx"
    sic_key                         = "xxxxxxxxxxxx"
    vm_size                         = "Standard_D3_v2"
    disk_size                       = "100"
    vm_os_sku                       = "sg-byol"
    vm_os_offer                     = "check-point-cg-r8110"
    os_version                      = "R8110"
    bootstrap_script                = "touch /home/admin/bootstrap.txt; echo 'hello_world' > /home/admin/bootstrap.txt"
    allow_upload_download           = true
    authentication_type             = "Password"
    availability_zones_num          = "1"
    minimum_number_of_vm_instances  = 2
    maximum_number_of_vm_instances  = 10
    management_name                 = "mgmt"
    management_IP                   = "13.92.42.181"
    management_interface            = "eth1-private"
    configuration_template_name     = "vmss_template"
    notification_email              = ""
    frontend_load_distribution      = "Default"
    backend_load_distribution       = "Default"
    enable_custom_metrics           = true
    enable_floating_ip              = false
    deployment_mode                 = "Standard"
    admin_shell                     = "/etc/cli.sh"
    serial_console_password_hash    = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
    maintenance_mode_password_hash  = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
    nsg_id                          = ""
    add_storage_account_ip_rules    = false
    storage_account_additional_ips  = []
}
```    
        
## Deploy Without Public IP

1. By default, the VMSS is deployed with public IP
2. To deploy without public IP, remove the "public_ip_address_configuration" block in main.tf

## Conditional creation
- To create role assignment and enable CloudGuard metrics in order to send statuses and statistics collected from VMSS instances to the Azure Monitor service:
  ```
  enable_custom_metrics = true
  ```

### Module's variables:

# Parameters Description

| Name | Description | Type | Allowed values |
|------|-------------|------|----------------|
| **subscription_id** | The subscription ID is used to pay for Azure cloud services | string | |
| **source_image_vhd_uri** | The URI of the blob containing the development image. Please use noCustomUri if you want to use marketplace images | string | **Default:** "noCustomUri" |
| **resource_group_name** | The name of the resource group that will contain the contents of the deployment | string | Resource group names only allow alphanumeric characters, periods, underscores, hyphens and parenthesis and cannot end in a period.<br/>Note: Resource group name must not contain reserved words based on: sk40179. |
| **location** | The region where the resources will be deployed at | string | The full list of Azure regions can be found at https://azure.microsoft.com/regions. |
| **vmss_name** | The name of the Check Point VMSS Object | string | Only alphanumeric characters are allowed, and the name must be 1-30 characters long.<br/>Note: vmss_name name must not contain reserved words based on: sk40179. |
| **vnet_name** | Virtual Network name | string | The name must begin with a letter or number, end with a letter, number or underscore, and may contain only letters, numbers, underscores, periods, or hyphens. |
| **vnet_resource_group** | Resource Group of the existing virtual network | string | The exact name of the existing vnet's resource group. |
| **frontend_subnet_name** | Specifies the name of the external subnet | string | The exact name of the existing external subnet. |
| **backend_subnet_name** | Specifies the name of the internal subnet | string | The exact name of the existing internal subnet. |
| **backend_lb_IP_address** | Is a whole number that can be represented as a binary integer with no more than the number of digits remaining in the address after the given prefix | string | Starting from 5-th IP address in a subnet. For example: subnet - 10.0.1.0/24, backend_lb_IP_address = 4 , the LB IP is 10.0.1.4. |
| **admin_password** | The password associated with the local administrator account on each cluster member | string | Password must have 3 of the following: 1 lower case character, 1 upper case character, 1 number, and 1 special character. |
| **sic_key** | The Secure Internal Communication one time secret used to set up trust between the cluster object and the management server | string | Only alphanumeric characters are allowed, and the value must be 12-30 characters long. |
| **vm_size** | Specifies the size of Virtual Machine | string | "Standard_DS2_v2", "Standard_DS3_v2", "Standard_DS4_v2", "Standard_DS5_v2", "Standard_F2s", "Standard_F4s", "Standard_F8s", "Standard_F16s", "Standard_D4s_v3", "Standard_D8s_v3", "Standard_D16s_v3", "Standard_D32s_v3", "Standard_D64s_v3", "Standard_E4s_v3", "Standard_E8s_v3", "Standard_E16s_v3", "Standard_E20s_v3", "Standard_E32s_v3", "Standard_E64s_v3", "Standard_E64is_v3", "Standard_F4s_v2", "Standard_F8s_v2", "Standard_F16s_v2", "Standard_F32s_v2", "Standard_F64s_v2", "Standard_M8ms", "Standard_M16ms", "Standard_M32ms", "Standard_M64ms", "Standard_M64s", "Standard_D2_v2", "Standard_D3_v2", "Standard_D4_v2", "Standard_D5_v2", "Standard_D11_v2", "Standard_D12_v2", "Standard_D13_v2", "Standard_D14_v2", "Standard_D15_v2", "Standard_F2", "Standard_F4", "Standard_F8", "Standard_F16", "Standard_D4_v3", "Standard_D8_v3", "Standard_D16_v3", "Standard_D32_v3", "Standard_D64_v3", "Standard_E4_v3", "Standard_E8_v3", "Standard_E16_v3", "Standard_E20_v3", "Standard_E32_v3", "Standard_E64_v3", "Standard_E64i_v3", "Standard_DS11_v2", "Standard_DS12_v2", "Standard_DS13_v2", "Standard_DS14_v2", "Standard_DS15_v2", "Standard_D2_v5", "Standard_D4_v5", "Standard_D8_v5", "Standard_D16_v5","Standard_D32_v5", "Standard_D2s_v5", "Standard_D4s_v5", "Standard_D8s_v5", "Standard_D16s_v5", "Standard_D2d_v5", "Standard_D4d_v5", "Standard_D8d_v5", "Standard_D16d_v5", "Standard_D32d_v5", "Standard_D2ds_v5", "Standard_D4ds_v5", "Standard_D8ds_v5", "Standard_D16ds_v5", "Standard_D32ds_v5". |
| **disk_size** | Storage data disk size size(GB) must be 100 for versions R81.20 and below | string | A number in the range 100 - 3995 (GB).<br/>**Default:** 100 |
| **vm_os_sku** | A sku of the image to be deployed | string | "sg-byol" - BYOL license;<br/>"sg-ngtp" - NGTP PAYG license;<br/>"sg-ngtx" - NGTX PAYG license. |
| **vm_os_offer** | The name of the image offer to be deployed | string | "check-point-cg-r8110";<br/>"check-point-cg-r8120";<br/>"check-point-cg-r82". |
| **os_version** | GAIA OS version | string | "R8110";<br/>"R8120";<br/>"R82". |
| **bootstrap_script** | An optional script to run on the initial boot | string | Bootstrap script example:<br/>"touch /home/admin/bootstrap.txt; echo 'hello_world' > /home/admin/bootstrap.txt".<br/>The script will create bootstrap.txt file in the /home/admin/ and add 'hello word' string into it. |
| **allow_upload_download** | Automatically download Blade Contracts and other important data. Improve product experience by sending data to Check Point | boolean | true;<br/>false. |
| **authentication_type** | Specifies whether a password authentication or SSH Public Key authentication should be used | string | "Password";<br/>"SSH Public Key". |
| **availability_zones_num** | A list of a single item of the Availability Zone which the Virtual Machine should be allocated in | string | "centralus", "eastus2", "francecentral", "northeurope", "southeastasia", "westeurope", "westus2", "eastus", "uksouth". |
| **minimum_number_of_vm_instances** | The minimum number of VMSS instances for this resource | number | Valid values are in the range 0 - 10. |
| **maximum_number_of_vm_instances** | The maximum number of VMSS instances for this resource | number | Valid values are in the range 0 - 10. |
| **management_name** | The name of the management server as it appears in the configuration file | string | Field cannot be empty. Only alphanumeric characters or '_'/'-' are allowed, and the name must be 1-30 characters long. |
| **management_IP** | The IP address used to manage the VMSS instances | string | A valid IP address. |
| **management_interface** | Management option for the Gateways in the VMSS | string | "eth0-public" - Manages the GWs using their external NIC's public IP address;<br/>"eth0-private" - Manages the GWs using their external NIC's private IP address;<br/>"eth1-private" - Manages the GWs using their internal NIC's private IP address.<br/>**Default:** "eth1-private" |
| **configuration_template_name** | The configuration template name as it appears in the configuration file | string | Field cannot be empty. Only alphanumeric characters or '_'/'-' are allowed, and the name must be 1-30 characters long. |
| **frontend_load_distribution** | The load balancing distribution method for the External Load Balancer | string | "Default" - None(5-tuple);<br/>"SourceIP" - ClientIP(2-tuple);<br/>"SourceIPProtocol" - ClientIP and protocol(3-tuple). |
| **backend_load_distribution** | The load balancing distribution method for the Internal Load Balancer | string | "Default" - None(5-tuple);<br/>"SourceIP" - ClientIP(2-tuple);<br/>"SourceIPProtocol" - ClientIP and protocol(3-tuple). |
| **notification_email** | An email address to notify about scaling operations | string | Leave empty double quotes or enter a valid email address. |
| **enable_custom_metrics** | Indicates whether Custom Metrics will be used for VMSS Scaling policy and VM monitoring | boolean | true;<br/>false.<br/>**Default:** true |
| **enable_floating_ip** | Indicates whether the load balancers will be deployed with floating IP | boolean | true;<br/>false.<br/>**Default:** false |
| **deployment_mode** | Indicates which load balancer need to be deployed. External + Internal(Standard), only External, only Internal | string | Standard;<br/>External;<br/>Internal.<br/>**Default:** "Standard" |
| **admin_shell** | Enables to select different admin shells | string | /etc/cli.sh;<br/>/bin/bash;<br/>/bin/csh;<br/>/bin/tcsh.<br/>**Default:** "/etc/cli.sh" |
| **serial_console_password_hash** | Optional parameter, used to enable serial console connection in case of SSH key as authentication type, to generate password hash use the command 'openssl passwd -6 PASSWORD' on Linux and paste it here | string | |
| **maintenance_mode_password_hash** | Maintenance mode password hash, relevant only for R81.20 and higher versions, to generate a password hash use the command 'grub2-mkpasswd-pbkdf2' on Linux and paste it here | string | |
| **nsg_id** | Optional ID for a Network Security Group that already exists in Azure, if not provided, will create a default NSG | string | Existing NSG resource ID.<br/>**Default:** "" |
| **add_storage_account_ip_rules** | Add Storage Account IP rules that allow access to the Serial Console only for IPs based on their geographic location, if false then accses will be allowed from all networks | boolean | true;<br/>false.<br/>**Default:** false |
| **storage_account_additional_ips** | IPs/CIDRs that are allowed access to the Storage Account | list(string) | A list of valid IPs and CIDRs.<br/>**Default:** [] |
| **security_rules** | Security rules for the Network Security | list(any) | A list of valid security rules values.<br/>A security rule composed of:<br/>{name, priority, direction, access, protocol, source_port_ranges, destination_port_ranges, source_address_prefix, destination_address_prefix, description}.<br/>**Default:** [{"name":"AllowAllInBound", "priority":"100", "direction":"Inbound", "access":"Allow", "protocol":"*", "source_port_ranges":"*", "destination_port_ranges":"", "description":"Allow all inbound connections", "source_address_prefix":"*", "destination_address_prefix":""}] |
| **admin_SSH_key** | The SSH public key for SSH connections to the instance.<br/>Used when the authentication_type is 'SSH Public Key' | string | **Default:** "" |