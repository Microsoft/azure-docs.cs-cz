---
title: Konfigurace klíčů spravovaných zákazníkem v Azure Průzkumník dat pomocí šablony Azure Resource Manager
description: Tento článek popisuje, jak nakonfigurovat šifrování klíčů spravovaných zákazníkem pro vaše data v Azure Průzkumník dat pomocí Azure Resource Manager šablony.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: bff8c50cdece803e030c0975a9b14ac5739baaf7
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2020
ms.locfileid: "75725803"
---
# <a name="configure-customer-managed-keys-using-the-azure-resource-manager-template"></a>Konfigurace klíčů spravovaných zákazníkem pomocí šablony Azure Resource Manager

> [!div class="op_single_selector"]
> * [C#](create-cluster-database-csharp.md)
> * [Šablona Azure Resource Manageru](create-cluster-database-resource-manager.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](../../includes/data-explorer-configure-customer-managed-keys.md)]

## <a name="configure-encryption-with-customer-managed-keys"></a>Konfigurace šifrování pomocí klíčů spravovaných zákazníkem

V této části nakonfigurujete klíče spravované zákazníky pomocí šablon Azure Resource Manager. Ve výchozím nastavení používá šifrování Azure Průzkumník dat klíče spravované společností Microsoft. V tomto kroku Nakonfigurujte cluster Azure Průzkumník dat tak, aby používal klíče spravované zákazníkem, a zadejte klíč, který se má přidružit ke clusteru.

Šablonu Azure Resource Manager můžete nasadit pomocí Azure Portal nebo pomocí PowerShellu.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "clusterName": {
      "type": "string",
      "defaultValue": "[concat('kusto', uniqueString(resourceGroup().id))]",
      "metadata": {
        "description": "Name of the cluster to create"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {},
  "resources": [
    {
      "name": "[parameters('clusterName')]",
      "type": "Microsoft.Kusto/clusters",
      "sku": {
        "name": "Standard_D13_v2",
        "tier": "Standard",
        "capacity": 2
      },
      "apiVersion": "2019-09-07",
      "location": "[parameters('location')]",
      "properties": {
        "keyVaultProperties": {
          "keyVaultUri": "<keyVaultUri>",
          "keyName": "<keyName>",
          "keyVersion": "<keyVersion"
        }
      }
    }
  ]
}
```

## <a name="update-the-key-version"></a>Aktualizace verze klíče

Když vytváříte novou verzi klíče, budete muset cluster aktualizovat, aby používal novou verzi. Nejdřív zavolejte `Get-AzKeyVaultKey`, abyste získali nejnovější verzi klíče. Pak aktualizujte vlastnosti trezoru klíčů clusteru tak, aby používaly novou verzi klíče, jak je znázorněno v části [Konfigurace šifrování pomocí klíčů spravovaných zákazníkem](#configure-encryption-with-customer-managed-keys).

## <a name="next-steps"></a>Další kroky

* [Zabezpečení clusterů Azure Průzkumník dat v Azure](security.md)
* [Konfigurace spravovaných identit pro cluster Azure Průzkumník dat](managed-identities.md)
* [Zabezpečte svůj cluster v Azure Průzkumník dat-Azure Portal](manage-cluster-security.md) tím, že povolíte šifrování v klidovém prostředí.
* [Konfigurace klíčů spravovaných zákazníkem pomocíC#](customer-managed-keys-csharp.md)

