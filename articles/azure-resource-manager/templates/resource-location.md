---
title: Umístění prostředku šablony
description: Popisuje, jak nastavit umístění prostředku v šabloně Azure Resource Manager (šablona ARM).
ms.topic: conceptual
ms.date: 09/04/2019
ms.custom: ''
ms.openlocfilehash: 84a818109e6681b8d0e18de4d2d7969310582818
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96922408"
---
# <a name="set-resource-location-in-arm-template"></a>Nastavení umístění prostředku v šabloně ARM

Při nasazování šablony Azure Resource Manager (šablona ARM) je nutné zadat umístění každého prostředku. Toto umístění nemusí být stejné jako umístění skupiny prostředků.

## <a name="get-available-locations"></a>Získat dostupná umístění

Různé typy prostředků jsou podporovány v různých umístěních. Pro získání podporovaných umístění pro typ prostředku použijte Azure PowerShell nebo Azure CLI.

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

K zajištění flexibility při nasazování šablony použijte parametr k určení umístění pro prostředky. Nastavte výchozí hodnotu parametru na `resourceGroup().location` .

Následující příklad ukazuje účet úložiště, který je nasazený do umístění zadaného jako parametr:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

* Úplný seznam funkcí šablon najdete v tématu [funkce šablon ARM](template-functions.md).
* Další informace o souborech šablon naleznete v tématu [pochopení struktury a syntaxe šablon ARM](template-syntax.md).
