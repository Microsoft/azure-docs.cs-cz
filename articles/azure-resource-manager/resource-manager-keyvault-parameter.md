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
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: 6e9ad6f74970b6c72b96ae142f02bee6b07fb558
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55455469"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Použití Azure Key Vault k předání zabezpečený parametr. hodnoty během nasazení

Namísto vložení hodnoty zabezpečenou hodnotu (jako jsou hesla) přímo v souboru parametrů, můžete načíst hodnotu z [Azure Key Vault](../key-vault/key-vault-whatis.md) během nasazení. Načtení hodnoty pomocí odkazu na trezor klíčů a tajný klíč v souboru parametrů. Hodnota se nikdy vystavena, protože pouze odkazujete na jeho ID služby key vault. Trezor klíčů může existovat v jiném předplatném než skupina prostředků, které nasazujete.

Absolvovat kurz, naleznete v tématu [kurzu: Integrace Azure Key Vault v nasazení šablony Resource Manageru](./resource-manager-tutorial-use-key-vault.md#prepare-a-key-vault).

## <a name="deploy-key-vaults-and-secrets"></a>Nasazení trezory klíčů a tajných kódů

Můžete vytvářet trezory klíčů a tajných kódů přidat, naleznete v tématu:

- [Nastavení a načtení tajného kódu s použitím rozhraní příkazového řádku](../key-vault/quick-create-cli.md)
- [Nastavení a načtení tajného kódu s použitím prostředí Powershell](../key-vault/quick-create-powershell.md)
- [Nastavení a načtení tajného kódu s využitím portálu](../key-vault/quick-create-portal.md)
- [Nastavení a načtení tajného kódu s použitím rozhraní .NET](../key-vault/quick-create-net.md)
- [Nastavení a načtení tajného kódu s použitím prostředí Node.js](../key-vault/quick-create-node.md)

Při integraci služby Key Vault s nasazení šablony Resource Manageru existují některé další aspekty a požadavky:

- `enabledForTemplateDeployment` je vlastností služby key vault. K nasazení podle modelu Resource Manager, přístup k tajným klíčům v této službě Key Vault `enabledForTemplateDeployment` musí být `true`. 
- Pokud si nejste vlastník služby key vault, vlastník musíte aktualizovat nastavení zásad zabezpečení trezoru klíčů můžete přidat tajných kódů.

Následující ukázky rozhraní příkazového řádku Azure a Azure Powershellu ukazují, jak se to dělá:

```azurecli
# Create a Key Vault
az keyvault create \
  --name $keyVaultName \
  --resource-group $resourceGroupName \
  --location $location \
  --enabled-for-template-deployment true
az keyvault set-policy --upn $userPrincipalName --name $keyVaultName --secret-permissions set delete get list
```

```azurepowershell
New-AzKeyVault `
  -VaultName $keyVaultName `
  -resourceGroupName $resourceGroupName `
  -Location $location `
  -EnabledForTemplateDeployment
Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userPrincipalName -PermissionsToSecrets set,delete,get,list
```

## <a name="grant-access-to-the-secrets"></a>Udělení přístupu k tajné klíče

Uživatel, který nasadí šabloně musí mít `Microsoft.KeyVault/vaults/deploy/action` oprávnění pro obor, který obsahuje Key Vault, včetně skupiny prostředků a Key Vault. [Vlastníka](../role-based-access-control/built-in-roles.md#owner) a [Přispěvatel](../role-based-access-control/built-in-roles.md#contributor) obě role udělit přístup. Pokud vytvoříte trezor klíčů, jste vlastníkem, abyste měli oprávnění. Pokud služby Key Vault je pod správou jiného předplatného, musí vlastník služby Key Vault grand přístup.

Následující postup ukazuje, jak vytvořit roli s minimální oprávnění a přiřazení uživatele

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
    New-AzRoleDefinition -InputFile "<PathToTheJSONFile>" 
    New-AzRoleAssignment -ResourceGroupName $resourceGroupName -RoleDefinitionName "Key Vault resource manager template deployment operator" -SignInName $userPrincipalName
    ```

    `New-AzRoleAssignment` Vzorovým kódem se přiřadí vlastní role pro uživatele na úrovni skupiny prostředků.  

Při použití služby Key Vault se šablonou pro [spravované aplikace](../managed-applications/overview.md), je nutné udělit přístup k **poskytovatele prostředků zařízení** instančního objektu. Další informace najdete v tématu [tajného kódu trezoru klíčů přístup při nasazování služby Azure Managed Applications](../managed-applications/key-vault-access.md).

## <a name="reference-secrets-with-static-id"></a>Referenční dokumentace tajných kódů pomocí statické ID

S tímto přístupem můžete odkazovat na službě key vault v souboru parametrů není šablona. Následující obrázek ukazuje, jak soubor parametrů odkazuje na tajný kód a předá tuto hodnotu do šablony.

![Diagram statické ID integrace trezoru klíčů Resource Manageru](./media/resource-manager-keyvault-parameter/statickeyvault.png)

[Kurz: Integrace Azure Key Vault v nasazení šablony Resource Manageru](./resource-manager-tutorial-use-key-vault.md) používá tuto metodu. Kurz nasazení virtuální počítače co zahrnuje hesla správce. Parametr hesla je nastaven na zabezpečený řetězec:

![Trezor klíčů integrace statické ID souboru šablony Resource Manageru](./media/resource-manager-keyvault-parameter/resource-manager-key-vault-static-id-template-file.png)

Teď vytvořte soubor parametrů pro předchozí šablonu postupem. V souboru parametrů zadejte parametr, který odpovídá názvu parametru v šabloně. Pro hodnotu parametru odkazujte tajný kód trezoru klíčů. Tajný kód odkazujete předáním identifikátor prostředku trezoru klíčů a název tajného kódu:

![Trezor klíčů integrace statické ID souboru parametrů Resource Manageru](./media/resource-manager-keyvault-parameter/resource-manager-key-vault-static-id-parameter-file.png)

Pokud je potřeba použít verzi tajného kódu, než je aktuální verzi, použijte `secretVersion` vlastnost.

```json
"secretName": "examplesecret",
"secretVersion": "cd91b2b7e10e492ebb870a6ee0591b68"
```

Nasazení šablony a předejte parametr soubor:

Pokud používáte Azure CLI, použijte:

```azurecli
az group create --name $resourceGroupName --location $location
az group deployment create \
    --name $deploymentName \
    --resource-group $resourceGroupName \
    --template-uri <The Template File URI> \
    --parameters <The Parameter File>
```

Pokud používáte PowerShell, použijte:

```powershell
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
  -Name $deploymentName `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri <The Template File URI> `
  -TemplateParameterFile <The Parameter File>
```

## <a name="reference-secrets-with-dynamic-id"></a>Referenční dokumentace tajných kódů pomocí dynamické ID

V předchozí části jsme si ukázali, jak předat ID statických prostředků pro tajný kód trezoru klíčů z parametru. Nicméně v některých případech budete muset odkaz tajný kód trezoru klíčů, který se liší podle aktuální nasazení. Nebo můžete chtít předat hodnoty parametrů v šabloně, ne vytvořit referenční parametr v souboru parametrů. V obou případech se může dynamicky generovat ID prostředku pro tajný kód trezoru klíčů pomocí propojenou šablonu.

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

```azurecli
az group create --name $resourceGroupName --location $location
az group deployment create \
    --name $deploymentName \
    --resource-group $resourceGroupName \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/azuredeploy.json \
    --parameters vaultName=$keyVaultName vaultResourceGroupName=examplegroup secretName=examplesecret
```

Pokud používáte PowerShell, použijte:

```powershell
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
  -Name $deploymentName `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/azuredeploy.json `
  -vaultName $keyVaultName -vaultResourceGroupName $keyVaultResourceGroupName -secretName $secretName
```

## <a name="next-steps"></a>Další postup

- Obecné informace o trezorů klíčů najdete v tématu [Začínáme s Azure Key Vault](../key-vault/key-vault-get-started.md).
- Kompletní příklady odkazující na klíče tajných kódů, najdete v článku [služby Key Vault příklady](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
