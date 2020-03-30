---
title: Definování více instancí vlastnosti
description: Při vytváření vlastnosti na prostředku použijte operaci kopírování v šabloně Azure Resource Manager k větší iterátování.
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: e86d38b0e5d2e39d54b3c419b6eebdcda74022db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258103"
---
# <a name="property-iteration-in-arm-templates"></a>Iterace vlastností v šablonách ARM

Tento článek ukazuje, jak vytvořit více než jednu instanci vlastnosti v šabloně Správce prostředků Azure (ARM). Přidáním elementu **copy** do části vlastností prostředku v šabloně můžete dynamicky nastavit počet položek pro vlastnost během nasazení. Také se vyhnete nutnosti opakovat syntaxi šablony.

Můžete také použít kopírování s [prostředky](copy-resources.md), [proměnnými](copy-variables.md)a [výstupy](copy-outputs.md).

## <a name="property-iteration"></a>Iterace vlastností

Prvek kopírování má následující obecný formát:

```json
"copy": [
  {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "input": <values-for-the-property>
  }
]
```

V **části Název**zadejte název vlastnosti prostředku, kterou chcete vytvořit. Count **count** Vlastnost určuje počet iterací, které chcete pro vlastnost.

Vlastnost **input** určuje vlastnosti, které chcete opakovat. Vytvoříte pole prvků vytvořených z hodnoty ve **vstupní** vlastnosti.

Následující příklad ukazuje, `copy` jak použít vlastnost dataDisks ve virtuálním počítači:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numberOfDataDisks": {
      "type": "int",
      "minValue": 0,
      "maxValue": 16,
      "defaultValue": 16,
      "metadata": {
        "description": "The number of dataDisks to create."
      }
    },
    ...
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2017-03-30",
      ...
      "properties": {
        "storageProfile": {
          ...
          "copy": [
            {
              "name": "dataDisks",
              "count": "[parameters('numberOfDataDisks')]",
              "input": {
                "diskSizeGB": 1023,
                "lun": "[copyIndex('dataDisks')]",
                "createOption": "Empty"
              }
            }
          ]
        }
      }
    }
  ]
}
```

Všimněte si, že při použití `copyIndex` uvnitř iterace vlastnosti, musíte zadat název iterace.

> [!NOTE]
> Iterace vlastnosti také podporuje argument posunu. Posun musí přijít za názvem iterace, například copyIndex('dataDisks', 1).
>

Správce prostředků rozšiřuje `copy` pole během nasazení. Název pole se stane názvem vlastnosti. Vstupní hodnoty se stanou vlastnostmi objektu. Nasazená šablona se stane:

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2017-03-30",
  "properties": {
    "storageProfile": {
      "dataDisks": [
        {
          "lun": 0,
          "createOption": "Empty",
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "createOption": "Empty",
          "diskSizeGB": 1023
        },
        {
          "lun": 2,
          "createOption": "Empty",
          "diskSizeGB": 1023
        }
      ],
      ...
```

Element copy je pole, takže můžete zadat více než jednu vlastnost prostředku.

```json
{
  "type": "Microsoft.Network/loadBalancers",
  "apiVersion": "2017-10-01",
  "name": "exampleLB",
  "properties": {
    "copy": [
      {
        "name": "loadBalancingRules",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      },
      {
        "name": "probes",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      }
    ]
  }
}
```

Iterace prostředků a vlastností můžete použít společně. Odkazit na iteraci vlastnosti podle názvu.

```json
{
  "type": "Microsoft.Network/virtualNetworks",
  "apiVersion": "2018-04-01",
  "name": "[concat(parameters('vnetname'), copyIndex())]",
  "copy":{
    "count": 2,
    "name": "vnetloop"
  },
  "location": "[resourceGroup().location]",
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "[parameters('addressPrefix')]"
      ]
    },
    "copy": [
      {
        "name": "subnets",
        "count": 2,
        "input": {
          "name": "[concat('subnet-', copyIndex('subnets'))]",
          "properties": {
            "addressPrefix": "[variables('subnetAddressPrefix')[copyIndex('subnets')]]"
          }
        }
      }
    ]
  }
}
```

## <a name="copy-limits"></a>Kopírovat limity

Počet nesmí překročit 800.

Počet nemůže být záporné číslo. Pokud nasadíte šablonu s Azure PowerShell 2.6 nebo novější, Azure CLI 2.0.74 nebo novější, nebo REST API verze **2019-05-10** nebo novější, můžete nastavit počet na nulu. Dřívější verze PowerShellu, ROZHRANÍ CLI a rozhraní REST API nepodporují nulu pro počet.

## <a name="example-templates"></a>Ukázkové šablony

Následující příklad ukazuje běžný scénář pro vytvoření více než jednu hodnotu pro vlastnost.

|Šablona  |Popis  |
|---------|---------|
|[Nasazení virtuálního počítače s proměnným počtem datových disků](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Nasadí několik datových disků s virtuálním počítačem. |

## <a name="next-steps"></a>Další kroky

* Chcete-li projít kurz, [najdete v tématu Kurz: vytvoření více instancí prostředků pomocí ARM šablony](template-tutorial-create-multiple-instances.md).
* Další použití prvku kopírování naleznete v následujících tématech:
  * [Iterace prostředků v šablonách ARM](copy-resources.md)
  * [Iterace proměnných v šablonách ARM](copy-variables.md)
  * [Výstupní iterace v šablonách ARM](copy-outputs.md)
* Pokud se chcete dozvědět o částech šablony, [přečtěte si](template-syntax.md)část Vytváření šablon ARM .
* Informace o nasazení šablony najdete [v tématu Nasazení aplikace pomocí šablony ARM](deploy-powershell.md).

