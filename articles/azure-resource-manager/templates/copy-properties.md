---
title: Definování více instancí vlastnosti
description: Použijte operaci kopírování v šabloně Azure Resource Manager k iterování několikrát při vytváření vlastnosti prostředku.
ms.topic: conceptual
ms.date: 09/15/2020
ms.openlocfilehash: f199872d5bb8a0333bf7bedb9501a6ca1b884691
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90605239"
---
# <a name="property-iteration-in-arm-templates"></a>Iterace vlastnosti v šablonách ARM

V tomto článku se dozvíte, jak vytvořit více než jednu instanci vlastnosti v šabloně Azure Resource Manager (šablona ARM). Přidáním elementu **kopírování** do oddílu Vlastnosti prostředku ve vaší šabloně můžete dynamicky nastavit počet položek pro vlastnost během nasazení. Nemusíte se také vyhnout opakování syntaxe šablony.

Kopírovat můžete pouze pomocí prostředků nejvyšší úrovně, i když aplikujete kopírování na vlastnost. Další informace o změně podřízeného prostředku na prostředek nejvyšší úrovně najdete v tématu [iterace u podřízeného prostředku](copy-resources.md#iteration-for-a-child-resource).

Můžete také použít kopírování s [prostředky](copy-resources.md), [proměnnými](copy-variables.md)a [výstupy](copy-outputs.md).

## <a name="syntax"></a>Syntax

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

Do pole **název**zadejte název vlastnosti prostředku, kterou chcete vytvořit.

Vlastnost **Count** určuje počet iterací, které chcete pro vlastnost.

Vlastnost **input** určuje vlastnosti, které chcete opakovat. Vytvoříte pole prvků vytvořené z hodnoty vlastnosti **input** .

## <a name="copy-limits"></a>Omezení kopírování

Počet nemůže být větší než 800.

Počet nemůže být záporné číslo. Pokud nasadíte šablonu s poslední verzí rozhraní příkazového řádku Azure CLI, PowerShellu nebo REST API, může to být nula. Konkrétně je nutné použít:

* Azure PowerShell **2,6** nebo novější
* Azure CLI **2.0.74** nebo novější
* REST API verze **2019-05-10** nebo novější
* [Odkazovaná nasazení](linked-templates.md) musí pro typ prostředku nasazení používat rozhraní API verze **2019-05-10** nebo novější.

Starší verze prostředí PowerShell, rozhraní příkazového řádku a REST API pro počet nepodporují nulu.

## <a name="property-iteration"></a>Iterace vlastnosti

Následující příklad ukazuje, jak použít `copy` vlastnost Datadisks na virtuálním počítači:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

Všimněte si, že při použití `copyIndex` v rámci iterace vlastnosti je nutné zadat název iterace. Iterace vlastnosti také podporuje argument posunu. Posun musí být zadán za názvem iterace, například copyIndex (' datadisks ', 1).

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

Operace kopírování je užitečná při práci s poli, protože můžete iterovat přes každý prvek v poli. Použijte `length` funkci v poli k určení počtu iterací a `copyIndex` k načtení aktuálního indexu v poli.

Následující příklad šablony vytvoří skupinu převzetí služeb při selhání pro databáze, které jsou předány jako pole.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "primaryServerName": {
            "type": "string"
        },
        "secondaryServerName": {
            "type": "string"
        },
        "databaseNames": {
            "type": "array",
            "defaultValue": [
                "mydb1",
                "mydb2",
                "mydb3"
            ]
        }
    },
    "variables": {
        "failoverName": "[concat(parameters('primaryServerName'),'/', parameters('primaryServerName'),'failovergroups')]"
    },
    "resources": [
        {
            "type": "Microsoft.Sql/servers/failoverGroups",
            "apiVersion": "2015-05-01-preview",
            "name": "[variables('failoverName')]",
            "properties": {
                "readWriteEndpoint": {
                    "failoverPolicy": "Automatic",
                    "failoverWithDataLossGracePeriodMinutes": 60
                },
                "readOnlyEndpoint": {
                    "failoverPolicy": "Disabled"
                },
                "partnerServers": [
                    {
                        "id": "[resourceId('Microsoft.Sql/servers', parameters('secondaryServerName'))]"
                    }
                ],
                "copy": [
                    {
                        "name": "databases",
                        "count": "[length(parameters('databaseNames'))]",
                        "input": "[resourceId('Microsoft.Sql/servers/databases', parameters('primaryServerName'), parameters('databaseNames')[copyIndex('databases')])]"
                    }
                ]
            }
        }
    ],
    "outputs": {
    }
}
```

Element Copy je pole, abyste mohli zadat více než jednu vlastnost prostředku.

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

## <a name="example-templates"></a>Příklady šablon

Následující příklad ukazuje běžný scénář pro vytvoření více než jedné hodnoty pro vlastnost.

|Šablona  |Description  |
|---------|---------|
|[Nasazení virtuálního počítače s proměnným počtem datových disků](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Nasadí několik datových disků s virtuálním počítačem. |

## <a name="next-steps"></a>Další kroky

* Pokud chcete projít kurz, přečtěte si [kurz: vytvoření více instancí prostředků pomocí šablon ARM](template-tutorial-create-multiple-instances.md).
* Pro jiné použití kopie elementu viz:
  * [Iterace prostředků v šablonách ARM](copy-resources.md)
  * [Iterace proměnných v šablonách ARM](copy-variables.md)
  * [Výstupní iterace v šablonách ARM](copy-outputs.md)
* Pokud se chcete dozvědět o oddílech šablony, přečtěte si téma [vytváření šablon ARM](template-syntax.md).
* Informace o tom, jak šablonu nasadit, najdete v tématu [nasazení aplikace se šablonou ARM](deploy-powershell.md).

