---
title: Proměnné šablony Azure Resource Manageru | Dokumentace Microsoftu
description: Popisuje, jak definovat proměnné v šablonách Azure Resource Manageru pomocí deklarativní syntaxe JSON.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/18/2018
ms.author: tomfitz
ms.openlocfilehash: f6c629182fdcce83c566869860480d9c70488797
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53712742"
---
# <a name="variables-section-of-azure-resource-manager-templates"></a>Části proměnných šablon Azure Resource Manageru
V sekci proměnných vytvořit hodnoty, které lze použít v celé vaší šablony. Není nutné definovat proměnné, ale často zjednodušení šablony snížením složité výrazy.

## <a name="define-and-use-a-variable"></a>Definice a používání proměnné

Následující příklad ukazuje definicí proměnné. Vytvoří hodnotu řetězce pro název účtu úložiště. Několik funkcí šablony používá k získání hodnoty parametrů a zřetězit ho do jedinečného řetězce.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

Při definování prostředku, použijte proměnnou.

```json
"resources": [
  {
    "name": "[variables('storageName')]",
    "type": "Microsoft.Storage/storageAccounts",
    ...
```

## <a name="available-definitions"></a>Dostupné definice

Předchozí příklad ukázal jedním ze způsobů k definování proměnné. Můžete použít některý z následující definice:

```json
"variables": {
    "<variable-name>": "<variable-value>",
    "<variable-name>": { 
        <variable-complex-type-value> 
    },
    "<variable-object-name>": {
        "copy": [
            {
                "name": "<name-of-array-property>",
                "count": <number-of-iterations>,
                "input": {
                    <properties-to-repeat>
                }
            }
        ]
    },
    "copy": [
        {
            "name": "<variable-array-name>",
            "count": <number-of-iterations>,
            "input": {
                <properties-to-repeat>
            }
        }
    ]
}
```

## <a name="configuration-variables"></a>Konfigurační proměnné

Komplexní typy JSON můžete použít k definování souvisejících hodnot pro prostředí. 

```json
"variables": {
    "environmentSettings": {
        "test": {
            "instanceSize": "Small",
            "instanceCount": 1
        },
        "prod": {
            "instanceSize": "Large",
            "instanceCount": 4
        }
    }
},
```

V parametrech vytvořit hodnotu určující, která konfigurace bude hodnoty použití.

```json
"parameters": {
    "environmentName": {
        "type": "string",
        "allowedValues": [
          "test",
          "prod"
        ]
    }
},
```

Aktuální nastavení s načíst:

```json
"[variables('environmentSettings')[parameters('environmentName')].instanceSize]"
```

## <a name="use-copy-element-in-variable-definition"></a>Použití elementu copy v definici proměnné

Můžete použít **kopírování** syntaxe pro vytvoření proměnné pomocí pole z několika prvků. Zadejte počet pro počet prvků. Každý prvek obsahuje vlastnosti v rámci **vstupní** objektu. Můžete kopírovat buď v rámci proměnné nebo vytvořte proměnnou. Při definování proměnné a použití **kopírování** v rámci této proměnné můžete vytvořit objekt, který má vlastnost typu pole. Při použití **kopírování** na nejvyšší úrovni a definovat jeden nebo více proměnných v rámci něj vytvořit jeden nebo více polí. Oba přístupy jsou uvedeny v následujícím příkladu:

```json
"variables": {
    "disk-array-on-object": {
        "copy": [
            {
                "name": "disks",
                "count": 3,
                "input": {
                    "name": "[concat('myDataDisk', copyIndex('disks', 1))]",
                    "diskSizeGB": "1",
                    "diskIndex": "[copyIndex('disks')]"
                }
            }
        ]
    },
    "copy": [
        {
            "name": "disks-top-level-array",
            "count": 3,
            "input": {
                "name": "[concat('myDataDisk', copyIndex('disks-top-level-array', 1))]",
                "diskSizeGB": "1",
                "diskIndex": "[copyIndex('disks-top-level-array')]"
            }
        }
    ]
},
```

Proměnná **disku pole v objektu** obsahuje následující objekt se pole s názvem **disky**:

```json
{
  "disks": [
    {
      "name": "myDataDisk1",
      "diskSizeGB": "1",
      "diskIndex": 0
    },
    {
      "name": "myDataDisk2",
      "diskSizeGB": "1",
      "diskIndex": 1
    },
    {
      "name": "myDataDisk3",
      "diskSizeGB": "1",
      "diskIndex": 2
    }
  ]
}
```

Proměnná **disky nejvyšší úroveň pole** obsahuje následující pole:

```json
[
  {
    "name": "myDataDisk1",
    "diskSizeGB": "1",
    "diskIndex": 0
  },
  {
    "name": "myDataDisk2",
    "diskSizeGB": "1",
    "diskIndex": 1
  },
  {
    "name": "myDataDisk3",
    "diskSizeGB": "1",
    "diskIndex": 2
  }
]
```

Můžete také zadat více než jeden objekt při vytváření proměnné pomocí kopírování. Následující příklad definuje dvě pole jako proměnné. Jeden s názvem **disky nejvyšší úroveň pole** a má pět prvků. Druhá s názvem **různých pole** a má tři prvky.

```json
"variables": {
    "copy": [
        {
            "name": "disks-top-level-array",
            "count": 5,
            "input": {
                "name": "[concat('oneDataDisk', copyIndex('disks-top-level-array', 1))]",
                "diskSizeGB": "1",
                "diskIndex": "[copyIndex('disks-top-level-array')]"
            }
        },
        {
            "name": "a-different-array",
            "count": 3,
            "input": {
                "name": "[concat('twoDataDisk', copyIndex('a-different-array', 1))]",
                "diskSizeGB": "1",
                "diskIndex": "[copyIndex('a-different-array')]"
            }
        }
    ]
},
```

Tento přístup funguje dobře, když se budete muset provést hodnoty parametrů a ujistěte se, že jsou ve správném formátu pro hodnoty v šabloně. Následující příklad formátuje hodnoty parametrů pro použití v definice pravidla zabezpečení:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "securityRules": {
      "type": "array"
    }
  },
  "variables": {
    "copy": [
      {
        "name": "securityRules",
        "count": "[length(parameters('securityRules'))]",
        "input": {
          "name": "[parameters('securityRules')[copyIndex('securityRules')].name]",
          "properties": {
            "description": "[parameters('securityRules')[copyIndex('securityRules')].description]",
            "priority": "[parameters('securityRules')[copyIndex('securityRules')].priority]",
            "protocol": "[parameters('securityRules')[copyIndex('securityRules')].protocol]",
            "sourcePortRange": "[parameters('securityRules')[copyIndex('securityRules')].sourcePortRange]",
            "destinationPortRange": "[parameters('securityRules')[copyIndex('securityRules')].destinationPortRange]",
            "sourceAddressPrefix": "[parameters('securityRules')[copyIndex('securityRules')].sourceAddressPrefix]",
            "destinationAddressPrefix": "[parameters('securityRules')[copyIndex('securityRules')].destinationAddressPrefix]",
            "access": "[parameters('securityRules')[copyIndex('securityRules')].access]",
            "direction": "[parameters('securityRules')[copyIndex('securityRules')].direction]"
          }
        }
      }
    ]
  },
  "resources": [
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Network/networkSecurityGroups",
      "name": "NSG1",
      "location": "[resourceGroup().location]",
      "properties": {
        "securityRules": "[variables('securityRules')]"
      }
    }
  ],
  "outputs": {}
}
```

## <a name="example-templates"></a>Příklad šablony

Tyto šablony příklad ukazují některé scénáře pro používání proměnných. Je nasadíte na testování, jak se zpracovává proměnné v různých scénářích. 

|Šablona  |Popis  |
|---------|---------|
| [Definice proměnných](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | Ukazuje různé druhy proměnných. Šablona nenasadí žádné prostředky. Vytvoří proměnné hodnoty a vrátí tyto hodnoty. |
| [Konfigurační proměnná](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | Ukazuje použití proměnné, která definuje hodnoty konfigurace. Šablona nenasadí žádné prostředky. Vytvoří proměnné hodnoty a vrátí tyto hodnoty. |
| [pravidel zabezpečení sítě](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) a [soubor s parametry](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | Vytvoří pole ve správném formátu pro přiřazení pravidla zabezpečení pro skupinu zabezpečení sítě. |


## <a name="next-steps"></a>Další postup
* Hotové šablony pro mnoho různých typů řešení najdete na stránce [Šablony Azure pro rychlý start](https://azure.microsoft.com/documentation/templates/).
* Podrobnosti o funkce, které můžete použít z v rámci šablony najdete v tématu [funkce šablon Azure Resource Manageru](resource-group-template-functions.md).
* Doporučení o vytváření šablon naleznete v tématu [osvědčené postupy pro šablony Azure Resource Manageru](template-best-practices.md).
* Budete muset použít prostředky, které existují v rámci jiné skupiny prostředků. Tento postup je běžný při práci s účty úložiště nebo virtuální sítě, které jsou sdíleny napříč více než jednu skupinu prostředků. Další informace najdete v tématu [funkce resourceId](resource-group-template-functions-resource.md#resourceid).