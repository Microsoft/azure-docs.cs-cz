---
title: Tajný klíč trezoru klíčů se šablonou
description: Ukazuje, jak předat tajný klíč z trezoru klíčů jako parametr během nasazení.
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: d21a7d727091b427fee59e22db6a77a495a4eab7
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458262"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Použití trezoru klíčů Azure k předání hodnoty zabezpečeného parametru během nasazení

Místo umístění zabezpečené hodnoty (například hesla) přímo do šablony nebo souboru parametrů můžete hodnotu během nasazení načíst z [trezoru klíčů Azure.](../../key-vault/general/overview.md) Hodnotu načtete odkazem na trezor klíčů a tajný klíč v souboru parametrů. Hodnota není nikdy vystavena, protože odkazujete pouze na id trezoru klíčů. Trezor klíčů může existovat v jiném předplatném než skupina prostředků, do které nasazujete.

Tento článek se zaměřuje na scénář předávání citlivé hodnoty v jako parametr šablony. Nezahrnuje scénář nastavení vlastnosti virtuálního počítače na adresu URL certifikátu v trezoru klíčů. Šablonu pro rychlý start tohoto scénáře najdete [v tématu Instalace certifikátu z azure key vaultu na virtuálním počítači](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows).

## <a name="deploy-key-vaults-and-secrets"></a>Nasazení trezorů klíčů a tajných kódů

Chcete-li získat přístup k `enabledForTemplateDeployment` trezoru klíčů `true`během nasazení šablony, nastavte v trezoru klíčů na .

Pokud již trezor klíčů máte, ujistěte se, že umožňuje nasazení šablon.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az keyvault update  --name ExampleVault --enabled-for-template-deployment true
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName ExampleVault -EnabledForTemplateDeployment
```

---

Chcete-li vytvořit nový trezor klíčů a přidat tajný klíč, použijte:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name ExampleGroup --location centralus
az keyvault create \
  --name ExampleVault \
  --resource-group ExampleGroup \
  --location centralus \
  --enabled-for-template-deployment true
az keyvault secret set --vault-name ExampleVault --name "ExamplePassword" --value "hVFkk965BuUv"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name ExampleGroup -Location centralus
New-AzKeyVault `
  -VaultName ExampleVault `
  -resourceGroupName ExampleGroup `
  -Location centralus `
  -EnabledForTemplateDeployment
$secretvalue = ConvertTo-SecureString 'hVFkk965BuUv' -AsPlainText -Force
$secret = Set-AzKeyVaultSecret -VaultName ExampleVault -Name 'ExamplePassword' -SecretValue $secretvalue
```

---

Jako vlastník trezoru klíčů máte automaticky přístup k vytváření tajných kódů. Pokud uživatel pracující s tajnými klíči není vlastníkem trezoru klíčů, udělte přístup pomocí:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az keyvault set-policy \
  --upn <user-principal-name> \
  --name ExampleVault \
  --secret-permissions set delete get list
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$userPrincipalName = "<Email Address of the deployment operator>"

Set-AzKeyVaultAccessPolicy `
  -VaultName ExampleVault `
  -UserPrincipalName <user-principal-name> `
  -PermissionsToSecrets set,delete,get,list
```

---

Další informace o vytváření trezorů klíčů a přidávání tajných kódů naleznete v tématu:

- [Nastavení a načtení tajného klíče pomocí funkce cli](../../key-vault/secrets/quick-create-cli.md)
- [Nastavení a načtení tajného klíče pomocí powershellu](../../key-vault/secrets/quick-create-powershell.md)
- [Nastavení a načtení tajného klíče pomocí portálu](../../key-vault/secrets/quick-create-portal.md)
- [Nastavení a načtení tajného klíče pomocí rozhraní .NET](../../key-vault/secrets/quick-create-net.md)
- [Nastavení a načtení tajného klíče pomocí souboru Node.js](../../key-vault/secrets/quick-create-node.md)

## <a name="grant-access-to-the-secrets"></a>Udělit přístup k tajným kódům

Uživatel, který šablonu nasazuje, musí mít `Microsoft.KeyVault/vaults/deploy/action` oprávnění pro obor skupiny prostředků a trezoru klíčů. Tento přístup udělují role [Vlastník](../../role-based-access-control/built-in-roles.md#owner) i [Přispěvatel.](../../role-based-access-control/built-in-roles.md#contributor) Pokud jste trezor klíčů vytvořili, jste vlastníkem, takže máte oprávnění.

Následující postup ukazuje, jak vytvořit roli s minimálním oprávněním a jak přiřadit uživatele

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
    Nahraďte "000000000-0000-0000-0000-0000000000000000" ID předplatného.

2. Vytvořte novou roli pomocí souboru JSON:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli-interactive
    az role definition create --role-definition "<path-to-role-file>"
    az role assignment create \
      --role "Key Vault resource manager template deployment operator" \
      --assignee <user-principal-name> \
      --resource-group ExampleGroup
    ```

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    ```azurepowershell-interactive
    New-AzRoleDefinition -InputFile "<path-to-role-file>"
    New-AzRoleAssignment `
      -ResourceGroupName ExampleGroup `
      -RoleDefinitionName "Key Vault resource manager template deployment operator" `
      -SignInName <user-principal-name>
    ```

    ---

    Ukázky přiřazují vlastní roli uživateli na úrovni skupiny prostředků.

Při použití trezoru klíčů se šablonou pro [spravovanou aplikaci](../managed-applications/overview.md)je nutné udělit přístup k instančnímu objektu **zprostředkovatele prostředků zařízení.** Další informace najdete [v tématu Tajný klíč aplikace Access Key Vault při nasazování spravovaných aplikací Azure](../managed-applications/key-vault-access.md).

## <a name="reference-secrets-with-static-id"></a>Referenční tajné klíče se statickým ID

S tímto přístupem odkazujete na trezor klíčů v souboru parametrů, nikoli na šablonu. Následující obrázek ukazuje, jak soubor parametru odkazuje na tajný klíč a předá tuto hodnotu šabloně.

![Diagram statického ID integrace trezoru klíčů Správce prostředků](./media/key-vault-parameter/statickeyvault.png)

[Kurz: Integrace trezoru klíčů Azure v nasazení šablony Správce prostředků](./template-tutorial-use-key-vault.md) používá tuto metodu.

Následující šablona nasazuje server SQL, který obsahuje heslo správce. Parametr hesel je nastaven na zabezpečený řetězec. Šablona však neurčuje, odkud tato hodnota pochází.

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
      "type": "Microsoft.Sql/servers",
      "apiVersion": "2015-05-01-preview",
      "name": "[parameters('sqlServerName')]",
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

Nyní vytvořte soubor parametrů pro předchozí šablonu. V souboru parametrů zadejte parametr, který odpovídá názvu parametru v šabloně. Pro hodnotu parametru odkaz tajný klíč z trezoru klíčů. Na tajný klíč odkazujete předáním identifikátoru prostředku trezoru klíčů a názvu tajného klíče:

V následujícím souboru parametrů musí tajný klíč trezoru klíčů již existovat a zadáte statickou hodnotu pro jeho ID prostředku.

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

Pokud potřebujete použít jinou verzi než aktuální verzi, `secretVersion` použijte vlastnost.

```json
"secretName": "ExamplePassword",
"secretVersion": "cd91b2b7e10e492ebb870a6ee0591b68"
```

Nasaďte šablonu a předaďte v souboru parametrů:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name SqlGroup --location westus2
az deployment group create \
  --resource-group SqlGroup \
  --template-uri <template-file-URI> \
  --parameters <parameter-file>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri <template-file-URI> `
  -TemplateParameterFile <parameter-file>
```

---

## <a name="reference-secrets-with-dynamic-id"></a>Referenční tajné klíče s dynamickým ID

Předchozí část ukázala, jak předat id statického prostředku pro tajný klíč trezoru klíčů z parametru. V některých případech je však nutné odkazovat na tajný klíč trezoru klíčů, který se liší v závislosti na aktuálním nasazení. Nebo můžete chtít předat hodnoty parametrů do šablony, nikoli vytvořit referenční parametr v souboru parametrů. V obou případech můžete dynamicky generovat ID prostředku pro tajný klíč trezoru klíčů pomocí propojené šablony.

ID prostředku nelze v souboru parametrů dynamicky generovat, protože výrazy šablony nejsou v souboru parametrů povoleny.

V nadřazené šabloně přidáte vnořenou šablonu a předáte parametr, který obsahuje dynamicky generované ID prostředku. Následující obrázek ukazuje, jak parametr v propojené šabloně odkazuje na tajný klíč.

![Dynamické ID](./media/key-vault-parameter/dynamickeyvault.png)

Následující šablona dynamicky vytvoří ID trezoru klíčů a předá ho jako parametr.

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
      }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "name": "dynamicSecret",
      "properties": {
        "mode": "Incremental",
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminLogin": {
              "type": "string"
            },
            "adminPassword": {
              "type": "securestring"
            },
            "location": {
              "type": "string"
            }
          },
          "variables": {
            "sqlServerName": "[concat('sql-', uniqueString(resourceGroup().id, 'sql'))]"
          },
          "resources": [
            {
              "type": "Microsoft.Sql/servers",
              "apiVersion": "2018-06-01-preview",
              "name": "[variables('sqlServerName')]",
              "location": "[parameters('location')]",
              "properties": {
                "administratorLogin": "[parameters('adminLogin')]",
                "administratorLoginPassword": "[parameters('adminPassword')]"
              }
            }
          ],
          "outputs": {
            "sqlFQDN": {
              "type": "string",
              "value": "[reference(variables('sqlServerName')).fullyQualifiedDomainName]"
            }
          }
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
  }
}
```

## <a name="next-steps"></a>Další kroky

- Obecné informace o trezorech klíčů najdete v tématu [Co je Azure Key Vault?](../../key-vault/general/overview.md).
- Úplné příklady odkazování na klíče klíčů naleznete v [příkladech trezoru klíčů](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
