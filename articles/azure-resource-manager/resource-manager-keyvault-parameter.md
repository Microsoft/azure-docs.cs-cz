---
title: Tajný kód trezoru klíčů pomocí šablony Azure Resource Manageru | Dokumentace Microsoftu
description: Ukazuje, jak předat tajného klíče ze služby key vault jako parametr během nasazení.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: tomfitz
ms.openlocfilehash: e47a087e27b6a8ade947e36ded762ce2e518ca25
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65507992"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Použití Azure Key Vault k předání zabezpečený parametr. hodnoty během nasazení

Namísto vložení hodnoty zabezpečenou hodnotu (jako jsou hesla) přímo v souboru šablony nebo parametr, můžete načíst hodnotu z [Azure Key Vault](../key-vault/key-vault-whatis.md) během nasazení. Načtení hodnoty pomocí odkazu na trezor klíčů a tajný klíč v souboru parametrů. Hodnota se nikdy vystavena, protože pouze odkazujete na jeho ID služby key vault. Trezor klíčů může existovat v jiném předplatném než které nasazení provádíte do skupiny prostředků.

## <a name="deploy-key-vaults-and-secrets"></a>Nasazení trezorů klíčů a tajných kódů

Chcete-li získat přístup k trezoru klíčů během nasazování šablony, nastavte `enabledForTemplateDeployment` v trezoru klíčů, aby `true`.

Následující ukázky rozhraní příkazového řádku Azure a Azure Powershellu ukazují, jak vytvořit trezor klíčů a přidat tajný klíč.

```azurecli
az group create --name $resourceGroupName --location $location
az keyvault create \
  --name $keyVaultName \
  --resource-group $resourceGroupName \
  --location $location \
  --enabled-for-template-deployment true
az keyvault secret set --vault-name $keyVaultName --name "ExamplePassword" --value "hVFkk965BuUv"
```

```azurepowershell
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzKeyVault `
  -VaultName $keyVaultName `
  -resourceGroupName $resourceGroupName `
  -Location $location `
  -EnabledForTemplateDeployment
$secretvalue = ConvertTo-SecureString 'hVFkk965BuUv' -AsPlainText -Force
$secret = Set-AzKeyVaultSecret -VaultName $keyVaultName -Name 'ExamplePassword' -SecretValue $secretvalue
```

Jako vlastníka trezoru klíčů budete mít automaticky přístup k vytváření tajných kódů. Není-li uživatel práci s tajnými kódy vlastníka trezoru klíčů, udělte přístup pomocí:

```azurecli
az keyvault set-policy \
  --upn $userPrincipalName \
  --name $keyVaultName \
  --secret-permissions set delete get list
```

```azurepowershell
$userPrincipalName = "<Email Address of the deployment operator>"

Set-AzKeyVaultAccessPolicy `
  -VaultName $keyVaultName `
  -UserPrincipalName $userPrincipalName `
  -PermissionsToSecrets set,delete,get,list
```

Další informace o vytváření trezorů klíčů a přidávání tajných kódů naleznete v tématu:

- [Nastavení a načtení tajného kódu s použitím rozhraní příkazového řádku](../key-vault/quick-create-cli.md)
- [Nastavení a načtení tajného kódu s použitím prostředí Powershell](../key-vault/quick-create-powershell.md)
- [Nastavení a načtení tajného kódu s využitím portálu](../key-vault/quick-create-portal.md)
- [Nastavení a načtení tajného kódu s použitím rozhraní .NET](../key-vault/quick-create-net.md)
- [Nastavení a načtení tajného kódu s použitím prostředí Node.js](../key-vault/quick-create-node.md)

## <a name="grant-access-to-the-secrets"></a>Udělení přístupu k tajné klíče

Uživatel, který nasadí šabloně musí mít `Microsoft.KeyVault/vaults/deploy/action` oprávnění oboru skupiny prostředků a trezor klíčů. [Vlastníka](../role-based-access-control/built-in-roles.md#owner) a [Přispěvatel](../role-based-access-control/built-in-roles.md#contributor) obě role udělit přístup. Pokud jste vytvořili trezor klíčů, jste vlastníkem, abyste měli oprávnění.

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

    ```azurecli
    az role definition create --role-definition "<PathToRoleFile>"
    az role assignment create \
      --role "Key Vault resource manager template deployment operator" \
      --assignee $userPrincipalName \
      --resource-group $resourceGroupName
    ```

    ```azurepowershell
    New-AzRoleDefinition -InputFile "<PathToRoleFile>" 
    New-AzRoleAssignment `
      -ResourceGroupName $resourceGroupName `
      -RoleDefinitionName "Key Vault resource manager template deployment operator" `
      -SignInName $userPrincipalName
    ```

    Ukázky přiřadit vlastní roli uživatele na úrovni skupiny prostředků.  

Při použití služby Key Vault se šablonou pro [spravované aplikace](../managed-applications/overview.md), je nutné udělit přístup k **poskytovatele prostředků zařízení** instančního objektu. Další informace najdete v tématu [tajného kódu trezoru klíčů přístup při nasazování služby Azure Managed Applications](../managed-applications/key-vault-access.md).

## <a name="reference-secrets-with-static-id"></a>Referenční dokumentace tajných kódů pomocí statické ID

S tímto přístupem můžete odkazovat na službě key vault v souboru parametrů není šablona. Následující obrázek ukazuje, jak soubor parametrů odkazuje na tajný kód a předá tuto hodnotu do šablony.

![Diagram statické ID integrace trezoru klíčů Resource Manageru](./media/resource-manager-keyvault-parameter/statickeyvault.png)

[Kurz: Integrace Azure Key Vault v nasazení šablony Resource Manageru](./resource-manager-tutorial-use-key-vault.md) používá tuto metodu.

Následující šablona nasadí serveru SQL server, který obsahuje heslo správce. Parametr hesla je nastaven na zabezpečený řetězec. Ale šablona neurčuje, kde tato hodnota pochází.

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

Teď vytvořte soubor parametrů pro předchozí šablonu postupem. V souboru parametrů zadejte parametr, který odpovídá názvu parametru v šabloně. Pro hodnotu parametru odkazujte tajný kód trezoru klíčů. Tajný kód odkazujete předáním identifikátor prostředku trezoru klíčů a název tajného kódu:

V následujícím souboru parametru tajný kód trezoru klíčů už musí existovat a zadejte statickou hodnotu pro jeho ID prostředku.

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
                "id": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<vault-name>"
              },
              "secretName": "ExamplePassword"
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
"secretName": "ExamplePassword",
"secretVersion": "cd91b2b7e10e492ebb870a6ee0591b68"
```

Nasazení šablony a předejte parametr soubor:

Pokud používáte Azure CLI, použijte:

```azurecli
az group create --name $resourceGroupName --location $location
az group deployment create \
    --resource-group $resourceGroupName \
    --template-uri <The Template File URI> \
    --parameters <The Parameter File>
```

Pokud používáte PowerShell, použijte:

```powershell
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
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
    --resource-group $resourceGroupName \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/azuredeploy.json \
    --parameters vaultName=$keyVaultName vaultResourceGroupName=examplegroup secretName=examplesecret
```

Pokud používáte PowerShell, použijte:

```powershell
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/azuredeploy.json `
  -vaultName $keyVaultName -vaultResourceGroupName $keyVaultResourceGroupName -secretName $secretName
```

## <a name="next-steps"></a>Další postup

- Obecné informace o trezorů klíčů najdete v tématu [co je Azure Key Vault?](../key-vault/key-vault-overview.md).
- Kompletní příklady odkazující na klíče tajných kódů, najdete v článku [služby Key Vault příklady](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
