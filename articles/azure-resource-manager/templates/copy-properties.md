---
title: Definování více instancí vlastnosti
description: Použijte operaci kopírování v šabloně Azure Resource Manager k iterování několikrát při vytváření vlastnosti prostředku.
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: b759389cd1065c399658bd8d0c1ddd263054697c
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2020
ms.locfileid: "77622865"
---
# <a name="property-iteration-in-azure-resource-manager-templates"></a>Iterace vlastnosti v šablonách Azure Resource Manager

V tomto článku se dozvíte, jak vytvořit více než jednu instanci vlastnosti v šabloně Azure Resource Manager. Přidáním elementu **kopírování** do oddílu Vlastnosti prostředku ve vaší šabloně můžete dynamicky nastavit počet položek pro vlastnost během nasazení. Nemusíte se také vyhnout opakování syntaxe šablony.

Můžete také použít kopírování s [prostředky](copy-resources.md), [proměnnými](copy-variables.md)a [výstupy](copy-outputs.md).

## <a name="property-iteration"></a>Iterace vlastnosti

Element Copy má následující obecný formát:

```json
"copy": [
  {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "input": <values-for-the-property>
  }
]
```

Do pole **název**zadejte název vlastnosti prostředku, kterou chcete vytvořit. Vlastnost **Count** určuje počet iterací, které chcete pro vlastnost.

Vlastnost **input** určuje vlastnosti, které chcete opakovat. Vytvoříte pole prvků vytvořené z hodnoty vlastnosti **input** .

Následující příklad ukazuje, jak použít `copy` na vlastnost datadisks na virtuálním počítači:

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

Všimněte si, že při použití `copyIndex` v rámci iterace vlastnosti je nutné zadat název iterace.

> [!NOTE]
> Iterace vlastnosti také podporuje argument posunu. Posun musí být zadán za názvem iterace, například copyIndex (' datadisks ', 1).
>

Správce prostředků rozbalí `copy` pole během nasazování. Název pole se zobrazí jako název vlastnosti. Vstupní hodnoty se stanou vlastnostmi objektu. Nasazená šablona bude:

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

Element Copy je pole, abyste mohli zadat více než jednu vlastnost prostředku.

```json
{
  "type": "Microsoft.Network/loadBalancers",
  "apiVersion": "2017-10-01",
  "name": "examleLB",
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

Můžete použít iteraci prostředků a vlastností společně. Odkázat na iteraci vlastnosti podle názvu.

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

## <a name="copy-limits"></a>Omezení kopírování

Počet nemůže být větší než 800.

Počet nemůže být záporné číslo. Pokud nasadíte šablonu s Azure PowerShell 2,6 nebo novějším, Azure CLI 2.0.74 nebo novějším nebo REST API verze **2019-05-10** nebo novější, můžete nastavit počet na nula. Starší verze prostředí PowerShell, rozhraní příkazového řádku a REST API pro počet nepodporují nulu.

## <a name="example-templates"></a>Příklad šablony

Následující příklad ukazuje běžný scénář pro vytvoření více než jedné hodnoty pro vlastnost.

|Šablona  |Popis  |
|---------|---------|
|[Nasazení virtuálního počítače s proměnným počtem datových disků](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Nasadí několik datových disků s virtuálním počítačem. |

## <a name="next-steps"></a>Další kroky

* Kurz najdete v tématu [kurz: vytvoření více instancí prostředků pomocí šablon Správce prostředků](template-tutorial-create-multiple-instances.md).
* Pro jiné použití kopie elementu viz:
  * [Iterace prostředků v šablonách Azure Resource Manager](copy-resources.md)
  * [Iterace proměnné v šablonách Azure Resource Manager](copy-variables.md)
  * [Výstupní iterace v šablonách Azure Resource Manager](copy-outputs.md)
* Pokud se chcete dozvědět o oddílech šablony, přečtěte si téma [vytváření Azure Resource Manager šablon](template-syntax.md).
* Informace o tom, jak šablonu nasadit, najdete v tématu [nasazení aplikace pomocí šablony Azure Resource Manager](deploy-powershell.md).

