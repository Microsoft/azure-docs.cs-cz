---
title: Key Vault tajný klíč se šablonou
description: Ukazuje, jak předat tajný klíč z trezoru klíčů jako parametr během nasazování.
ms.topic: conceptual
ms.date: 12/17/2020
ms.openlocfilehash: 05749fe2e9179051c3183ea2e592cf7190ddb347
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889854"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Použití trezoru klíčů Azure Key Vault k předávání hodnoty zabezpečeného parametru během nasazování

Namísto vložení zabezpečené hodnoty (například hesla) přímo do šablony nebo souboru parametrů můžete načíst hodnotu z [Azure Key Vault](../../key-vault/general/overview.md) během nasazení. Hodnotu načtete odkazem na Trezor klíčů a tajný kód v souboru parametrů. Hodnota se nikdy nezveřejňuje, protože odkazujete jenom na její ID trezoru klíčů. Trezor klíčů může existovat v jiném předplatném, než je skupina prostředků, do které nasazujete.

Tento článek se zaměřuje na scénář předávání citlivé hodnoty v jako parametr šablony. Nezabývá se scénářem nastavení vlastnosti virtuálního počítače na adresu URL certifikátu ve Key Vault. Šablonu pro rychlý Start v tomto scénáři najdete v tématu [instalace certifikátu z Azure Key Vault na virtuálním počítači](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows).

## <a name="deploy-key-vaults-and-secrets"></a>Nasazení trezorů klíčů a tajných kódů

Pro přístup k trezoru klíčů během nasazování šablony nastavte `enabledForTemplateDeployment` v trezoru klíčů na `true` .

Pokud již máte Key Vault, ujistěte se, že umožňuje nasazení šablon.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az keyvault update  --name ExampleVault --enabled-for-template-deployment true
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName ExampleVault -EnabledForTemplateDeployment
```

---

Pokud chcete vytvořit nový Key Vault a přidat tajný klíč, použijte:

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

Jako vlastník trezoru klíčů automaticky máte přístup k vytváření tajných kódů. Pokud uživatel, který pracuje s tajnými kódy, není vlastníkem trezoru klíčů, udělte přístup pomocí:

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

Další informace o vytváření trezorů klíčů a přidávání tajných klíčů najdete v těchto tématech:

- [Nastavení a načtení tajného klíče pomocí rozhraní příkazového řádku](../../key-vault/secrets/quick-create-cli.md)
- [Nastavení a načtení tajného klíče pomocí PowerShellu](../../key-vault/secrets/quick-create-powershell.md)
- [Nastavení a načtení tajného klíče pomocí portálu](../../key-vault/secrets/quick-create-portal.md)
- [Nastavení a načtení tajného klíče pomocí .NET](../../key-vault/secrets/quick-create-net.md)
- [Nastavení a načtení tajného klíče pomocí Node.js](../../key-vault/secrets/quick-create-node.md)

## <a name="grant-access-to-the-secrets"></a>Udělení přístupu k tajným klíčům

Uživatel, který šablonu nasadí, musí mít `Microsoft.KeyVault/vaults/deploy/action` oprávnění pro rozsah skupiny prostředků a trezoru klíčů. Tento přístup udělují i role [vlastníka](../../role-based-access-control/built-in-roles.md#owner) a [Přispěvatel](../../role-based-access-control/built-in-roles.md#contributor) . Pokud jste vytvořili Trezor klíčů, jste vlastníkem, abyste měli oprávnění.

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

    V ukázkách se uživateli přiřadí vlastní role na úrovni skupiny prostředků.

Při použití Key Vault se šablonou pro [spravovanou aplikaci](../managed-applications/overview.md)je nutné udělit přístup k instančnímu objektu **poskytovatele prostředků zařízení** . Další informace najdete v tématu [přístup Key Vault tajného klíče při nasazení Azure Managed Applications](../managed-applications/key-vault-access.md).

## <a name="reference-secrets-with-static-id"></a>Referenční tajné klíče se statickým ID

S tímto přístupem odkazujete na Trezor klíčů v souboru parametrů, nikoli na šablonu. Následující obrázek ukazuje, jak soubor parametrů odkazuje na tajný kód a předá tuto hodnotu šabloně.

![Správce prostředků diagramu statického ID integrace trezoru klíčů](./media/key-vault-parameter/statickeyvault.png)

[Kurz: integrování Azure Key Vault v Správce prostředků Template Deployment](./template-tutorial-use-key-vault.md) používá tuto metodu.

Následující šablona nasadí SQL Server, který obsahuje heslo správce. Parametr password je nastaven na zabezpečený řetězec. Ale šablona neurčuje, ze které hodnoty pochází.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

Nyní vytvořte soubor parametrů pro předchozí šablonu. V souboru parametrů zadejte parametr, který odpovídá názvu parametru v šabloně. Pro hodnotu parametru odkázat na tajný klíč z trezoru klíčů. Odkazujte na tajný klíč předáním identifikátoru prostředku trezoru klíčů a názvu tajného kódu:

V následujícím souboru parametrů již musí existovat tajný klíč trezoru klíčů a pro jeho ID prostředku poskytnete statickou hodnotu.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
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

Pokud potřebujete použít jinou verzi tajného klíče, než je aktuální verze, použijte `secretVersion` vlastnost.

```json
"secretName": "ExamplePassword",
"secretVersion": "cd91b2b7e10e492ebb870a6ee0591b68"
```

Nasaďte šablonu a předejte ji do souboru parametrů:

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

Předchozí část ukázala, jak předat identifikátor statického prostředku pro tajný klíč trezoru klíčů z parametru. V některých scénářích ale musíte odkazovat na tajný klíč trezoru klíčů, který se liší v závislosti na aktuálním nasazení. Nebo můžete chtít předat hodnoty parametrů do šablony, nikoli vytvořit parametr odkazu v souboru parametrů. V obou případech můžete dynamicky generovat ID prostředku pro tajný kód trezoru klíčů pomocí propojené šablony.

V souboru parametrů nelze dynamicky generovat ID prostředku, protože výrazy šablony nejsou povoleny v souboru parametrů.

V nadřazené šabloně přidáte vnořenou šablonu a předáte parametr, který obsahuje dynamicky generované ID prostředku. Následující obrázek ukazuje, jak parametr v propojené šabloně odkazuje na tajný kód.

![Dynamické ID](./media/key-vault-parameter/dynamickeyvault.png)

Následující šablona dynamicky vytvoří ID trezoru klíčů a předá ho jako parametr.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
      "apiVersion": "2020-10-01",
      "name": "dynamicSecret",
      "properties": {
        "mode": "Incremental",
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

- Obecné informace o trezorech klíčů najdete v tématu [co je Azure Key Vault?](../../key-vault/general/overview.md).
- Kompletní Příklady referenčních tajných klíčů najdete v tématu [Key Vault příklady](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
- Microsoft Learn modul, který pokrývá předávání zabezpečené hodnoty z trezoru klíčů, najdete v tématu [Správa složitých nasazení cloudu pomocí pokročilých funkcí šablon ARM](/learn/modules/manage-deployments-advanced-arm-template-features/).
