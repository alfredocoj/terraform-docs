# Terraform State Migration in Azure Storage Account

[Terraform](https://www.terraform.io/) allows you to define, visualize and deploy your cloud infrastructure. Using Terraform, you create configuration files using HCL syntax. The HCL syntax allows you to specify the cloud provider, such as Azure, and the elements that make up your cloud infrastructure. After creating the configuration files, you create an execution plan that allows you to preview the infrastructure changes before they are deployed. After verifying the changes, apply the execution plan to deploy the infrastructure.

Terraform state is used to reconcile deployed resources with Terraform configurations. The state lets Terraform know which Azure resources will be added, updated or deleted. More details on this configuration can be found in Store Terraform state in Azure storage.

This solution of storing the state of the terraform in some cloud storage solution is a good practice. In the case of Azure, you can use [Storage Account](https://learn.microsoft.com/en-us/azure/developer/terraform/store-state-in-azure-storage). However, sometimes it may be necessary to migrate to another container and/or a new key. In this case, this documentation can help you with the migration.


# 1. Instruções para Migração de Key do storage account state da Azure.

The project is considered to have the following organization of directories:

```
|-- atlantis.yaml
|-- repos.yaml
|--/DEV
|---- *.tf
|--/HML
|---- *.tf
|--/PROD
|---- *.tf
```

And that each directory has the terraform files that should correspond to the terraform workspaces of `dev`, `hml` and `prod`.

```
# Go to DEV project
cd /DEV

# start terraform project
terraform init

# List the workspaces if you have already created something in remote 
terraform workspace list

# select the dev workspace
terraform workspace select dev

# import the application
terraform import azuread_application.appname /applications/vdc123a5-284d-4989-bc28-2fba1b6a2ac1

# import from the main service
terraform import azuread_service_principal.appname_service_principal /servicePrincipals/x11a6361-6d2a-4f04-a1c3-d2f67fa1234x 

# import the resource group
terraform import azurerm_resource_group.rg_name /subscriptions/12bfb0c3-e041-2fd1-a123-33d4033b5xx9/resourceGroups/rg-us-teste-dev

# import the key vault
terraform import azurerm_key_vault.kv-name /subscriptions/12bfb0c3-e041-2fd1-a123-33d4033b5xx9/resourceGroups/rg-us-teste-dev/providers/Microsoft.KeyVault/vaults/kv-teste-dev

# Import the key vault policy
terraform import azurerm_key_vault_access_policy.kv-policy-secret /subscriptions/12bfb0c3-e041-2fd1-a123-33d4033b5xx9/resourceGroups/rg-us-teste-dev/providers/Microsoft.KeyVault/vaults/kv-teste-dev/objectId/c4h72be9-5a38-485f-85c9-123dcbd123bc

# import the storage account
terraform import azurerm_storage_account.storage_account_name /subscriptions/12bfb0c3-e041-2fd1-a123-33d4033b5xx9/resourceGroups/rg-us-teste-dev/providers/Microsoft.Storage/storageAccounts/testedev

# Run the terraform plan and check that we already have the status synchronized with remote
terraform plan
```

# 2. Troubleshooting

Caso algo aconteça mal, pode-se refazer o processo, mas primeiro será preciso remover o state errado da seguinte forma:

- Listar o recurso salvos no estado atual usando o comando:
```
terraform state list
```
- Pode-se remover o recurso que ficou salvo de forma errada:
```
terraform state rm azurerm_storage_account.storage_account_name
```
- Em seguida pode-se importá-lo novamente.
