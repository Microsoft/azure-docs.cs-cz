---
title: Konfigurace klíčů spravovaných zákazníky v Azure Data Exploreru pomocí šablony Azure Resource Manager
description: Tento článek popisuje, jak nakonfigurovat šifrování klíčů spravovaných zákazníky na vašich datech v Průzkumníku dat Azure pomocí šablony Azure Resource Manager.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 454a80089b5f74d4a70015ffcd03d0212e8c08a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297915"
---
# <a name="configure-customer-managed-keys-using-the-azure-resource-manager-template"></a>Konfigurace klíčů spravovaných zákazníkem pomocí šablony Azure Resource Manager

> [!div class="op_single_selector"]
> * [Portál](customer-managed-keys-portal.md)
> * [C #](customer-managed-keys-csharp.md)
> * [Šablona Azure Resource Manageru](customer-managed-keys-resource-manager.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](../../includes/data-explorer-configure-customer-managed-keys.md)]

[!INCLUDE [data-explorer-configure-customer-managed-keys part 2](../../includes/data-explorer-configure-customer-managed-keys-b.md)]

## <a name="configure-encryption-with-customer-managed-keys"></a>Konfigurace šifrování pomocí klíčů spravovaných zákazníkem

V této části nakonfigurujete klíče spravované zákazníky pomocí šablon Azure Resource Manager. Ve výchozím nastavení používá šifrování Azure Data Explorer u klíčů spravovaných společností Microsoft. V tomto kroku nakonfigurujte cluster Průzkumníka dat Azure tak, aby používal klíče spravované zákazníky, a zadejte klíč, který chcete k clusteru přidružit.

Šablonu Azure Resource Manageru můžete nasadit pomocí portálu Azure nebo pomocí PowerShellu.

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

Když vytvoříte novou verzi klíče, budete muset aktualizovat cluster, aby se používala nová verze. Nejprve `Get-AzKeyVaultKey` zavolejte a získejte nejnovější verzi klíče. Potom aktualizujte vlastnosti trezoru klíčů clusteru tak, aby používaly novou verzi klíče, jak je znázorněno v [části Konfigurace šifrování pomocí klíčů spravovaných zákazníkem](#configure-encryption-with-customer-managed-keys).

## <a name="next-steps"></a>Další kroky

* [Zabezpečené clustery Azure Data Explorer v Azure](security.md)
* [Konfigurace spravovaných identit pro cluster Azure Data Explorer](managed-identities.md)
* [Zabezpečte svůj cluster v Azure Data Explorer – Portál Azure](manage-cluster-security.md) povolením šifrování v klidovém stavu.
* [Konfigurace klíčů spravovaných zákazníkem pomocí c #](customer-managed-keys-csharp.md)

