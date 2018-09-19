---
title: Nasazení více instancí prostředku Azure | Dokumentace Microsoftu
description: Použití operace kopírování a polí v šabloně Azure Resource Manageru k iteraci více než jednou při nasazování prostředků.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
editor: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/10/2018
ms.author: tomfitz
ms.openlocfilehash: 8828ba3c91df7b0a2fde3c42ecd81bd4ee4d17a3
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46295933"
---
# <a name="deploy-multiple-instances-of-a-resource-or-property-in-azure-resource-manager-templates"></a>Nasazení více instancí prostředku nebo vlastnosti v šablonách Azure Resource Manageru

Tento článek popisuje, jak k iteraci v šabloně Azure Resource Manageru k vytvoření více instancí prostředku. Pokud je potřeba určit, jestli je prostředek nasazený vůbec, přečtěte si téma [podmínky](resource-manager-templates-resources.md#condition).

Podívejte se kurz [kurz: vytvoření více instancí prostředků pomocí šablon Resource Manageru](./resource-manager-tutorial-create-multiple-instances.md).

## <a name="resource-iteration"></a>Iterace prostředků

Pokud během nasazení se musíte rozhodnout vytvořit jednu nebo více instancí prostředku, přidejte `copy` element na typ prostředku. V elementu copy určíte počet iterací a název pro tuto smyčku. Hodnota count musí být kladné celé číslo a nemůže být delší než 800. 

Prostředek, který chcete vytvořit více než jednou má následující formát:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "apiVersion": "2016-01-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": 3
            }
        }
    ],
    "outputs": {}
}
```

Všimněte si, že název každého prostředku obsahuje `copyIndex()` funkce, která vrací aktuální iteraci ve smyčce. `copyIndex()` je založený na nule. To, v následujícím příkladu:

```json
"name": "[concat('storage', copyIndex())]",
```

Vytvoří tyto názvy:

* storage0
* storage1
* storage2.

Odsazení hodnotu indexu, můžete hodnotu předat ve funkci copyIndex(). Počet iterací provádět je stále zadaný v elementu copy, ale hodnota copyIndex je posunut o zadanou hodnotu. To, v následujícím příkladu:

```json
"name": "[concat('storage', copyIndex(1))]",
```

Vytvoří tyto názvy:

* storage1
* storage2
* storage3

Operace kopírování je užitečné při práci s poli, protože můžete iterovat každý prvek v poli. Použití `length` funkce na pole, které chcete určit počet iterací, a `copyIndex` načíst aktuální index v poli. To, v následujícím příkladu:

```json
"parameters": { 
  "org": { 
     "type": "array", 
     "defaultValue": [ 
         "contoso", 
         "fabrikam", 
         "coho" 
      ] 
  }
}, 
"resources": [ 
  { 
      "name": "[concat('storage', parameters('org')[copyIndex()])]", 
      "copy": { 
         "name": "storagecopy", 
         "count": "[length(parameters('org'))]" 
      }, 
      ...
  } 
]
```

Vytvoří tyto názvy:

* storagecontoso
* storagefabrikam
* storagecoho

Ve výchozím nastavení správce prostředků vytvoří prostředky paralelně. Proto není zaručeno pořadí, ve které jste vytvořili. Můžete však určit, že se prostředky nasadí v sekvenci. Například při aktualizaci produkčním prostředí, můžete chtít rozvrhnout aktualizace tak jenom určitý počet jsou aktualizovány v daný okamžik.

Chcete-li sériově nasazení více instancí prostředku, nastavte `mode` k **sériového portu** a `batchSize` na počet instancí k nasazení v čase. Sériového portu v režimu Resource Manageru vytvoří závislost na předchozích instancí ve smyčce, tak se nespustí jednu dávku, dokud se nedokončí předchozí dávky.

Například účty úložiště, dva sériově nasazení najednou, použijte:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "apiVersion": "2016-01-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": 4,
                "mode": "serial",
                "batchSize": 2
            }
        }
    ],
    "outputs": {}
}
``` 

Vlastnost mode přijímá také **paralelní**, což je výchozí hodnota.

## <a name="property-iteration"></a>Vlastnost iterace

Chcete-li vytvořit více hodnot pro vlastnost na prostředek, přidejte `copy` pole v elementu properties. Toto pole obsahuje objekty, a každý objekt má následující vlastnosti:

* název – název vlastnosti pro vytvoření více hodnot pro
* počet – počet hodnot k vytvoření
* (vstup) – objekt, který obsahuje hodnoty pro přiřazení k vlastnosti  

Následující příklad ukazuje, jak použít `copy` dataDisks vlastnost na virtuálním počítači:

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2017-03-30",
  "properties": {
    "storageProfile": {
      "copy": [{
          "name": "dataDisks",
          "count": 3,
          "input": {
              "lun": "[copyIndex('dataDisks')]",
              "createOption": "Empty",
              "diskSizeGB": "1023"
          }
      }],
      ...
```

Všimněte si, že při použití `copyIndex` uvnitř vlastnosti iterace, je nutné zadat název iterace. Není nutné zadat název, při použití s prostředků iterace.

Resource Manager rozšiřuje `copy` pole během nasazení. Název pole bude název vlastnosti. Vstupní hodnoty stane vlastností objektu. Stane se nasadila Šablona:

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
              "diskSizeGB": "1023"
          },
          {
              "lun": 1,
              "createOption": "Empty",
              "diskSizeGB": "1023"
          },
          {
              "lun": 2,
              "createOption": "Empty",
              "diskSizeGB": "1023"
          }
      }],
      ...
```

V elementu copy je pole, takže můžete zadat více než jednu vlastnost pro prostředek. Přidání objektu pro každou vlastnost k vytvoření.

```json
{
    "name": "string",
    "type": "Microsoft.Network/loadBalancers",
    "apiVersion": "2017-10-01",
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

Zdroj a vlastnost iterace můžete použít společně. Odkaz na vlastnost iterace podle názvu.

```json
{
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[concat(parameters('vnetname'), copyIndex())]",
    "apiVersion": "2018-04-01",
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

## <a name="variable-iteration"></a>Proměnné iterace

Chcete-li vytvořit více instancí proměnné, použijte `copy` element v sekci proměnných. Můžete vytvořit více instancí objektů s související hodnoty a pak přiřadit tyto hodnoty instance prostředku. Kopírování můžete vytvořit objekt s vlastnost typu pole nebo pole. Oba přístupy jsou uvedeny v následujícím příkladu:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {
    "disk-array-on-object": {
      "copy": [
        {
          "name": "disks",
          "count": 5,
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
        "count": 5,
        "input": {
          "name": "[concat('myDataDisk', copyIndex('disks-top-level-array', 1))]",
          "diskSizeGB": "1",
          "diskIndex": "[copyIndex('disks-top-level-array')]"
        }
      }
    ]
  },
  "resources": [],
  "outputs": {
    "exampleObject": {
      "value": "[variables('disk-array-on-object')]",
      "type": "object"
    },
    "exampleArrayOnObject": {
      "value": "[variables('disk-array-on-object').disks]",
      "type" : "array"
    },
    "exampleArray": {
      "value": "[variables('disks-top-level-array')]",
      "type" : "array"
    }
  }
}
```

Kterýkoliv přístup v elementu copy se pole umožňující vám zadat více než jednu proměnnou. Přidání objektu pro každou proměnnou vytvořit.

```json
"copy": [
  {
    "name": "first-variable",
    "count": 5,
    "input": {
      "demoProperty": "[concat('myProperty', copyIndex('first-variable'))]",
    }
  },
  {
    "name": "second-variable",
    "count": 3,
    "input": {
      "demoProperty": "[concat('myProperty', copyIndex('second-variable'))]",
    }
  },
]
```

## <a name="depend-on-resources-in-a-loop"></a>Závisí na prostředky ve smyčce
Určíte, že je prostředek nasazený pomocí po jiný prostředek `dependsOn` elementu. K nasazení na prostředek, který závisí na kolekci prostředků ve smyčce, zadejte název kopií smyčky v elementu dependsOn. Následující příklad ukazuje, jak nasadit 3 účty úložiště před nasazením virtuálního počítače. Kompletní definici virtuálního počítače se nezobrazí. Všimněte si, že v elementu copy má název nastavený `storagecopy` a elementu dependsOn pro virtuální počítače je také nastavena na `storagecopy`.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "resources": [
        {
            "apiVersion": "2016-01-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": 3
            }
        },
        {
            "apiVersion": "2015-06-15", 
            "type": "Microsoft.Compute/virtualMachines", 
            "name": "[concat('VM', uniqueString(resourceGroup().id))]",  
            "dependsOn": ["storagecopy"],
            ...
        }
    ],
    "outputs": {}
}
```

<a id="looping-on-a-nested-resource" />

## <a name="iteration-for-a-child-resource"></a>Iterace pro podřízený prostředek
Kopírovací smyčkou nelze použít pro podřízený prostředek. Vytvoření více instancí prostředku, které obvykle definují jako vnořený v rámci jiného prostředku, musíte místo toho vytvořit tento prostředek jako prostředku nejvyšší úrovně. Můžete definovat relaci se nadřazený prostředek prostřednictvím typ a název vlastnosti.

Předpokládejme například, že obvykle definujete datovou sadu jako podřízený prostředek v rámci služby data factory.

```json
"resources": [
{
    "type": "Microsoft.DataFactory/datafactories",
    "name": "exampleDataFactory",
    ...
    "resources": [
    {
        "type": "datasets",
        "name": "exampleDataSet",
        "dependsOn": [
            "exampleDataFactory"
        ],
        ...
    }
}]
```

Pro vytvoření víc instancí datových sad, přesuňte ho mimo datovou továrnou. Datová sada musí být na stejné úrovni jako objekt pro vytváření dat, ale je stále podřízený prostředek služby data factory. Zachováte vztah mezi datovou sadu a data factory prostřednictvím typ a název vlastnosti. Vzhledem k tomu, že již nelze odvodit z jeho pozice v šabloně, je nutné zadat plně kvalifikovaný typ ve formátu: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`.

K navázání vztahu nadřazené a podřízené s instancí služby data factory, zadejte název pro datovou sadu, která obsahuje název nadřazené prostředku. Použijte formát: `{parent-resource-name}/{child-resource-name}`.  

Následující příklad ukazuje implementaci:

```json
"resources": [
{
    "type": "Microsoft.DataFactory/datafactories",
    "name": "exampleDataFactory",
    ...
},
{
    "type": "Microsoft.DataFactory/datafactories/datasets",
    "name": "[concat('exampleDataFactory', '/', 'exampleDataSet', copyIndex())]",
    "dependsOn": [
        "exampleDataFactory"
    ],
    "copy": { 
        "name": "datasetcopy", 
        "count": "3" 
    } 
    ...
}]
```

## <a name="example-templates"></a>Příklad šablony

Následující příklady ukazují běžné scénáře týkající se vytvoření víc prostředků nebo vlastnosti.

|Šablona  |Popis  |
|---------|---------|
|[Zkopírujte úložiště](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystorage.json) |Nasadí více účtů úložiště s číslem indexu v názvu. |
|[Úložiště kopie sériového portu](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/serialcopystorage.json) |Nasadí více účtů úložiště jeden v době. Název obsahuje číslo indexu. |
|[Kopírování s polem úložiště](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystoragewitharray.json) |Nasadí více účtů úložiště. Název obsahuje hodnotu z pole. |
|[Nasazení virtuálního počítače s variabilním počtem datových disků](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Nasadí více datových disků virtuálního počítače. |
|[Zkopírujte proměnné](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) |Ukazuje různé způsoby pro proměnné iterace. |
|[Víc pravidel zabezpečení](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |Nasadí víc pravidel zabezpečení do skupiny zabezpečení sítě. Vytvoří pravidla zabezpečení z parametru. Pro parametr, naleznete v tématu [více NSG v souboru parametrů](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json). |

## <a name="next-steps"></a>Další postup

* Absolvovat kurz, naleznete v tématu [kurz: vytvoření více instancí prostředků pomocí šablon Resource Manageru](./resource-manager-tutorial-create-multiple-instances.md).

* Pokud chcete další informace o části šablony, přečtěte si téma [vytváření šablon Azure Resource Manageru](resource-group-authoring-templates.md).
* Informace o nasazení šablony najdete v tématu [nasazení aplikace pomocí šablony Azure Resource Manageru](resource-group-template-deploy.md).

