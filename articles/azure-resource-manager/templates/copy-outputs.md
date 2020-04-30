---
title: Definovat více instancí výstupní hodnoty
description: Použijte operaci kopírování v šabloně Azure Resource Manager k iterování více časů při vracení hodnoty z nasazení.
ms.topic: conceptual
ms.date: 04/17/2020
ms.openlocfilehash: 0315af2f083285c4704b08fec608341b6f0b2231
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81617828"
---
# <a name="output-iteration-in-arm-templates"></a>Výstupní iterace v šablonách ARM

V tomto článku se dozvíte, jak vytvořit více než jednu hodnotu pro výstup v šabloně Azure Resource Manager (ARM). Přidáním elementu **kopírování** do části výstupy ve vaší šabloně můžete dynamicky vracet počet položek během nasazování.

Můžete také použít kopírování s [prostředky](copy-resources.md), [vlastnosti v prostředku](copy-properties.md)a [proměnné](copy-variables.md).

## <a name="outputs-iteration"></a>Iterace výstupu

Element Copy má následující obecný formát:

```json
"copy": {
  "count": <number-of-iterations>,
  "input": <values-for-the-output>
}
```

Vlastnost **Count** určuje počet iterací, které chcete pro výstupní hodnotu.

Vlastnost **input** určuje vlastnosti, které chcete opakovat. Vytvoříte pole prvků vytvořené z hodnoty vlastnosti **input** . Může se jednat o jedinou vlastnost (například řetězec) nebo o objekt s několika vlastnostmi.

Následující příklad vytvoří proměnný počet účtů úložiště a vrátí koncový bod pro každý účet úložiště:

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

Předchozí šablona vrátí pole s následujícími hodnotami:

```json
[
    "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
    "https://1storagecfrbqnnmpeudi.blob.core.windows.net/"
]
```

V dalším příkladu vrátí tři vlastnosti z nových účtů úložiště.

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

* Pokud chcete projít kurz, přečtěte si [kurz: vytvoření více instancí prostředků pomocí šablon ARM](template-tutorial-create-multiple-instances.md).
* Pro jiné použití kopie elementu viz:
  * [Iterace prostředků v šablonách ARM](copy-resources.md)
  * [Iterace vlastnosti v šablonách ARM](copy-properties.md)
  * [Iterace proměnných v šablonách ARM](copy-variables.md)
* Pokud se chcete dozvědět o oddílech šablony, přečtěte si téma [vytváření šablon ARM](template-syntax.md).
* Informace o tom, jak šablonu nasadit, najdete v tématu [nasazení aplikace se šablonou ARM](deploy-powershell.md).

