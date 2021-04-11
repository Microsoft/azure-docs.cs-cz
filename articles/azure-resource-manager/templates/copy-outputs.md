---
title: Definovat více instancí výstupní hodnoty
description: Pomocí operace kopírování v šabloně Azure Resource Manager (šablona ARM) můžete iterovat víckrát při vracení hodnoty z nasazení.
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: 49050f4c0a494bbfb470b64704a09d8738a727f7
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2021
ms.locfileid: "106385730"
---
# <a name="output-iteration-in-arm-templates"></a>Výstupní iterace v šablonách ARM

V tomto článku se dozvíte, jak vytvořit více než jednu hodnotu pro výstup v šabloně Azure Resource Manager (šablona ARM). Přidáním smyčky kopírování do části Outputs (výstupy) vaší šablony můžete dynamicky vracet počet položek během nasazování.

Můžete také použít příkaz Kopírovat smyčku s [prostředky](copy-resources.md), [vlastnostmi v prostředku](copy-properties.md)a [proměnnými](copy-variables.md).

## <a name="syntax"></a>Syntax

# <a name="json"></a>[JSON](#tab/json)

Přidejte `copy` element do výstupní části šablony, aby se vracel počet položek. Element Copy má následující obecný formát:

```json
"copy": {
  "count": <number-of-iterations>,
  "input": <values-for-the-output>
}
```

`count`Vlastnost určuje počet iterací, které chcete pro výstupní hodnotu.

`input`Vlastnost určuje vlastnosti, které chcete opakovat. Vytvoříte pole prvků konstruovaných z hodnoty `input` Vlastnosti. Může se jednat o jedinou vlastnost (například řetězec) nebo o objekt s několika vlastnostmi.

# <a name="bicep"></a>[Bicep](#tab/bicep)

Cykly lze použít k vrácení počtu položek během nasazování:

- Iterace nad polem:

  ```bicep
  output <output-name> array = [for <item> in <collection>: {
    <properties>
  }]

  ```

- Iterace nad prvky pole

  ```bicep
  output <output-name> array = [for <item>, <index> in <collection>: {
    <properties>
  }]
  ```

- Index smyčky using

  ```bicep
  output <output-name> array = [for <index> in range(<start>, <stop>): {
    <properties>
  }]
  ```

---

## <a name="copy-limits"></a>Omezení kopírování

Počet nemůže být větší než 800.

Počet nemůže být záporné číslo. Pokud nasadíte šablonu s poslední verzí rozhraní příkazového řádku Azure CLI, PowerShellu nebo REST API, může to být nula. Konkrétně je nutné použít:

- Azure PowerShell **2,6** nebo novější
- Azure CLI **2.0.74** nebo novější
- REST API verze **2019-05-10** nebo novější
- [Odkazovaná nasazení](linked-templates.md) musí pro typ prostředku nasazení používat rozhraní API verze **2019-05-10** nebo novější.

Starší verze prostředí PowerShell, rozhraní příkazového řádku a REST API pro počet nepodporují nulu.

## <a name="outputs-iteration"></a>Iterace výstupu

Následující příklad vytvoří proměnný počet účtů úložiště a vrátí koncový bod pro každý účet úložiště:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageCount": {
      "type": "int",
      "defaultValue": 2
    }
  },
  "variables": {
    "baseName": "[concat('storage', uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(), variables('baseName'))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": "[parameters('storageCount')]"
      }
    }
  ],
  "outputs": {
    "storageEndpoints": {
      "type": "array",
      "copy": {
        "count": "[parameters('storageCount')]",
        "input": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]"
      }
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param storageCount int = 2

var baseName_var = 'storage${uniqueString(resourceGroup().id)}'

resource baseName 'Microsoft.Storage/storageAccounts@2019-04-01' = [for i in range(0, storageCount): {
  name: '${i}${baseName_var}'
  location: resourceGroup().location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  properties: {}
}]

output storageEndpoints array = [for i in range(0, storageCount): reference(${i}${baseName_var}).primaryEndpoints.blob]
```

---

Předchozí šablona vrátí pole s následujícími hodnotami:

```json
[
  "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
  "https://1storagecfrbqnnmpeudi.blob.core.windows.net/"
]
```

V dalším příkladu vrátí tři vlastnosti z nových účtů úložiště.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageCount": {
      "type": "int",
      "defaultValue": 2
    }
  },
  "variables": {
    "baseName": "[concat('storage', uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(), variables('baseName'))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": "[parameters('storageCount')]"
      }
    }
  ],
  "outputs": {
    "storageInfo": {
      "type": "array",
      "copy": {
        "count": "[parameters('storageCount')]",
        "input": {
          "id": "[reference(concat(copyIndex(), variables('baseName')), '2019-04-01', 'Full').resourceId]",
          "blobEndpoint": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]",
          "status": "[reference(concat(copyIndex(), variables('baseName'))).statusOfPrimary]"
        }
      }
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param storageCount int = 2

var baseName_var = 'storage${uniqueString(resourceGroup().id)}'

resource baseName 'Microsoft.Storage/storageAccounts@2019-04-01' = [for i in range(0, storageCount): {
  name: '${i}${baseName_var}'
  location: resourceGroup().location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  properties: {}
}]

output storageInfo array = [for i in range(0, storageCount): {
  id: reference(concat(i, baseName_var), '2019-04-01', 'Full').resourceId
  blobEndpoint: reference(concat(i, baseName_var)).primaryEndpoints.blob
  status: reference(concat(i, baseName_var)).statusOfPrimary
}]
```

---

Předchozí příklad vrátí pole s následujícími hodnotami:

```json
[
  {
    "id": "Microsoft.Storage/storageAccounts/0storagecfrbqnnmpeudi",
    "blobEndpoint": "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
    "status": "available"
  },
  {
    "id": "Microsoft.Storage/storageAccounts/1storagecfrbqnnmpeudi",
    "blobEndpoint": "https://1storagecfrbqnnmpeudi.blob.core.windows.net/",
    "status": "available"
  }
]
```

## <a name="next-steps"></a>Další kroky

- Kurz najdete v tématu [kurz: vytvoření více instancí prostředků pomocí šablon ARM](template-tutorial-create-multiple-instances.md).
- Další použití smyčky kopírování najdete v těchto tématech:
  - [Iterace prostředků v šablonách ARM](copy-resources.md)
  - [Iterace vlastnosti v šablonách ARM](copy-properties.md)
  - [Iterace proměnných v šablonách ARM](copy-variables.md)
- Pokud se chcete dozvědět o oddílech šablony, přečtěte si téma [pochopení struktury a syntaxe šablon ARM](template-syntax.md).
- Informace o tom, jak šablonu nasadit, najdete v tématu [nasazení prostředků pomocí šablon ARM a Azure PowerShell](deploy-powershell.md).
