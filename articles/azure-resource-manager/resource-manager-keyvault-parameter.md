---
title: Tajný kód trezoru klíčů pomocí šablony Azure Resource Manageru | Dokumentace Microsoftu
description: Ukazuje, jak předat tajného klíče ze služby key vault jako parametr během nasazení.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2018
ms.author: tomfitz
ms.openlocfilehash: 06719f3a92dae805081ea85c346df97ebed0e0dc
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "49078066"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Použití Azure Key Vault k předání zabezpečený parametr. hodnoty během nasazení

Pokud potřebujete předat jako parametr zabezpečenou hodnotu (jako jsou hesla) během nasazení, můžete načíst hodnotu z [Azure Key Vault](../key-vault/key-vault-whatis.md). Načtení hodnoty pomocí odkazu na trezor klíčů a tajný klíč v souboru parametrů. Hodnota se nikdy vystavena, protože pouze odkazujete na jeho ID služby key vault. Trezor klíčů může existovat v jiném předplatném než skupina prostředků, které nasazujete.

## <a name="deploy-a-key-vault-and-secret"></a>Nasazení služby key vault a tajný klíč

Pokud chcete vytvořit trezor klíčů a tajný klíč, pomocí Azure CLI nebo Powershellu. `enabledForTemplateDeployment` je vlastností služby key vault. K nasazení podle modelu Resource Manager, přístup k tajným klíčům v této službě Key Vault `enabledForTemplateDeployment` musí být `true`. 

Následující ukázkový skript Azure Powershellu a Azure CLI ukazuje, jak vytvořit trezor klíčů a tajný klíč.

Pokud používáte Azure CLI, použijte:

```azurecli-interactive
keyVaultName='{your-unique-vault-name}'
resourceGroupName='{your-resource-group-name}'
location='centralus'
userPrincipalName='{your-email-address-associated-with-your-subscription}'

# Create a resource group
az group create --name $resourceGroupName --location $location

# Create a Key Vault
az keyvault create \
  --name $keyVaultName \
  --resource-group $resourceGroupName \
  --location $location \
  --enabled-for-template-deployment true
az keyvault set-policy --upn $userPrincipalName --name $keyVaultName --secret-permissions set delete get list

# Create a secret with the name, vmAdminPassword
password=$(openssl rand -base64 32)
echo $password
az keyvault secret set --vault-name $keyVaultName --name 'vmAdminPassword' --value $password
```

Pokud používáte PowerShell, použijte:

```azurepowershell-interactive
$keyVaultName = "{your-unique-vault-name}"
$resourceGroupName="{your-resource-group-name}"
$location='Central US'
$userPrincipalName='{your-email-address-associated-with-your-subscription}'

New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

New-AzureRmKeyVault `
  -VaultName $keyVaultName `
  -resourceGroupName $resourceGroupName `
  -Location $location `
  -EnabledForTemplateDeployment
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userPrincipalName -PermissionsToSecrets set,delete,get,list

$password = openssl rand -base64 32
echo $password
$secretvalue = ConvertTo-SecureString $password -AsPlainText -Force
Set-AzureKeyVaultSecret -VaultName $keyVaultName -Name "vmAdminPassword" -SecretValue $secretvalue
```

Pokud při spuštění skriptu PowerShell mimo Cloud Shell, použijte místo toho vytvořit heslo následující příkaz:

```powershell
Add-Type -AssemblyName System.Web
[System.Web.Security.Membership]::GeneratePassword(16,3)
```

Použití šablony Resource Manageru: viz [kurz: integrace Azure Key Vault v nasazení šablony Resource Manageru](./resource-manager-tutorial-use-key-vault.md#prepare-the-key-vault).

> [!NOTE]
> Jednotlivé služby Azure má požadavky na konkrétní heslo. Například požadavky na Azure virtuální počítače najdete tady [jaké jsou požadavky na heslo při vytváření virtuálního počítače?](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

## <a name="enable-access-to-the-secret"></a>Povolení přístupu k tajný kód

Kromě nastavení `enabledForTemplateDeployment` k `true`, musí mít uživatel nasazení šablony `Microsoft.KeyVault/vaults/deploy/action` oprávnění pro obor, který obsahuje Key Vault, včetně skupiny prostředků a Key Vault. [Vlastníka](../role-based-access-control/built-in-roles.md#owner) a [Přispěvatel](../role-based-access-control/built-in-roles.md#contributor) obě role udělit přístup. Pokud vytvoříte trezor klíčů, jste vlastníkem, abyste měli oprávnění. Pokud služby Key Vault je pod správou jiného předplatného, musí vlastník služby Key Vault grand přístup.

Následující postup ukazuje, jak vytvořit roli s minimální permssion a přiřazení uživatele
1. Vytvořte soubor JSON s definicí vlastní role:

    ```json
    {
      "Name": "Key Vault resource manager template deployment operator",
      "IsCustom": true,
      "Description": "Lets you deploy a resource manager template with the access to the secrets in the Key Vault.",
      "Actions": [
        "Microsoft.KeyVault/vaults/deploy/action"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
    "00000000-0000-0000-0000-000000000000" nahraďte ID předplatného uživatele, který potřebují pro nasazení šablony.

2. Vytvoření nové role pomocí souboru JSON:

    ```azurepowershell
    $resourceGroupName= "<Resource Group Name>" # the resource group which contains the Key Vault
    $userPrincipalName = "<Email Address of the deployment operator>"
    New-AzureRmRoleDefinition -InputFile "<PathToTheJSONFile>" 
    New-AzureRmRoleAssignment -ResourceGroupName $resourceGroupName -RoleDefinitionName "Key Vault resource manager template deployment operator" -SignInName $userPrincipalName
    ```

    `New-AzureRmRoleAssignment` Ukázka přiřazení vlastní role uživatele na úrovni skupiny prostředků.  

Při použití služby Key Vault se šablonou pro [spravované aplikace](../managed-applications/overview.md), je nutné udělit přístup k **poskytovatele prostředků zařízení** instančního objektu. Další informace najdete v tématu [tajného kódu trezoru klíčů přístup při nasazování služby Azure Managed Applications](../managed-applications/key-vault-access.md).

## <a name="reference-a-secret-with-static-id"></a>Odkazovat na tajný kód se statické ID

Stejně jako jakékoli jiné šablony je šablona, která přijímá tajný kód trezoru klíčů. Důvodem je, že **odkazujete službě key vault v souboru parametrů není šablona.** Následující obrázek ukazuje, jak soubor parametrů odkazuje na tajný kód a předá tuto hodnotu do šablony.

![Statické ID](./media/resource-manager-keyvault-parameter/statickeyvault.png)

Například [následující šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/keyvaultparameter/sqlserver.json) nasadí, který obsahuje heslo správce databáze SQL. Parametr hesla je nastaven na zabezpečený řetězec. Ale šablona neurčuje, kde tato hodnota pochází.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adminLogin": {
      "type": "string"
    },
    "adminPassword": {
      "type": "securestring"
    },
    "sqlServerName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "name": "[parameters('sqlServerName')]",
      "type": "Microsoft.Sql/servers",
      "apiVersion": "2015-05-01-preview",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
        "administratorLogin": "[parameters('adminLogin')]",
        "administratorLoginPassword": "[parameters('adminPassword')]",
        "version": "12.0"
      }
    }
  ],
  "outputs": {
  }
}
```

Teď vytvořte soubor parametrů pro předchozí šablonu postupem. V souboru parametrů zadejte parametr, který odpovídá názvu parametru v šabloně. Pro hodnotu parametru odkazujte tajný kód trezoru klíčů. Tajný kód odkazujete předáním identifikátor prostředku trezoru klíčů a název tajného kódu. V [následující parametr soubor](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/keyvaultparameter/sqlserver.parameters.json), již musí existovat tajný kód trezoru klíčů, a zadejte statickou hodnotu pro jeho ID prostředku. Zkopírujte tento soubor místně a nastavit ID předplatného, název trezoru a název systému SQL server.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "adminLogin": {
            "value": "exampleadmin"
        },
        "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "/subscriptions/<subscription-id>/resourceGroups/examplegroup/providers/Microsoft.KeyVault/vaults/<vault-name>"
              },
              "secretName": "examplesecret"
            }
        },
        "sqlServerName": {
            "value": "<your-server-name>"
        }
    }
}
```

Pokud je potřeba použít verzi tajného kódu, než je aktuální verzi, použijte `secretVersion` vlastnost.

```json
"secretName": "examplesecret",
"secretVersion": "cd91b2b7e10e492ebb870a6ee0591b68"
```

Nyní nasaďte šablonu a předejte parametr soubor. Můžete použít šablonu příkladu z Githubu, ale je nutné použít souboru místní parametrů s hodnot nastavených pro vaše prostředí.

Pokud používáte Azure CLI, použijte:

```azurecli-interactive
az group create --name datagroup --location $location
az group deployment create \
    --name exampledeployment \
    --resource-group datagroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json \
    --parameters @sqlserver.parameters.json
```

Pokud používáte PowerShell, použijte:

```powershell-interactive
New-AzureRmResourceGroup -Name datagroup -Location $location
New-AzureRmResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName datagroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json `
  -TemplateParameterFile sqlserver.parameters.json
```

## <a name="reference-a-secret-with-dynamic-id"></a>Odkazovat na tajný kód se dynamické ID

V předchozí části jsme si ukázali, jak předat ID statických prostředků pro tajný kód trezoru klíčů z parametru. Nicméně v některých případech budete muset odkaz tajný kód trezoru klíčů, který se liší podle aktuální nasazení. Nebo můžete chtít vytvořit odkaz na parametr v souboru parametrů místo předání hodnot parametrů do šablony jednoduše. V obou případech se může dynamicky generovat ID prostředku pro tajný kód trezoru klíčů pomocí propojenou šablonu.

Nelze generovat dynamicky ID prostředku v souboru parametrů vzhledem k tomu, že šablona výrazy nejsou povoleny v souboru parametrů. 

V nadřazené šablony můžete přidat propojené šablony a předejte parametr, který obsahuje ID dynamicky generované prostředku. Následující obrázek ukazuje, jak v propojené šablony parametr odkazuje na tajný kód.

![Dynamické ID](./media/resource-manager-keyvault-parameter/dynamickeyvault.png)

[Následující šablony](https://github.com/Azure/azure-quickstart-templates/tree/master/201-key-vault-use-dynamic-id) dynamicky vytvoří ID služby key vault a předá jej jako parametr.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "The location where the resources will be deployed."
            }
        },
        "vaultName": {
            "type": "string",
            "metadata": {
                "description": "The name of the keyvault that contains the secret."
            }
        },
        "secretName": {
            "type": "string",
            "metadata": {
                "description": "The name of the secret."
            }
        },
        "vaultResourceGroupName": {
            "type": "string",
            "metadata": {
                "description": "The name of the resource group that contains the keyvault."
            }
        },
        "vaultSubscription": {
            "type": "string",
            "defaultValue": "[subscription().subscriptionId]",
            "metadata": {
                "description": "The name of the subscription that contains the keyvault."
            }
        },
        "_artifactsLocation": {
            "type": "string",
            "metadata": {
                "description": "The base URI where artifacts required by this template are located. When the template is deployed using the accompanying scripts, a private location in the subscription will be used and this value will be automatically generated."
            },
            "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/"
        },
        "_artifactsLocationSasToken": {
            "type": "securestring",
            "metadata": {
                "description": "The sasToken required to access _artifactsLocation.  When the template is deployed using the accompanying scripts, a sasToken will be automatically generated."
            },
            "defaultValue": ""
        }
    },
    "resources": [
        {
            "apiVersion": "2018-05-01",
            "name": "dynamicSecret",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "contentVersion": "1.0.0.0",
                    "uri": "[uri(parameters('_artifactsLocation'), concat('./nested/sqlserver.json', parameters('_artifactsLocationSasToken')))]"
                },
                "parameters": {
                    "location": {
                        "value": "[parameters('location')]"
                    },
                    "adminLogin": {
                        "value": "ghuser"
                    },
                    "adminPassword": {
                        "reference": {
                            "keyVault": {
                                "id": "[resourceId(parameters('vaultSubscription'), parameters('vaultResourceGroupName'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
                            },
                            "secretName": "[parameters('secretName')]"
                        }
                    }
                }
            }
        }
    ],
    "outputs": {
        "sqlFQDN": {
            "type": "string",
            "value": "[reference('dynamicSecret').outputs.sqlFQDN.value]"
        }
    }
}
```

Nasadit předchozí šablonu a zadejte hodnoty pro parametry. Můžete použít šablonu příkladu z Githubu, ale je nutné zadat hodnoty parametrů pro vaše prostředí.

Pokud používáte Azure CLI, použijte:

```azurecli-interactive
az group create --name datagroup --location $location
az group deployment create \
    --name exampledeployment \
    --resource-group datagroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/azuredeploy.json \
    --parameters vaultName=<your-vault> vaultResourceGroupName=examplegroup secretName=examplesecret
```

Pokud používáte PowerShell, použijte:

```powershell
New-AzureRmResourceGroup -Name datagroup -Location $location
New-AzureRmResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName datagroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/azuredeploy.json `
  -vaultName <your-vault> -vaultResourceGroupName examplegroup -secretName examplesecret
```

## <a name="next-steps"></a>Další postup
* Obecné informace o trezorů klíčů najdete v tématu [Začínáme s Azure Key Vault](../key-vault/key-vault-get-started.md).
* Kompletní příklady odkazující na klíče tajných kódů, najdete v článku [služby Key Vault příklady](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
