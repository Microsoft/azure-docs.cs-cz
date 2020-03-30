---
title: Umístění zdroje šablony
description: Popisuje, jak nastavit umístění prostředků v šabloně Azure Resource Manager.
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: a8324dac1232eecd5624e5f1dc0e6656295c0a10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156425"
---
# <a name="set-resource-location-in-arm-template"></a>Nastavení umístění prostředků v šabloně ARM

Při nasazování šablony Správce prostředků Azure (ARM) je nutné zadat umístění pro každý prostředek. Umístění nemusí být ve stejném umístění jako umístění skupiny prostředků.

## <a name="get-available-locations"></a>Získat dostupná místa

Různé typy prostředků jsou podporovány v různých umístěních. Pokud chcete získat podporovaná umístění pro typ prostředku, použijte Azure PowerShell nebo Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes `
  | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az provider show \
  --namespace Microsoft.Batch \
  --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" \
  --out table
```

---

## <a name="use-location-parameter"></a>Použít parametr umístění

Chcete-li umožnit flexibilitu při nasazování šablony, použijte parametr k určení umístění prostředků. Nastavte výchozí hodnotu parametru na `resourceGroup().location`.

Následující příklad ukazuje účet úložiště, který je nasazen do umístění určeného jako parametr:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
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
  "variables": {
    "storageAccountName": "[concat('storage', uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2018-07-01",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {}
    }
  ],
  "outputs": {
    "storageAccountName": {
      "type": "string",
      "value": "[variables('storageAccountName')]"
    }
  }
}
```

## <a name="next-steps"></a>Další kroky

* Úplný seznam funkcí šablony najdete v tématu [Funkce šablony Správce prostředků Azure](template-functions.md).
* Další informace o souborech šablon naleznete [v tématu Principy struktury a syntaxe šablon ARM](template-syntax.md).
