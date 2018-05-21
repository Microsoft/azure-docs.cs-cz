---
title: Proměnných šablony Azure Resource Manager | Microsoft Docs
description: Popisuje, jak definovat proměnné v šablonách správce prostředků Azure pomocí deklarativní syntaxe JSON.
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
ms.date: 12/12/2017
ms.author: tomfitz
ms.openlocfilehash: 08728a3c0b4d4578939004e2d1b1ee2d30a682ab
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="variables-section-of-azure-resource-manager-templates"></a>Části proměnných šablon Azure Resource Manager
V sekci proměnných můžete vytvořit hodnoty, které lze použít v celé vaší šablony. Není potřeba definovat proměnné, ale jejich často zjednodušit vaše šablony snížením složité výrazy.

## <a name="define-and-use-a-variable"></a>Definice a používání proměnné

Následující příklad ukazuje definici proměnné. Vytvoří hodnotu řetězce pro název účtu úložiště. Používá několik šablony funkcí získat hodnotu parametru a zřetězit ho do jedinečného řetězce.

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

## <a name="available-definitions"></a>K dispozici definice

Předchozí příklad ukázal jedním ze způsobů k definování proměnné. Můžete použít některou z následující definice:

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

V parametrech vytvořit hodnotu, která určuje, které konfigurační hodnoty použít.

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

## <a name="use-copy-element-in-variable-definition"></a>Pomocí elementu kopírování v definicí proměnné

Můžete použít **kopie** syntaxe vytvoření proměnné s pole více elementů. Zadejte počet pro počet elementů. Každý prvek obsahuje vlastnosti v rámci **vstupní** objektu. Můžete kopírovat buď v rámci proměnné nebo vytvořte proměnnou. Při definování proměnné a použijete **kopie** v rámci tuto proměnnou můžete vytvořit objekt, který má vlastnost pole. Při použití **kopie** na nejvyšší úrovni a definovat jeden nebo více proměnných v něm můžete vytvořit jeden nebo více polí. Následující příklad ukazuje obou přístupů:

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

Více než jeden objekt můžete zadat také v případě použití kopie k vytvoření proměnné. V následujícím příkladu definuje dvě pole jako proměnné. Jednu s názvem **disky nejvyšší úroveň pole** a má pět elementy. Druhá s názvem **jiné pole** a má tři prvky.

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

Tento postup funguje dobře, pokud potřebujete provést hodnoty parametrů a ujistěte se, že jsou ve správném formátu pro hodnotu šablony. Následující příklad formátuje hodnoty parametrů pro použití k definování pravidel zabezpečení:

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

## <a name="recommendations"></a>Doporučení
Při práci s proměnnými, může být užitečné následující informace:

* Použití proměnných hodnot, které je nutné použít více než jednou v šabloně. Pokud hodnota je použit pouze jednou, hodnotu pevně usnadňuje šablony čtení.
* Nelze použít [odkaz](resource-group-template-functions-resource.md#reference) fungovat v **proměnné** část šablony. **Odkaz** funkce odvozuje svou hodnotu z prostředku stav modulu runtime. Proměnné jsou však vyřešeny během počáteční analýzy šablony. Konstrukce hodnoty kterém musí **odkaz** funkce přímo v **prostředky** nebo **výstupy** část šablony.
* Zahrnout proměnné pro názvy prostředků, které musí být jedinečný.

## <a name="example-templates"></a>Příklad šablony

Tyto šablony příklad ukazují některé scénáře pro používání proměnných. Je k testování zpracování proměnné v různých scénářích nasazení. 

|Šablona  |Popis  |
|---------|---------|
| [definice proměnné](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | Ukazuje různé typy proměnných. Šablony není nasazen žádné prostředky. To vytvoří hodnoty proměnných a vrátí tyto hodnoty. |
| [Konfigurace proměnné](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | Demonstruje použití proměnné, která definuje hodnoty konfigurace. Šablony není nasazen žádné prostředky. To vytvoří hodnoty proměnných a vrátí tyto hodnoty. |
| [pravidla zabezpečení sítě](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) a [soubor parametrů](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | Vytvoří pole ve správném formátu pro přiřazení pravidla zabezpečení pro skupinu zabezpečení sítě. |


## <a name="next-steps"></a>Další postup
* Hotové šablony pro mnoho různých typů řešení najdete na stránce [Šablony Azure pro rychlý start](https://azure.microsoft.com/documentation/templates/).
* Podrobnosti o funkcích, které můžete použít z v rámci šablon najdete v tématu [funkce šablon Azure Resource Manager](resource-group-template-functions.md).
* Pokud chcete kombinovat několik šablon během nasazení, přečtěte si téma [použití propojených šablon s Azure Resource Manager](resource-group-linked-templates.md).
* Musíte používat prostředky, které existují v jiné skupině prostředků. Tento scénář je běžný, při práci s účty úložiště a virtuální sítě, které jsou sdíleny více skupin prostředků. Další informace najdete v tématu [resourceId funkce](resource-group-template-functions-resource.md#resourceid).