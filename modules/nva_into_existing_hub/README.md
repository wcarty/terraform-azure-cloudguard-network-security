# Check Point CloudGuard Virtual WAN Module - Existing Hub

This Terraform module deploys Check Point CloudGuard Network Security Virtual WAN NVA solution into an existing vWAN Hub in Azure.
As part of the deployment the following resources are created:
- Resource groups
- Azure Managed Application:
  - NVA
  - Managed identity

For additional information,
please see the [CloudGuard Network for Azure Virtual WAN Deployment Guide](https://sc1.checkpoint.com/documents/IaaS/WebAdminGuides/EN/CP_CloudGuard_Network_for_Azure_vWAN/Default.htm)

## Usage
Follow best practices for using CGNS modules on [the root page](https://registry.terraform.io/modules/CheckPointSW/cloudguard-network-security/azure/latest).

**Example:**
```
provider "azurerm" {
  features {}
}

module "example_module" {

        source  = "CheckPointSW/cloudguard-network-security/azure//modules/nva_into_existing_hub"
        version = "1.0.4"

        authentication_method           = "Service Principal"
        client_secret                   = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
        client_id                       = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
        tenant_id                       = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
        subscription_id                 = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
        resource-group-name             = "tf-managed-app-resource-group"
        location                        = "westcentralus"
        vwan-hub-name                   = "tf-vwan-hub"
        vwan-hub-resource-group         = "tf-vwan-hub-rg"
        managed-app-name                = "tf-vwan-managed-app-nva"
        nva-rg-name                     = "tf-vwan-nva-rg"
        nva-name                        = "tf-vwan-nva"
        os-version                      = "R8120"
        license-type                    = "Security Enforcement (NGTP)"
        scale-unit                      = "2"
        bootstrap-script                = "touch /home/admin/bootstrap.txt; echo 'hello_world' > /home/admin/bootstrap.txt"
        admin-shell                     = "/etc/cli.sh"
        sic-key                         = "xxxxxxxxxxxx"
        admin_SSH_key                   = "ssh-rsa xxxxxxxxxxxxxxxxxxxxxxxx imported-openssh-key"
        bgp-asn                         = "64512"
        custom-metrics                  = "yes"
        routing-intent-internet-traffic = "yes"
        routing-intent-private-traffic  = "yes"
        smart1-cloud-token-a            = ""
        smart1-cloud-token-b            = ""
        smart1-cloud-token-c            = ""
        smart1-cloud-token-d            = ""
        smart1-cloud-token-e            = ""   
        existing-public-ip              = ""
        new-public-ip                   = "yes"
}
```
  
## Known limitations
1. 'terraform destroy' doesn't work if routing-intent is configured. To destroy the deployment, the routing-intent should be deleted manually first. 

### Module's variables:

| Name | Description | Type | Allowed values |
|------|-------------|------|----------------|
| **authentication_method** | The authentication method used to deploy the solution | string | "Service Principal";<br/>"Azure CLI". |
| **client_secret** | The client secret value of the Service Principal used to deploy the solution | string | |
| **client_id** | The client ID of the Service Principal used to deploy the solution | string | |
| **tenant_id** | The tenant ID of the Service Principal used to deploy the solution | string | |
| **subscription_id** | The subscription ID is used to pay for Azure cloud services | string | |
| **resource-group-name** | The name of the resource group that will contain the managed application | string | Resource group names only allow alphanumeric characters, periods, underscores, hyphens and parenthesis and cannot end in a period.<br/>**Default:** "tf-managed-app-resource-group" |
| **location** | The region where the resources will be deployed at | string | The full list of supported Azure regions can be found at https://learn.microsoft.com/en-us/azure/virtual-wan/virtual-wan-locations-partners#locations.<br/>**Default:** "westcentralus" |
| **vwan-hub-name** | The name of the virtual WAN hub that will be created | string | The name must begin with a letter or number, end with a letter, number or underscore, and may contain only letters, numbers, underscores, periods, or hyphens. |
| **vwan-hub-resource-group** | The virtual WAN hub resource group name | string | |
| **managed-app-name** | The name of the managed application that will be created | string | The name must begin with a letter or number, end with a letter, number or underscore, and may contain only letters, numbers, underscores, periods, or hyphens.<br/>**Default:** "tf-vwan-managed-app-nva" |
| **nva-name** | The name of the NVA that will be created | string | The name must begin with a letter or number, end with a letter, number or underscore, and may contain only letters, numbers, underscores, periods, or hyphens.<br/>**Default:** "tf-vwan-nva" |
| **nva-rg-name** | The name of the resource group that will contain the NVA | string | Resource group names only allow alphanumeric characters, periods, underscores, hyphens and parenthesis and cannot end in a period.<br/>**Default:** "tf-vwan-nva-rg" |
| **os-version** | The GAIA os version | string | "R8110";<br/>"R8120";<br/>"R82".<br/>**Default:** "R8120" |
| **license-type** | The Check Point licence type | string | "Security Enforcement (NGTP)";<br/>"Full Package (NGTX + S1C)";<br/>"Full Package Premium (NGTX + S1C++)".<br/>**Default:** "Security Enforcement (NGTP)" |
| **scale-unit** | The scale unit determines the size and number of resources deployed. The higher the scale unit, the greater the amount of traffic that can be handled | string | "2";<br/>"4";<br/>"10";<br/>"20";<br/>"30";<br/>"60";<br/>"80".<br/>**Default:** "2" |
| **bootstrap_script** | An optional script to run on the initial boot | string | Bootstrap script example:<br/>"touch /home/admin/bootstrap.txt; echo 'hello_world' > /home/admin/bootstrap.txt".<br/>The script will create bootstrap.txt file in the /home/admin/ and add 'hello word' string into it.<br/>**Default:** "" |
| **admin_shell** | Enables to select different admin shells | string | /etc/cli.sh;<br/>/bin/bash;<br/>/bin/csh;<br/>/bin/tcsh.<br/>**Default:** "/etc/cli.sh" |
| **sic-key** | The Secure Internal Communication one time secret used to set up trust between the gateway object and the management server | string | Only alphanumeric characters are allowed, and the value must be 12-30 characters long. |
| **admin_SSH_key** | The public ssh key used for ssh connection to the NVA GW instances | string | ssh-rsa xxxxxxxxxxxxxxxxxxxxxxxx generated-by-azure. |
| **bgp-asn** | The BGP autonomous system number | string | 64512.<br/>**Default:** "64512" |
| **custom-metrics** | Indicates whether CloudGuard Metrics will be use for gateway monitoring | string | yes;<br/>no.<br/>**Default:** "yes" |
| **routing-intent-internet-traffic** | Set routing intent policy to allow internet traffic through the new nva | string | yes;<br/>no.<br/>Please verify routing-intent is configured successfully post-deployment.<br/>**Default:** "yes" |
| **routing-intent-private-traffic** | Set routing intent policy to allow private traffic through the new nva | string | yes;<br/>no.<br/>Please verify routing-intent is configured successfully post-deployment.<br/>**Default:** "yes" |
| **smart1-cloud-token-a** | Smart-1 Cloud token to connect automatically ***NVA instance a*** to Check Point's Security Management as a Service.<br/><br/>Follow these instructions to quickly connect this member to Smart-1 Cloud - [SK180501](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk180501) | string | A valid token copied from the Connect Gateway screen in Smart-1 Cloud portal. |
| **smart1-cloud-token-b** | Smart-1 Cloud token to connect automatically ***NVA instance b*** to Check Point's Security Management as a Service.<br/><br/>Follow these instructions to quickly connect this member to Smart-1 Cloud - [SK180501](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk180501) | string | A valid token copied from the Connect Gateway screen in Smart-1 Cloud portal. |
| **smart1-cloud-token-c** | Smart-1 Cloud token to connect automatically ***NVA instance c*** to Check Point's Security Management as a Service.<br/><br/>Follow these instructions to quickly connect this member to Smart-1 Cloud - [SK180501](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk180501) | string | A valid token copied from the Connect Gateway screen in Smart-1 Cloud portal. |
| **smart1-cloud-token-d** | Smart-1 Cloud token to connect automatically ***NVA instance d*** to Check Point's Security Management as a Service.<br/><br/>Follow these instructions to quickly connect this member to Smart-1 Cloud - [SK180501](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk180501) | string | A valid token copied from the Connect Gateway screen in Smart-1 Cloud portal. |
| **smart1-cloud-token-e** | Smart-1 Cloud token to connect automatically ***NVA instance e*** to Check Point's Security Management as a Service.<br/><br/>Follow these instructions to quickly connect this member to Smart-1 Cloud - [SK180501](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk180501) | string | A valid token copied from the Connect Gateway screen in Smart-1 Cloud portal. |




