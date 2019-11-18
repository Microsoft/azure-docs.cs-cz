---
title: Key Vault tajný klíč se šablonou
description: Ukazuje, jak předat tajný klíč z trezoru klíčů jako parametr během nasazování.
ms.topic: conceptual
ms.date: 05/09/2019
ms.openlocfilehash: 37d21e295eca2b40e91f92d65d6e927ee6857d0e
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149489"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Použití Azure Key Vault k předání hodnoty zabezpečeného parametru během nasazení

Namísto vložení zabezpečené hodnoty (například hesla) přímo do šablony nebo souboru parametrů můžete načíst hodnotu z [Azure Key Vault](../key-vault/key-vault-overview.md) během nasazení. Hodnotu načtete odkazem na Trezor klíčů a tajný kód v souboru parametrů. Hodnota se nikdy nezveřejňuje, protože odkazujete jenom na její ID trezoru klíčů. Trezor klíčů může existovat v jiném předplatném, než je skupina prostředků, do které nasazujete.

## <a name="deploy-key-vaults-and-secrets"></a>Nasazení trezorů klíčů a tajných kódů

Pro přístup k trezoru klíčů během nasazování šablony nastavte `enabledForTemplateDeployment` v trezoru klíčů na `true`.

Následující ukázky Azure CLI a Azure PowerShell ukazují, jak vytvořit Trezor klíčů a jak přidat tajný klíč.

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

Jako vlastník trezoru klíčů automaticky máte přístup k vytváření tajných kódů. Pokud uživatel, který pracuje s tajnými kódy, není vlastníkem trezoru klíčů, udělte přístup pomocí:

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

Další informace o vytváření trezorů klíčů a přidávání tajných klíčů najdete v těchto tématech:

- [Nastavení a načtení tajného klíče pomocí rozhraní příkazového řádku](../key-vault/quick-create-cli.md)
- [Nastavení a načtení tajného klíče pomocí PowerShellu](../key-vault/quick-create-powershell.md)
- [Nastavení a načtení tajného klíče pomocí portálu](../key-vault/quick-create-portal.md)
- [Nastavení a načtení tajného klíče pomocí .NET](../key-vault/quick-create-net.md)
- [Nastavení a načtení tajného klíče pomocí Node. js](../key-vault/quick-create-node.md)

## <a name="grant-access-to-the-secrets"></a>Udělení přístupu k tajným klíčům

Uživatel, který šablonu nasadí, musí mít oprávnění `Microsoft.KeyVault/vaults/deploy/action` pro rozsah skupiny prostředků a trezoru klíčů. Tento přístup udělují i role [vlastníka](../role-based-access-control/built-in-roles.md#owner) a [Přispěvatel](../role-based-access-control/built-in-roles.md#contributor) . Pokud jste vytvořili Trezor klíčů, jste vlastníkem, abyste měli oprávnění.

Následující postup ukazuje, jak vytvořit roli s minimálním oprávněním a jak přiřadit uživatele.

1. Vytvořte vlastní soubor JSON definice role:

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
    Nahraďte "00000000-0000-0000-0000-000000000000" IDENTIFIKÁTORem předplatného.

2. Vytvořte novou roli pomocí souboru JSON:

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

    V ukázkách se uživateli přiřadí vlastní role na úrovni skupiny prostředků.  

Při použití Key Vault se šablonou pro [spravovanou aplikaci](../managed-applications/overview.md)je nutné udělit přístup k instančnímu objektu **poskytovatele prostředků zařízení** . Další informace najdete v tématu [přístup Key Vault tajného klíče při nasazení Azure Managed Applications](../managed-applications/key-vault-access.md).

## <a name="reference-secrets-with-static-id"></a>Referenční tajné klíče se statickým ID

S tímto přístupem odkazujete na Trezor klíčů v souboru parametrů, nikoli na šablonu. Následující obrázek ukazuje, jak soubor parametrů odkazuje na tajný kód a předá tuto hodnotu šabloně.

![Správce prostředků diagramu statického ID integrace trezoru klíčů](./media/resource-manager-keyvault-parameter/statickeyvault.png)

[Kurz: integrování Azure Key Vault v Správce prostředků Template Deployment](./resource-manager-tutorial-use-key-vault.md) používá tuto metodu.

Následující šablona nasadí SQL Server, který obsahuje heslo správce. Parametr password je nastaven na zabezpečený řetězec. Ale šablona neurčuje, ze které hodnoty pochází.

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

Nyní vytvořte soubor parametrů pro předchozí šablonu. V souboru parametrů zadejte parametr, který odpovídá názvu parametru v šabloně. Pro hodnotu parametru odkázat na tajný klíč z trezoru klíčů. Odkazujte na tajný klíč předáním identifikátoru prostředku trezoru klíčů a názvu tajného kódu:

V následujícím souboru parametrů již musí existovat tajný klíč trezoru klíčů a pro jeho ID prostředku poskytnete statickou hodnotu.

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

Pokud potřebujete použít jinou verzi tajného klíče, než je aktuální verze, použijte vlastnost `secretVersion`.

```json
"secretName": "ExamplePassword",
"secretVersion": "cd91b2b7e10e492ebb870a6ee0591b68"
```

Nasaďte šablonu a předejte ji do souboru parametrů:

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

## <a name="reference-secrets-with-dynamic-id"></a>Referenční tajné klíče s dynamickým ID

Předchozí část ukázala, jak předat identifikátor statického prostředku pro tajný klíč trezoru klíčů z parametru. V některých scénářích ale musíte odkazovat na tajný klíč trezoru klíčů, který se liší v závislosti na aktuálním nasazení. Nebo můžete chtít předat hodnoty parametrů do šablony, nikoli vytvořit parametr odkazu v souboru parametrů. V obou případech můžete dynamicky generovat ID prostředku pro tajný kód trezoru klíčů pomocí propojené šablony.

V souboru parametrů nelze dynamicky generovat ID prostředku, protože výrazy šablony nejsou povoleny v souboru parametrů. 

V nadřazené šabloně přidáte odkazovanou šablonu a předáte parametr, který obsahuje dynamicky generované ID prostředku. Následující obrázek ukazuje, jak parametr v propojené šabloně odkazuje na tajný kód.

![Dynamické ID](./media/resource-manager-keyvault-parameter/dynamickeyvault.png)

[Následující šablona](https://github.com/Azure/azure-quickstart-templates/tree/master/201-key-vault-use-dynamic-id) dynamicky vytvoří ID trezoru klíčů a předá ho jako parametr.

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

Nasaďte předchozí šablonu a zadejte hodnoty pro parametry. Můžete použít ukázkovou šablonu z GitHubu, ale musíte zadat hodnoty parametrů pro vaše prostředí.

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

## <a name="next-steps"></a>Další kroky

- Obecné informace o trezorech klíčů najdete v tématu [co je Azure Key Vault?](../key-vault/key-vault-overview.md).
- Kompletní Příklady referenčních tajných klíčů najdete v tématu [Key Vault příklady](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
