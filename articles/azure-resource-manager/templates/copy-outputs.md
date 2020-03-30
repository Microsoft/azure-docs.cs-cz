---
title: Definování více instancí výstupní hodnoty
description: Při vracení hodnoty z nasazení použijte operaci kopírování v šabloně Správce prostředků Azure k větší iterace.
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: 3889260d02f438274c80e99e99136515499443e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153382"
---
# <a name="output-iteration-in-arm-templates"></a>Výstupní iterace v šablonách ARM

Tento článek ukazuje, jak vytvořit více než jednu hodnotu pro výstup v šabloně Správce prostředků Azure (ARM). Přidáním elementu **copy** do části výstupy šablony můžete dynamicky vrátit počet položek během nasazení.

Můžete také použít kopii s [prostředky](copy-resources.md), [vlastnostmi v prostředku](copy-properties.md)a [proměnnými](copy-variables.md).

## <a name="outputs-iteration"></a>Iterace výstupů

Prvek kopírování má následující obecný formát:

```json
"copy": [
  {
    "count": <number-of-iterations>,
    "input": <values-for-the-variable>
  }
]
```

Vlastnost **count** určuje počet iterací, které chcete pro výstupní hodnotu.

Vlastnost **input** určuje vlastnosti, které chcete opakovat. Vytvoříte pole prvků vytvořených z hodnoty ve **vstupní** vlastnosti. Může se jedná o jednu vlastnost (například řetězec) nebo objekt s několika vlastnostmi.

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

Další příklad vrátí tři vlastnosti z nových účtů úložiště.

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

* Chcete-li projít kurz, [najdete v tématu Kurz: vytvoření více instancí prostředků pomocí ARM šablony](template-tutorial-create-multiple-instances.md).
* Další použití prvku kopírování naleznete v následujících tématech:
  * [Iterace prostředků v šablonách ARM](copy-resources.md)
  * [Iterace vlastností v šablonách ARM](copy-properties.md)
  * [Iterace proměnných v šablonách ARM](copy-variables.md)
* Pokud se chcete dozvědět o částech šablony, [přečtěte si](template-syntax.md)část Vytváření šablon ARM .
* Informace o nasazení šablony najdete [v tématu Nasazení aplikace pomocí šablony ARM](deploy-powershell.md).

