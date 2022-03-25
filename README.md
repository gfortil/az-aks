# Azure - HPCC AKS Root Module
<br>

This module is intended as an example for development and test systems only. It can be used as a blueprint to develop your own production version that meets your organization's security requirements.
<br>
<br>

## Introduction

This module deploys an HPCC AKS cluster using remote modules that are listed below.
<br>

## Remote Modules
These are the list of all the remote modules.

| Name                 | Description                                                              | URL                                                                        | Required |
| -------------------- | ------------------------------------------------------------------------ | -------------------------------------------------------------------------- | :------: |
| subscription         | Queries enabled azure subscription from host machine                     | https://github.com/Azure-Terraform/terraform-azurerm-subscription-data.git |   yes    |
| naming               | Enforces naming conventions                                              | -                                                                          |   yes    |
| metadata             | Provides metadata                                                        | https://github.com/Azure-Terraform/terraform-azurerm-metadata.git          |   yes    |
| resource_group       | Creates a resource group                                                 | https://github.com/Azure-Terraform/terraform-azurerm-resource-group.git    |   yes    |
| virtual_network      | Creates a virtual network                                                | https://github.com/Azure-Terraform/terraform-azurerm-virtual-network.git   |   yes    |
| cheapest_spot_region | Returns the region name with the cheapest instance based on a given size | https://github.com/gfortil/terraform-azurerm-cheapest-region.git           |    no    |
| kubernetes           | Creates an Azure Kubernetes Service Cluster                              | https://github.com/Azure-Terraform/terraform-azurerm-kubernetes.git        |   yes    |
<br>

## Providers

| Name       | Version   |
| ---------- | --------- |
| azurerm    | >= 2.57.0 |
| random     | ~>3.1.0   |
| kubernetes | ~>2.2.0   |
<br>

## Supported Arguments
<br>

### The `admin` block:
This block contains information on the user who is deploying the cluster. This is used as tags and part of some resource names to identify who deployed a given resource and how to contact that user. This block is required.

| Name  | Description                  | Type   | Default | Required |
| ----- | ---------------------------- | ------ | ------- | :------: |
| name  | Name of the admin.           | string | -       |   yes    |
| email | Email address for the admin. | string | -       |   yes    |

<br>
Usage Example:
<br>

    admin = {
        name  = "Shuri"
        email = "shuri@blackgirlscode.com"
    }

<br>
### The `disable_naming_conventions` block:
When set to `true`, this attribute drops the naming conventions set forth by the python module. This attribute is optional.

 | Name                       | Description                 | Type | Default | Required |
 | -------------------------- | --------------------------- | ---- | ------- | :------: |
 | disable_naming_conventions | Disable naming conventions. | bool | `false` |    no    |
<br>

### The `metadata` block:
TThe arguments in this block are used as tags and part of resources’ names. This block can be omitted when disable_naming_conventions is set to `true`.

 | Name                | Description                  | Type   | Default | Required |
 | ------------------- | ---------------------------- | ------ | ------- | :------: |
 | project_name        | Name of the project.         | string | ""      |   yes    |
 | product_name        | Name of the product.         | string | hpcc    |    no    |
 | business_unit       | Name of your bussiness unit. | string | ""      |    no    |
 | environment         | Name of the environment.     | string | ""      |    no    |
 | market              | Name of market.              | string | ""      |    no    |
 | product_group       | Name of product group.       | string | ""      |    no    |
 | resource_group_type | Resource group type.         | string | ""      |    no    |
 | sre_team            | Name of SRE team.            | string | ""      |    no    |
 | subscription_type   | Subscription type.           | string | ""      |    no    |
<br>

Usage Example:
<br>

    metadata = {    
        project             = "hpccdemo"
        product_name        = "example"
        business_unit       = "commercial"
        environment         = "sandbox"
        market              = "us"
        product_group       = "contoso"
        resource_group_type = "app"
        sre_team            = "hpccplatform"
        subscription_type   = "dev"
    }

<br>

### The `tags` argument:
The tag attribute can be used for additional tags. The tags must be key value pairs. This block is optional.

 | Name | Description               | Type        | Default | Required |
 | ---- | ------------------------- | ----------- | ------- | :------: |
 | tags | Additional resource tags. | map(string) | admin   |    no    |
<br>

### The `resource_group` block:
This block creates a resource group (like a folder) for your resources. This block is required.

 | Name        | Description                                                       | Type | Default | Required |
 | ----------- | ----------------------------------------------------------------- | ---- | ------- | :------: |
 | unique_name | Will concatenate a number at the end of your resource group name. | bool | `true`  |   yes    |
<br>

Usage Example:
<br>

    resource_group = {
        unique_name = true
    }

<br>

### The `virtual_network` block:
This block imports metadata of a virtual network deployed outside of this project. This block is optional.

 | Name              | Description                             | Type   | Default | Required |
 | ----------------- | --------------------------------------- | ------ | ------- | :------: |
 | private_subnet_id | The ID of the private subnet.           | string | -       |   yes    |
 | public_subnet_id  | The ID of the public subnet.            | string | -       |   yes    |
 | route_table_id    | The ID  of the route table for the AKS. | string | -       |   yes    |
 | location          | The location of the virtual network     | string | -       |   yes    |
<br>

Usage Example:
<br>

    virtual_network = {
        private_subnet_id = ""
        public_subnet_id  = ""
        route_table_id    = ""
        location          = ""
    }

<br>

## The `node_pools` block:
The `node-pools` block supports the following arguments:<br>
`system` - (Required) The system or default node pool. This node pool hosts the system pods by default. The possible arguments for this block are defined below. 

`addpool` - (Required) The additional node pool configuration. This block name is changeable and must be unique across all additional node pools. At least one additional node pool is required. The possible arguments for this block are defined below.

### The `system` block:
This block creates a system node pool. This block is required.

| Name                        | Optional, Required | Description                                                                                                                                                                                                                                                                                                               |
| --------------------------- | ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| vm_size                     | Optional           | The size of the Virtual Machine, such as Standard_A4_v2.                                                                                                                                                                                                                                                                  |
| node_count                  | Optional           | The initial number of nodes which should exist in this Node Pool. If specified this must be between 1 and 1000 and between min_count and max_count.                                                                                                                                                                       |
| enable_auto_scalling        | Optional           | Should the Kubernetes Auto Scaler be enabled for this Node Pool? Defaults to false.                                                                                                                                                                                                                                       |
| min_count                   | Optional           | The minimum number of nodes which should exist in this Node Pool. If specified this must be between 1 and 1000. Can only be set when enable_auto_scalling is set to true.                                                                                                                                                 |
| max_count                   | Optional           | The maximum number of nodes which should exist in this Node Pool. If specified this must be between 1 and 1000. Can only be set when enable_auto_scalling is set to true.                                                                                                                                                 |
| availability_zones          | Optional           | A list of Availability Zones across which the Node Pool should be spread. Changing this forces a new resource to be created.                                                                                                                                                                                              |
| enable_host_encryption      | Optional           | Should the nodes in the Default Node Pool have host encryption enabled? Defaults to false. Can only be enabled on new node pools. Requires VirtualMachineScaleSets as VM type. Can only be enabled in Azure regions that support server-side encryption of Azure managed disks and only with specific supported VM sizes. |
| enable_node_public_ip       | Optional           | Should nodes in this Node Pool have a Public IP Address? Defaults to false.                                                                                                                                                                                                                                               |
| max_pods                    | Optional           | The maximum number of pods that can run on each agent.                                                                                                                                                                                                                                                                    |
| node_labels                 | Optional           | A map of Kubernetes labels which should be applied to nodes in the Default Node Pool.                                                                                                                                                                                                                                     |
| only_critical_addons_enable | Optional           | Enabling this option will taint default node pool with CriticalAddonsOnly=true:NoSchedule taint. When set to true, only system pods will be scheduled on the system node pool.                                                                                                                                            |
| orchestrator_version        | Optional           | Version of Kubernetes used for the Agents. If not specified, the latest recommended version will be used at provisioning time (but won't auto-upgrade).                                                                                                                                                                   |
| os_disk_size_gb             | Optional           | The size of the OS Disk which should be used for each agent in the Node Pool.                                                                                                                                                                                                                                             |
| os_disk_type                | Optional           | The type of disk which should be used for the Operating System. Possible values are Ephemeral and Managed. Defaults to Managed.                                                                                                                                                                                           |
| type                        | Optional           | The type of Node Pool which should be created. Possible values are AvailabilitySet and VirtualMachineScaleSets. Defaults to VirtualMachineScaleSets.                                                                                                                                                                      |
| tags                        | Optional           | A mapping of tags to assign to the Node Pool.                                                                                                                                                                                                                                                                             |
| subnet                      | Optional           | The ID of a Subnet where the Kubernetes Node Pool should exist.                                                                                                                                                                                                                                                           |
<br>

### The `addpool` block:
This block creates additional node pools. This block is optional. 

| Name                         | Optional, Required | Description                                                                                                                                                                                                                                                                                                               |
| ---------------------------- | ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| node_taints                  | Optional           | A list of Kubernetes taints which should be applied to nodes in the agent pool (e.g key=value:NoSchedule). Changing this forces a new resource to be created.                                                                                                                                                             |
| max_surge                    | Required           | The maximum number or percentage of nodes which will be added to the Node Pool size during an upgrade.                                                                                                                                                                                                                    |
| eviction_policy              | Optional           | The Eviction Policy which should be used for Virtual Machines within the Virtual Machine Scale Set powering this Node Pool. Possible values are Deallocate and Delete. Will only be used when priority is set to Spot. Changing this forces a new resource to be created.                                                 |
| os_type                      | Optional           | The Operating System which should be used for this Node Pool. Changing this forces a new resource to be created. Possible values are Linux and Windows. Defaults to Linux.                                                                                                                                                |
| priority                     | Optional           | The Priority for Virtual Machines within the Virtual Machine Scale Set that powers this Node Pool. Possible values are Regular and Spot. Defaults to Regular. Changing this forces a new resource to be created.                                                                                                          |
| proximity_placement_group_id | Optional           | The ID of the Proximity Placement Group where the Virtual Machine Scale Set that powers this Node Pool will be placed. Changing this forces a new resource to be created.                                                                                                                                                 |
| spot_max_price               | Optional           | The maximum price you're willing to pay in USD per Virtual Machine. Valid values are -1 (the current on-demand price for a Virtual Machine) or a positive value with up to five decimal places. Changing this forces a new resource to be created.                                                                        |
| vm_size                      | Optional           | The size of the Virtual Machine, such as Standard_A4_v2.                                                                                                                                                                                                                                                                  |
| node_count                   | Optional           | The initial number of nodes which should exist in this Node Pool. If specified this must be between 1 and 1000 and between min_count and max_count.                                                                                                                                                                       |
| enable_auto_scalling         | Optional           | Should the Kubernetes Auto Scaler be enabled for this Node Pool? Defaults to false.                                                                                                                                                                                                                                       |
| min_count                    | Optional           | The minimum number of nodes which should exist in this Node Pool. If specified this must be between 1 and 1000. Can only be set when enable_auto_scalling is set to true.                                                                                                                                                 |
| max_count                    | Optional           | The maximum number of nodes which should exist in this Node Pool. If specified this must be between 1 and 1000. Can only be set when enable_auto_scalling is set to true.                                                                                                                                                 |
| availability_zones           | Optional           | A list of Availability Zones across which the Node Pool should be spread. Changing this forces a new resource to be created.                                                                                                                                                                                              |
| enable_host_encryption       | Optional           | Should the nodes in the Default Node Pool have host encryption enabled? Defaults to false. Can only be enabled on new node pools. Requires VirtualMachineScaleSets as VM type. Can only be enabled in Azure regions that support server-side encryption of Azure managed disks and only with specific supported VM sizes. |
| enable_node_public_ip        | Optional           | Should nodes in this Node Pool have a Public IP Address? Defaults to false.                                                                                                                                                                                                                                               |
| max_pods                     | Optional           | The maximum number of pods that can run on each agent.                                                                                                                                                                                                                                                                    |
| node_labels                  | Optional           | A map of Kubernetes labels which should be applied to nodes in the Default Node Pool.                                                                                                                                                                                                                                     |
| only_critical_addons_enable  | Optional           | Enabling this option will taint default node pool with CriticalAddonsOnly=true:NoSchedule taint. When set to true, only system pods will be scheduled on the system node pool.                                                                                                                                            |
| orchestrator_version         | Optional           | Version of Kubernetes used for the Agents. If not specified, the latest recommended version will be used at provisioning time (but won't auto-upgrade).                                                                                                                                                                   |
| os_disk_size_gb              | Optional           | The size of the OS Disk which should be used for each agent in the Node Pool.                                                                                                                                                                                                                                             |
| os_disk_type                 | Optional           | The type of disk which should be used for the Operating System. Possible values are Ephemeral and Managed. Defaults to Managed.                                                                                                                                                                                           |
| type                         | Optional           | The type of Node Pool which should be created. Possible values are AvailabilitySet and VirtualMachineScaleSets. Defaults to VirtualMachineScaleSets.                                                                                                                                                                      |
| tags                         | Optional           | A mapping of tags to assign to the Node Pool.                                                                                                                                                                                                                                                                             |
| subnet                       | Optional           | The ID of a Subnet where the Kubernetes Node Pool should exist.                                                                                                                                                                                                                                                           |
<br>

Usage Example:
<br>

    node_pools = {
        system = {
            vm_size                      = "Standard_D4_v4"
            node_count                   = 1
            enable_auto_scaling          = true
            only_critical_addons_enabled = true
            min_count                    = 1
            max_count                    = 1
            availability_zones           = []
            subnet                       = "private"
            enable_host_encryption       = false
            enable_node_public_ip        = false
            os_disk_type                 = "Managed"
            type                         = "VirtualMachineScaleSets"
            # max_pods             = 10
            # node_labels          = {"engine" = "roxie", "engine" = "roxie"}
            # orchestrator_version = "2.9.0"
            # os_disk_size_gb      = 100
            # tags                 = {"mynodepooltag1" = "mytagvalue1","mynodepooltag2" = "mytagvalue2"}

        }

        addpool1 = {
            vm_size                      = "Standard_D4_v4"
            enable_auto_scaling          = true
            node_count                   = 2
            min_count                    = 1
            max_count                    = 2
            availability_zones           = []
            subnet                       = "public"
            priority                     = "Regular"
            spot_max_price               = -1
            max_surge                    = "1"
            os_type                      = "Linux"
            priority                     = "Regular"
            enable_host_encryption       = false
            enable_node_public_ip        = false
            only_critical_addons_enabled = false
            os_disk_type                 = "Managed"
            type                         = "VirtualMachineScaleSets"
            # orchestrator_version         = "2.9.0"
            # os_disk_size_gb              = 100
            # max_pods                     = 20
            # node_labels                  = {"engine" = "roxie", "engine" = "roxie"}
            # eviction_policy              = "Spot"
            # node_taints                  = ["mytaint1", "mytaint2"]
            # proximity_placement_group_id = "my_proximity_placement_group_id"
            # spot_max_price               = 1
            # tags                         = {"mynodepooltag1" = "mytagvalue1","mynodepooltag2" = "mytagvalue2"}
        }

        addpool2 = {
            vm_size                      = "Standard_D4_v4"
            enable_auto_scaling          = true
            node_count                   = 2
            min_count                    = 1
            max_count                    = 2
            availability_zones           = []
            subnet                       = "public"
            priority                     = "Regular"
            spot_max_price               = -1
            max_surge                    = "1"
            os_type                      = "Linux"
            priority                     = "Regular"
            enable_host_encryption       = false
            enable_node_public_ip        = false
            only_critical_addons_enabled = false
            os_disk_type                 = "Managed"
            type                         = "VirtualMachineScaleSets"
            # orchestrator_version         = "2.9.0"
            # os_disk_size_gb              = 100
            # max_pods                     = 20
            # node_labels                  = {"engine" = "roxie", "engine" = "roxie"}
            # eviction_policy              = "Spot"
            # node_taints                  = ["mytaint1", "mytaint2"]
            # proximity_placement_group_id = "my_proximity_placement_group_id"
            # spot_max_price               = 1
            # tags                         = {"mynodepooltag1" = "mytagvalue1","mynodepooltag2" = "mytagvalue2"}
        }
    }

# CHARTS 
# .......................
<br>

### The `disable_helm` argument:
This block disable helm deployments by Terraform. This block is optional and will stop HPCC from being installed.

 | Name         | Description                            | Type | Default | Required |
 | ------------ | -------------------------------------- | ---- | ------- | :------: |
 | disable_helm | Disable Helm deployments by Terraform. | bool | `false` |    no    |
<br>

### The `hpcc` block:
This block deploys the HPCC helm chart. This block is optional.

 | Name                       | Description                                                                                                                                       | Type         | Default                        | Required |
 | -------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ | ------------------------------ | :------: |
 | chart                      | Path to local chart directory name. Examples: ~/HPCC-Platform/helm/hpcc                                                                           | string       | null                           |    no    |
 | namespace                  | Namespace to use.                                                                                                                                 | string       | default                        |    no    |
 | name                       | Release name of the chart.                                                                                                                        | string       | myhpcck8s                      |    no    |
 | values                     | List of desired state files to use similar to -f in CLI.                                                                                          | list(string) | values-retained-azurefile.yaml |    no    |
 | version                    | Version of the HPCC chart.                                                                                                                        | string       | latest                         |   yes    |
 | image_root                 | Image root to use.                                                                                                                                | string       | hpccsystems                    |    no    |
 | image_name                 | Image name to use.                                                                                                                                | string       | platform-core                  |    no    |
 | atomic                     | If set, installation process purges chart on fail. The `wait` flag will be set automatically if `atomic` is used.                                 | bool         | false                          |    no    |
 | recreate_pods              | Perform pods restart during upgrade/rollback.                                                                                                     | bool         | false                          |    no    |
 | reuse_values               | When upgrading, reuse the last release's values and merge in any overrides. If `reset_values` is specified, this is ignored.                      | bool         | false                          |    no    |
 | reset_values               | When upgrading, reset the values to the ones built into the chart.                                                                                | bool         | false                          |    no    |
 | force_update               | Force resource update through delete/recreate if needed.                                                                                          | bool         | false                          |    no    |
 | cleanup_on_fail            | Allow deletion of new resources created in this upgrade when upgrade fails.                                                                       | bool         | false                          |    no    |
 | disable_openapi_validation | If set, the installation process will not validate rendered templates against the Kubernetes OpenAPI Schema.                                      | bool         | false                          |    no    |
 | max_history                | Maximum number of release versions stored per release.                                                                                            | number       | 0                              |    no    |
 | wait                       | Will wait until all resources are in a ready state before marking the release as successful. It will wait for as long as `timeout` .              | bool         | true                           |    no    |
 | dependency_update          | Runs helm dependency update before installing the chart.                                                                                          | bool         | false                          |    no    |
 | timeout                    | Time in seconds to wait for any individual kubernetes operation (like Jobs for hooks).                                                            | number       | 900                            |    no    |
 | wait_for_jobs              | If wait is enabled, will wait until all Jobs have been completed before marking the release as successful. It will wait for as long as `timeout`. | bool         | false                          |    no    |
 | lint                       | Run the helm chart linter during the plan.                                                                                                        | bool         | false                          |    no    |
<br>

 Usage Example:
 <br>

    hpcc = {
        version                    = "8.6.14-rc2"
        name                       = "myhpcck8s"
        atomic                     = true
        recreate_pods              = false
        resuse_values              = false
        reset_values               = false
        force_update               = false
        namespace                  = "default"
        cleanup_on_fail            = false
        disable_openapi_validation = false
        max_history                = 0
        wait                       = true
        dependency_update          = false
        timeout                    = 900
        wait_for_jobs              = false
        lint                       = false
        # chart   = "/Users/shuri/helm-chart/helm/hpcc"
        # values  = ["/Users/shuri/mycustomvalues1.yaml", "/Users/shuri/mycustomvalues2.yaml"]
        # image_root    = "west.blackgirlscode.com"
        # image_name    = "platform-core-ln"
        # image_version = "latest"
    }

<br>

### The `storage` block:
This block deploys the HPCC persistent volumes. This block is required.

 | Name                       | Description                                                                                                                                       | Type         | Default                                                | Valid Options    | Required |
 | -------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ | ------------------------------------------------------ | ---------------- | :------: |
 | default                    | Use AKS provided storage account?                                                                                                                 | bool         | `false`                                                | `true` , `false` |    no    |
 | version                    | The version of the storage chart.                                                                                                                 | string       | 0.1.0                                                  |                  |    no    |
 | chart                      | Absolute path to local chart directory. Examples: ~/HPCC-Platform//helm/examples/azure/hpcc-azurefile                                             | string       | null                                                   | no               |
 | name                       | Release name of the chart.                                                                                                                        | string       | `myhpcck8s`                                            | no               |
 | values                     | List of desired state files to use similar to -f in CLI.                                                                                          | list(string) | []                                                     | no               |
 | storage_account            | The storage account account to use.                                                                                                               | object       | Queries attributes' values from storage_account module | -                |    no    |
 | version                    | Version of the storage chart.                                                                                                                     | string       | 0.1.0                                                  | no               |
 | atomic                     | If set, installation process purges chart on fail. The `wait` flag will be set automatically if `atomic` is used.                                 | bool         | false                                                  | no               |
 | recreate_pods              | Perform pods restart during upgrade/rollback.                                                                                                     | bool         | false                                                  | no               |
 | reuse_values               | When upgrading, reuse the last release's values and merge in any overrides. If `reset_values` is specified, this is ignored.                      | bool         | false                                                  | no               |
 | reset_values               | When upgrading, reset the values to the ones built into the chart.                                                                                | bool         | false                                                  | no               |
 | force_update               | Force resource update through delete/recreate if needed.                                                                                          | bool         | false                                                  | no               |
 | cleanup_on_fail            | Allow deletion of new resources created in this upgrade when upgrade fails.                                                                       | bool         | false                                                  | no               |
 | disable_openapi_validation | If set, the installation process will not validate rendered templates against the Kubernetes OpenAPI Schema.                                      | bool         | false                                                  | no               |
 | max_history                | Maximum number of release versions stored per release.                                                                                            | number       | 0                                                      | no               |
 | wait                       | Will wait until all resources are in a ready state before marking the release as successful. It will wait for as long as `timeout` .              | bool         | true                                                   | no               |
 | dependency_update          | Runs helm dependency update before installing the chart.                                                                                          | bool         | false                                                  | no               |
 | timeout                    | Time in seconds to wait for any individual kubernetes operation (like Jobs for hooks).                                                            | number       | 600                                                    | no               |
 | wait_for_jobs              | If wait is enabled, will wait until all Jobs have been completed before marking the release as successful. It will wait for as long as `timeout`. | bool         | false                                                  | no               |
 | lint                       | Run the helm chart linter during the plan.                                                                                                        | bool         | false                                                  | no               |
<br>

### The `storage_account` block:
This block deploys the HPCC persistent volumes. This block is required.

 | Name                | Description                                                          | Type   | Default                     | Valid Options | Required |
 | ------------------- | -------------------------------------------------------------------- | ------ | --------------------------- | ------------- | :------: |
 | location            | Storage account location                                             | string | -                           | -             |    no    |
 | name                | Release name of the chart.                                           | string | `myhpcck8s`                 | yes           |
 | resource_group_name | The name of the resource group in which the storage account belongs. | string | -                           | -             |   yes    |
 | subscription_id     | The ID of the subscription in which the storage account belongs.     | string | Admin's active subscription | -             |    no    |
<br>

Usage Example:
<br>

    storage = {
        default                    = false
        version                    = "0.1.0"
        atomic                     = true
        recreate_pods              = false
        resuse_values              = false
        reset_values               = false
        force_update               = false
        namespace                  = "default"
        cleanup_on_fail            = false
        disable_openapi_validation = false
        max_history                = 0
        wait                       = true
        dependency_update          = false
        timeout                    = 600
        wait_for_jobs              = false
        lint                       = false
        # chart  = "/Users/shuri/helm-chart/helm/example/azure/hpcc-azurefile"
        # values = ["/Users/shuri/mycustomvalues1.yaml", "/Users/shuri/mycustomvalues2.yaml"]
        /*
        storage_account = {
            location            = "eastus"
            name                = "shurihpccsa3"
            resource_group_name = "app-storageaccount-sandbox-eastus-48936"
            # subscription_id   = "value"
        }
        */
    }

<br>

### The `elastic4hpcclogs` block:
This block deploys the elastic4hpcclogs chart. This block is optional.

 | Name                       | Description                                                                                                                                       | Type         | Default            | Required |
 | -------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ | ------------------ | :------: |
 | chart                      | Path to local chart directory name. Examples: ./HPCC-Platform//helm/managed/logging/elastic                                                       | string       | null               |    no    |
 | enable                     | Enable elastic4hpcclogs                                                                                                                           | bool         | `true`             |    no    |
 | name                       | Release name of the chart.                                                                                                                        | string       | myelastic4hpcclogs |    no    |
 | version                    | The version of the elastic4hpcclogs                                                                                                               | string       | 1.2.8              |          | no |
 | values                     | List of desired state files to use similar to -f in CLI.                                                                                          | list(string) | -                  |    no    |
 | version                    | Version of the elastic4hpcclogs chart.                                                                                                            | string       | 1.2.1              |    no    |
 | atomic                     | If set, installation process purges chart on fail. The `wait` flag will be set automatically if `atomic` is used.                                 | bool         | false              |    no    |
 | recreate_pods              | Perform pods restart during upgrade/rollback.                                                                                                     | bool         | false              |    no    |
 | reuse_values               | When upgrading, reuse the last release's values and merge in any overrides. If `reset_values` is specified, this is ignored.                      | bool         | false              |    no    |
 | reset_values               | When upgrading, reset the values to the ones built into the chart.                                                                                | bool         | false              |    no    |
 | force_update               | Force resource update through delete/recreate if needed.                                                                                          | bool         | false              |    no    |
 | cleanup_on_fail            | Allow deletion of new resources created in this upgrade when upgrade fails.                                                                       | bool         | false              |    no    |
 | disable_openapi_validation | If set, the installation process will not validate rendered templates against the Kubernetes OpenAPI Schema.                                      | bool         | false              |    no    |
 | max_history                | Maximum number of release versions stored per release.                                                                                            | number       | 0                  |    no    |
 | wait                       | Will wait until all resources are in a ready state before marking the release as successful. It will wait for as long as `timeout` .              | bool         | true               |    no    |
 | dependency_update          | Runs helm dependency update before installing the chart.                                                                                          | bool         | false              |    no    |
 | timeout                    | Time in seconds to wait for any individual kubernetes operation (like Jobs for hooks).                                                            | number       | 900                |    no    |
 | wait_for_jobs              | If wait is enabled, will wait until all Jobs have been completed before marking the release as successful. It will wait for as long as `timeout`. | bool         | false              |    no    |
 | lint                       | Run the helm chart linter during the plan.                                                                                                        | bool         | false              |    no    |

<br>

Usage Example:
<br>

    elastic4hpcclogs = {
        enable                     = true
        name                       = "myelastic4hpcclogs"
        atomic                     = true
        recreate_pods              = false
        reuse_values              = false
        reset_values               = false
        force_update               = false
        namespace                  = "default"
        cleanup_on_fail            = false
        disable_openapi_validation = false
        max_history                = 0
        wait                       = true
        dependency_update          = false
        timeout                    = 900
        wait_for_jobs              = false
        lint                       = false
        timeout                    = 900
        # chart  = "/Users/shuri/helm-chart/helm/managed/logging/elastic"
        # values = ["/Users/shuri/mycustomvalues1.yaml","/Users/shuri/mycustomvalues2.yaml"]
    }

<br>

The `hpcc`, `storage` and `elastic4hpcclogs` blocks also support the following arguments:
<br>

| Name                       | Optional, Required | Description                                                                                                                                                               |
| -------------------------- | ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| atomic                     | Optional           | If set, installation process purges chart on fail. The wait flag will be set automatically if atomic is used. Defaults to false.                                          |
| recreate_pods              | Optional           | Perform pods restart during upgrade/rollback. Defaults to false.                                                                                                          |
| cleanup_on_fail            | Optional           | Allow deletion of new resources created in this upgrade when upgrade fails. Defaults to false.                                                                            |
| disable_openapi_validation | Optional           | If set, the installation process will not validate rendered templates against the Kubernetes OpenAPI Schema. Defaults to false.                                           |
| wait                       | Optional           | Will wait until all resources are in a ready state before marking the release as successful. It will wait for as long as timeout. Defaults to false.                      |
| dependency_update          | Optional           | Runs helm dependency update before installing the chart. Defaults to false.                                                                                               |
| timeout                    | Optional           | Time in seconds to wait for any individual Kubernetes operation (like Jobs for hooks). Defaults to 900 seconds for hpcc and 600 seconds for storage and elastic4hpcclogs. |
| wait_for_jobs              | Optional           | If wait is enabled, will wait until all Jobs have been completed before marking the release as successful. It will wait for as long as timeout. Defaults to false.        |
| lint                       | Optional           | Run the helm chart linter during the plan. Defaults to false.                                                                                                             |
<br>


### The `auto_connect` argument:
This block automatically connect your cluster to your local machine similarly to `az aks get-credentials`.

 | Name         | Description                                                                                               | Type | Default | Required |
 | ------------ | --------------------------------------------------------------------------------------------------------- | ---- | ------- | :------: |
 | auto_connect | Automatically connect to the Kubernetes cluster from the host machine by overwriting the current context. | bool | `false` |    no    |
<br>

### The `auto_connect` argument:
This block automatically launch the ECLWatch interface.

 | Name                 | Description                                  | Type | Default | Required |
 | -------------------- | -------------------------------------------- | ---- | ------- | :------: |
 | auto_launch_eclwatch | Automatically launch the ECLWatch interface. | bool | `false` |    no    |
<br>

### The `expose_services` argument:
Expose ECLWatch and elastic4hpcclogs to the internet. This is unsafe and may not be supported by your organization. Setting this to `true` can cause eclwatch service to stick in a pending state.

 | Name            | Description                                           | Type | Default | Required |
 | --------------- | ----------------------------------------------------- | ---- | ------- | :------: |
 | expose_services | Expose ECLWatch and elastic4hpcclogs to the internet. | bool | `false` |    no    |
<br>


## Outputs

| Name            | Description                                                                                                                                                          |
| --------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| aks_login       | Get access credentials for the managed Kubernetes cluster.                                                                                                           |
| recommendations | A list of security and cost recommendations for this deployment. Your environment has to have been deployed for several hours before Azure provides recommendations. |
<br>

## Usage
### Deploy the Virtual Network Module
<ol>
<li> 

Clone this repo: `git clone https://github.com/gfortil/terraform-azurerm-hpcc.git`. </li>

<li>Linux and MacOS</li>
<ol>
<li> 

Change directory to terraform-azurerm-hpcc/modules/virtual_network: `cd terraform-azurerm-hpcc/modules/virtual_network` </li>
<li> 

Copy examples/admin.tfvars to terraform-azurerm-hpcc/modules/virtual_network: `cp examples/admin.tfvars .` </li>
</ol>
<li>Windows OS</li>
<ol>
<li> 
    
Change directory to terraform-azurerm-hpcc/modules/virtual_network: `cd terraform-azurerm-hpcc/modules/virtual_network` </li>
<li> 

Copy examples/admin.tfvars to terraform-azurerm-hpcc/modules/virtual_network: `copy examples\admin.tfvars .` </li>
</ol>
<li> 

Open `terraform-azurerm-hpcc/modules/virtual_network/admin.tfvars` file. </li>
<li> 

Set attributes to your preferred values. </li>
<li> 

Save `terraform-azurerm-hpcc/modules/virtual_network/admin.tfvars` file. </li>
<li> 

Run `terraform init`. This step is only required before your first `terraform apply`. </li>
<li> 

Run `terraform apply -var-file=admin.tfvars` or `terraform apply -var-file=admin.tfvars -auto-approve`. </li>
<li> 

Type `yes` if you didn't pass the flag `-auto-approve`. </li>
</ol>

### Deploy the Storage Account Module
<ol>
<li>Linux and MacOS</li>
<ol>
<li> 

Change directory to terraform-azurerm-hpcc/modules/storage_account: `cd terraform-azurerm-hpcc/modules/storage_account` </li>
<li> 

Copy examples/admin.tfvars to terraform-azurerm-hpcc/modules/storage_account: `cp examples/admin.tfvars .` </li>
</ol>
<li>Windows OS</li>
<ol>
<li> 
    
Change directory to terraform-azurerm-hpcc/modules/storage_account: `cd terraform-azurerm-hpcc/modules/storage_account` </li>
<li> 

Copy examples/admin.tfvars to terraform-azurerm-hpcc/modules/storage_account: `copy examples\admin.tfvars .` </li>
</ol>
<li> 

Open `terraform-azurerm-hpcc/modules/storage_account/admin.tfvars` file. </li>
<li> 

Set attributes to your preferred values. </li>
<li> 

Save `terraform-azurerm-hpcc/modules/storage_account/admin.tfvars` file. </li>
<li> 

Run `terraform init`. This step is only required before your first `terraform apply`. </li>
<li> 

Run `terraform apply -var-file=admin.tfvars` or `terraform apply -var-file=admin.tfvars -auto-approve`. </li>
<li> 

Type `yes` if you didn't pass the flag `-auto-approve`. </li>
</ol>

### Deploy the AKS Module
<ol>
<li>Linux and MacOS</li>
<ol>
<li> 

Change directory to terraform-azurerm-hpcc: `cd terraform-azurerm-hpcc` </li>
<li> 

Copy examples/admin.tfvars to terraform-azurerm-hpcc: `cp examples/admin.tfvars .` </li>
</ol>
<li>Windows OS</li>
<ol>
<li> 
    
Change directory to terraform-azurerm-hpcc: `cd terraform-azurerm-hpcc` </li>
<li> 

Copy examples/admin.tfvars to terraform-azurerm-hpcc: `copy examples\admin.tfvars .` </li>
</ol>
<li> 

Open `terraform-azurerm-hpcc/admin.tfvars` file. </li>
<li> 

Set attributes to your preferred values. </li>
<li> 

Save `terraform-azurerm-hpcc/admin.tfvars` file. </li>
<li> 

Run `terraform init`. This step is only required before your first `terraform apply`. </li>
<li> 

Run `terraform apply -var-file=admin.tfvars` or `terraform apply -var-file=admin.tfvars -auto-approve`. </li>
<li> 

Type `yes` if you didn't pass the flag `-auto-approve`. </li>
<li>

If `auto_connect = true` (in admin.tfvars), skip this step. </li>
<ol>
<li> 

Copy aks_login command. </li>
<li> 

Run aks_login in your command line. </li>
<li> 

Accept to overwrite your current context. </li>
</ol>
<li> 

List pods: `kubectl get pods`. </li>
<li> 

Get ECLWatch external IP: `kubectl get svc --field-selector metadata.name=eclwatch | awk 'NR==2 {print $4}'`. </li>
<li> 

Delete cluster: `terraform destroy -var-file=admin.tfvars` or `terraform destroy -var-file=admin.tfvars -auto-approve`. </li>
<li> 

Type: `yes` if flag `-auto-approve` was not set.</li>
</ol>
