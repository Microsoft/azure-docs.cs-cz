---
title: Umístění prostředku Azure Resource Manager šablony
description: Popisuje, jak nastavit umístění prostředků v šabloně Azure Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: tomfitz
ms.openlocfilehash: 6af5d82766533ef7bbacab04381db9a37502923e
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70294201"
---
# <a name="set-resource-location-in-resource-manager-template"></a>Nastavení umístění prostředku v šabloně Správce prostředků

Při nasazování šablony je nutné zadat umístění každého prostředku. Toto umístění nemusí být stejné jako umístění skupiny prostředků.

## <a name="get-available-locations"></a>Získat dostupná umístění

Různé typy prostředků jsou podporovány v různých umístěních. Pro získání podporovaných umístění pro typ prostředku použijte Azure PowerShell nebo Azure CLI.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes `
  | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az provider show \
  --namespace Microsoft.Batch \
  --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" \
  --out table
```

---

## <a name="use-location-parameter"></a>Použít parametr umístění

K zajištění flexibility při nasazování šablony použijte parametr k určení umístění pro prostředky. Nastavte výchozí hodnotu parametru na `resourceGroup().location`.

Následující příklad ukazuje účet úložiště, který je nasazený do umístění zadaného jako parametr:

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
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "apiVersion": "2018-07-01",
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

* Úplný seznam funkcí šablon naleznete v tématu [Azure Resource Manager Functions Template](resource-group-template-functions.md).
* Další informace o souborech šablon naleznete v tématu [pochopení struktury a syntaxe šablon Azure Resource Manager](resource-group-authoring-templates.md).
