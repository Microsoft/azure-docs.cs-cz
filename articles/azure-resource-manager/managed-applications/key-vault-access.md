---
title: Použití trezoru klíčů při nasazování spravované aplikace
description: Ukazuje, jak používat tajné kódy přístupu v azure key vault při nasazování spravovaných aplikací
author: tfitzmac
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: 5dc219747b9cc74b6c6aac8ab190ebfbe5ae9b32
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458279"
---
# <a name="access-key-vault-secret-when-deploying-azure-managed-applications"></a>Přístup k tajným klíčům trezoru klíčů při nasazování spravovaných aplikací Azure

Když potřebujete předat zabezpečenou hodnotu (jako heslo) jako parametr během nasazení, můžete načíst hodnotu z [trezoru klíčů Azure](../../key-vault/general/overview.md). Chcete-li získat přístup k trezoru klíčů při nasazování spravovaných aplikací, musíte udělit přístup k instančnímu objektu **zprostředkovatele prostředků zařízení.** Služba Spravované aplikace používá tuto identitu ke spuštění operací. Chcete-li úspěšně načíst hodnotu z trezoru klíčů během nasazení, musí mít instanční objekt přístup k trezoru klíčů.

Tento článek popisuje, jak nakonfigurovat trezor klíčů pro práci se spravovanými aplikacemi.

## <a name="enable-template-deployment"></a>Povolení nasazení šablony

1. Na portálu vyberte trezor klíčů.

1. Vyberte **Zásady přístupu**.   

   ![Vybrat zásady přístupu](./media/key-vault-access/select-access-policies.png)

1. Vyberte **Kliknutím zobrazíte pokročilé zásady přístupu**.

   ![Zobrazit rozšířené zásady přístupu](./media/key-vault-access/advanced.png)

1. Vyberte **Povolit přístup ke Správci prostředků Azure pro nasazení šablony**. Potom vyberte **Uložit**.

   ![Povolení nasazení šablony](./media/key-vault-access/enable-template.png)

## <a name="add-service-as-contributor"></a>Přidání služby jako přispěvatele

1. Vyberte **řízení přístupu (IAM)**.

   ![Vybrat řízení přístupu](./media/key-vault-access/access-control.png)

1. Vyberte **Přidat přiřazení role**.

   ![Výběr možnosti Přidat](./media/key-vault-access/add-access-control.png)

1. Vyberte **přispěvatele** pro roli. Vyhledejte **zprostředkovatele prostředků zařízení** a vyberte jej z dostupných možností.

   ![Hledat zprostředkovatele](./media/key-vault-access/search-provider.png)

1. Vyberte **Uložit**.

## <a name="reference-key-vault-secret"></a>Tajný klíč trezoru referenčního klíče

Chcete-li předat tajný klíč z trezoru klíčů do šablony ve spravované aplikaci, musíte použít [propojenou nebo vnořenou šablonu](../templates/linked-templates.md) a odkazovat na trezor klíčů v parametrech propojené nebo vnořené šablony. Zadejte ID prostředku trezoru klíčů a název tajného klíče.

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

Nakonfigurovali jste trezor klíčů tak, aby byl přístupný během nasazení spravované aplikace.

* Informace o předání hodnoty z trezoru klíčů jako parametršablony naleznete v [tématu Použití trezoru klíčů Azure k předání hodnoty zabezpečeného parametru během nasazení](../templates/key-vault-parameter.md).
* Příklady spravovaných aplikací najdete v [tématu Ukázkové projekty pro spravované aplikace Azure](sample-projects.md).
* Pokud chcete zjistit, jak vytvořit definiční soubor uživatelského rozhraní pro spravovanou aplikaci, přečtěte si téma [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
