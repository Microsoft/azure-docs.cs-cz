---
title: Při nasazování spravované aplikace použít Key Vault
description: Ukazuje, jak používat přístupové tajné kódy v Azure Key Vault při nasazování spravovaných aplikací.
author: tfitzmac
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: 5dc219747b9cc74b6c6aac8ab190ebfbe5ae9b32
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "81458279"
---
# <a name="access-key-vault-secret-when-deploying-azure-managed-applications"></a>Přístup Key Vault tajného klíče při nasazení Azure Managed Applications

Pokud během nasazení potřebujete předat zabezpečenou hodnotu (třeba heslo) jako parametr, můžete načíst hodnotu z [Azure Key Vault](../../key-vault/general/overview.md). Pro přístup k Key Vault při nasazování spravovaných aplikací musíte udělit přístup k instančnímu objektu **poskytovatele prostředků zařízení** . Služba spravovaných aplikací používá tuto identitu ke spuštění operací. Chcete-li úspěšně načíst hodnotu z Key Vault během nasazení, instanční objekt musí být schopný získat přístup k Key Vault.

Tento článek popisuje, jak nakonfigurovat Key Vault pro práci se spravovanými aplikacemi.

## <a name="enable-template-deployment"></a>Povolit nasazení šablony

1. Na portálu vyberte svůj Key Vault.

1. Vyberte **Zásady přístupu**.   

   ![Vybrat zásady přístupu](./media/key-vault-access/select-access-policies.png)

1. Vyberte **Kliknutím zobrazíte pokročilé zásady přístupu**.

   ![Zobrazit pokročilé zásady přístupu](./media/key-vault-access/advanced.png)

1. **Pro nasazení šablony vyberte Povolit přístup k Azure Resource Manager**. Pak vyberte **Uložit**.

   ![Povolit nasazení šablony](./media/key-vault-access/enable-template.png)

## <a name="add-service-as-contributor"></a>Přidat službu jako přispěvatele

1. Vyberte **Řízení přístupu (IAM)** .

   ![Vybrat řízení přístupu](./media/key-vault-access/access-control.png)

1. Vyberte **Přidat přiřazení role**.

   ![Výběr možnosti Přidat](./media/key-vault-access/add-access-control.png)

1. Jako role vyberte **Přispěvatel** . Vyhledejte **poskytovatele prostředků zařízení** a vyberte ho z dostupných možností.

   ![Hledat poskytovatele](./media/key-vault-access/search-provider.png)

1. Vyberte **Uložit**.

## <a name="reference-key-vault-secret"></a>Reference Key Vault tajný klíč

Chcete-li předat tajný kód z Key Vault do šablony ve spravované aplikaci, je nutné použít [propojenou nebo vnořenou šablonu](../templates/linked-templates.md) a odkazovat na Key Vault v parametrech pro propojenou nebo vnořenou šablonu. Zadejte ID prostředku Key Vault a název tajného klíče.

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

Nakonfigurovali jste Key Vault, aby byly dostupné během nasazování spravované aplikace.

* Informace o předání hodnoty z Key Vault jako parametru šablony naleznete v tématu [použití Azure Key Vault k předání hodnoty zabezpečeného parametru během nasazování](../templates/key-vault-parameter.md).
* Příklady spravovaných aplikací najdete v tématu [Ukázkové projekty pro spravované aplikace Azure](sample-projects.md).
* Pokud chcete zjistit, jak vytvořit definiční soubor uživatelského rozhraní pro spravovanou aplikaci, přečtěte si téma [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
