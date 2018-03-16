---
title: "Nasazení více instancí prostředků Azure | Microsoft Docs"
description: "Použití operace kopírování a polí ve šablonu Azure Resource Manager k iteraci v vícekrát při nasazení prostředků."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.assetid: 94d95810-a87b-460f-8e82-c69d462ac3ca
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2017
ms.author: tomfitz
ms.openlocfilehash: 8dfb664c7041d70f3ece812edb76df38a35e41f1
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="deploy-multiple-instances-of-a-resource-or-property-in-azure-resource-manager-templates"></a>Nasazení více instancí prostředek nebo vlastnost v šablonách Azure Resource Manager
Tento článek ukazuje, jak podmíněně nasazení prostředku a jak k iteraci v šablony Azure Resource Manager vytvořit více instancí prostředku.

## <a name="conditionally-deploy-resource"></a>Podmíněná nasazení prostředků

Pokud se rozhodnete musí během nasazení k vytvoření jedné instance nebo žádné instance prostředku, použijte `condition` elementu. Hodnota pro tento element překládá true nebo false. Pokud je hodnota true, prostředek je nasazena. Pokud je hodnota false, prostředek není nasazen. Například k určení, zda nový účet úložiště je nasazena nebo existující účet úložiště se používá, použijte tento příkaz:

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

## <a name="resource-iteration"></a>Iterace prostředků
Pokud se rozhodnete musí během nasazení vytvořit jeden nebo více instancí prostředku, přidejte `copy` element pro typ prostředku. V elementu kopírování zadejte počet opakování a název pro tento smyčky. Hodnota počtu musí být kladné celé číslo a nesmí být delší než 800. 

Prostředek pro vytvoření vícekrát má následující formát:

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

Všimněte si, že obsahuje název každého prostředku `copyIndex()` funkci, která vrátí na aktuální iteraci smyčky. `copyIndex()` je počítáno od nuly. To, v následujícím příkladu:

```json
"name": "[concat('storage', copyIndex())]",
```

Vytvoří tyto názvy:

* storage0
* storage1
* storage2.

K posunutí hodnotu indexu, může předat hodnotu ve funkci copyIndex(). V elementu kopie je stále zadat počet opakování provést, ale zadaná hodnota je posunut hodnotu copyIndex. To, v následujícím příkladu:

```json
"name": "[concat('storage', copyIndex(1))]",
```

Vytvoří tyto názvy:

* storage1
* storage2
* storage3

Operace kopírování je užitečné při práci s poli, protože můžete iterovat každý prvek v poli. Použití `length` funkce v poli zadat počet opakování, a `copyIndex` načíst aktuální index v poli. To, v následujícím příkladu:

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

Ve výchozím nastavení vytvoří Resource Manager prostředky paralelně. Proto není zaručena pořadí, ve které byly vytvořeny. Můžete však zadat, aby byly prostředky nasazené v pořadí. Například při aktualizaci provozním prostředí, můžete tak rozložte aktualizací jenom několik jsou aktualizovány v daném okamžiku.

Sériově nasadit více instancí prostředku, nastavte `mode` k **sériové** a `batchSize` na počet instancí pro nasazení v čase. Sériového portu v režimu Resource Manager vytvoří závislost na dřívější instancí ve smyčce, tak nespustí jeden batch, dokud se nedokončí předchozí dávka.

Například sériově nasadit účty úložiště, dva v čase, použijte:

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

Vlastnost režimu také přijímá **paralelní**, což je výchozí hodnota.

## <a name="property-iteration"></a>Vlastnost iterace

Chcete-li vytvořit více hodnot pro vlastnost prostředku, přidejte `copy` pole v prvku vlastnosti. Toto pole obsahuje objekty a každý objekt má následující vlastnosti:

* název – název vlastnosti pro vytvoření více hodnot pro
* počet - počet hodnot k vytvoření
* (vstup) – objekt, který obsahuje hodnoty, které mají přiřadit k vlastnosti  

Následující příklad ukazuje, jak se má použít `copy` pro dataDisks vlastnost na virtuálním počítači:

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

Všimněte si, že při použití `copyIndex` uvnitř iterace vlastnost, je nutné zadat název iterace. Nemáte k zadání názvu při použití s iterace prostředků.

Správce prostředků rozšíří `copy` pole během nasazení. Název pole bude název vlastnosti. Vstupní hodnoty stát vlastnosti objektu. Nasazené šablony se změní na:

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

Iterace prostředků a vlastnosti můžete použít společně. Odkaz na vlastnost iterace podle názvu.

```json
{
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[concat(parameters('vnetname'), copyIndex())]",
    "apiVersion": "2016-06-01",
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

Chcete-li vytvořit více instancí proměnné, použijte `copy` element v sekci proměnných. Můžete vytvořit více instancí objektů s související hodnotami a potom přiřadit tyto hodnoty k instancím typu prostředku. Kopírování vám pomůže vytvořit objekt s ve vlastnosti pole nebo pole. Následující příklad ukazuje obou přístupů:

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

## <a name="depend-on-resources-in-a-loop"></a>Závisí na prostředky ve smyčce
Určíte, že je prostředek nasazeno po jiný prostředek pomocí `dependsOn` elementu. K nasazení na prostředek, který závisí na kolekci prostředků ve smyčce, zadejte název kopírovací smyčkou v elementu dependsOn. Následující příklad ukazuje, jak nasadit tři účty úložiště před nasazením virtuálního počítače. Úplná definice virtuálního počítače se nezobrazí. Všimněte si, že element kopie má název nastaven `storagecopy` a element dependsOn pro virtuální počítače je také nastavena na `storagecopy`.

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
Kopírovací smyčkou nelze použít pro podřízený prostředek. Pokud chcete vytvořit několik instancí na prostředek, který je obvykle definovat jako vnořené v rámci jiný prostředek, musíte místo toho vytvořit prostředku jako prostředek nejvyšší úrovně. Můžete definovat relaci s nadřazený prostředek prostřednictvím typ a název vlastnosti.

Předpokládejme například, že definujete obvykle datovou sadu jako podřízený prostředek v rámci služby data factory.

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

Pokud chcete vytvořit více instancí datových sad, přesuňte jej mimo služby data factory. Datová sada musí být na stejné úrovni jako objekt pro vytváření dat, ale je stále prostředek podřízeného objektu pro vytváření dat. Můžete zachovat vztah mezi datovou sadu a objektu pro vytváření dat pomocí vlastnosti typu a název. Vzhledem k tomu, že už se nedá odvodit typ od pozice v šabloně, je nutné zadat plně kvalifikovaný typ ve formátu: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`.

K vytvoření vztahu nadřazený podřízený s instancí objektu pro vytváření dat, zadejte název pro datovou sadou, která obsahuje název nadřazené prostředků. Použijte formát: `{parent-resource-name}/{child-resource-name}`.  

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

Následující příklady ukazují běžné scénáře pro vytvoření více prostředků nebo vlastnosti.

|Šablona  |Popis  |
|---------|---------|
|[Zkopírujte úložiště](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystorage.json) |Nasadí více účtů úložiště s číslem indexu v názvu. |
|[Úložiště sériové kopie](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/serialcopystorage.json) |Nasadí více účtů úložiště jeden v čase. Název obsahuje číslo indexu. |
|[Zkopírujte úložiště s polem.](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystoragewitharray.json) |Nasadí více účtů úložiště. Název obsahuje hodnotu z pole. |
|[Virtuální počítač s nový nebo existující virtuální sítě, úložiště a veřejné IP adresy](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions) |Podmíněná nasadí nový nebo existující prostředky s virtuálním počítačem. |
|[Nasazení virtuálního počítače s proměnný počet datových disků](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Nasadí více datových disků s virtuálním počítačem. |
|[Zkopírujte proměnné](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) |Ukazuje různé způsoby iterace v proměnné. |
|[Víc pravidel zabezpečení](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |Nasadí víc pravidel zabezpečení do skupiny zabezpečení sítě. Vytvoří z parametr pravidla zabezpečení. |

## <a name="next-steps"></a>Další postup
* Pokud chcete další informace o části šablony, najdete v části [vytváření šablon Azure Resource Manager](resource-group-authoring-templates.md).
* Informace o nasazení šablony najdete v tématu [nasazení aplikace pomocí šablony Azure Resource Manageru](resource-group-template-deploy.md).

