---
title: Nasazení více instancí prostředků
description: Použijte operaci kopírování a pole v šabloně Azure Resource Manager k iterování několikrát při nasazování prostředků.
ms.topic: conceptual
ms.date: 09/27/2019
ms.openlocfilehash: 54d406771f64d97a3ba564556be6dc49677a732d
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2020
ms.locfileid: "76121977"
---
# <a name="resource-property-or-variable-iteration-in-azure-resource-manager-templates"></a>Iterace prostředku, vlastnosti nebo proměnné v šablonách Azure Resource Manager

V tomto článku se dozvíte, jak vytvořit v šabloně Azure Resource Manager více než jednu instanci prostředku, proměnné nebo vlastnosti. Chcete-li vytvořit více instancí, přidejte objekt `copy` do šablony.

Při použití s prostředkem má objekt kopírování následující formát:

```json
"copy": {
  "name": "<name-of-loop>",
  "count": <number-of-iterations>,
  "mode": "serial" <or> "parallel",
  "batchSize": <number-to-deploy-serially>
}
```

Při použití s proměnnou nebo vlastností má objekt kopírování následující formát:

```json
"copy": [
  {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "input": <values-for-the-property-or-variable>
  }
]
```

Obě použití jsou podrobněji popsány v tomto článku. Kurz najdete v tématu [kurz: vytvoření více instancí prostředků pomocí šablon Správce prostředků](template-tutorial-create-multiple-instances.md).

Pokud potřebujete určit, jestli je prostředek nasazený vůbec, viz [Podmínka elementu](conditional-resource-deployment.md).

## <a name="copy-limits"></a>Omezení kopírování

Chcete-li zadat počet iterací, zadejte hodnotu pro vlastnost Count. Počet nemůže být větší než 800.

Počet nemůže být záporné číslo. Pokud nasadíte šablonu s Azure PowerShell 2,6 nebo novějším, Azure CLI 2.0.74 nebo novějším nebo REST API verze **2019-05-10** nebo novější, můžete nastavit počet na nula. Starší verze prostředí PowerShell, rozhraní příkazového řádku a REST API pro počet nepodporují nulu.

Pomocí [úplného nasazení režimu](deployment-modes.md) s kopírováním buďte opatrní. Pokud znovu nasadíte v režimu úplného nasazení do skupiny prostředků, všechny prostředky, které nejsou zadané v šabloně po vyřešení smyčky kopírování, se odstraní.

Omezení pro počet jsou stejná, ať už se používá u prostředku, proměnné nebo vlastnosti.

## <a name="resource-iteration"></a>Iterace prostředku

Pokud chcete vytvořit více než jednu instanci prostředku v nasazení, přidejte `copy` element do typu prostředku. V elementu Copy zadejte počet iterací a název pro tuto smyčku.

Prostředek, který se má vytvořit několikrát, má následující formát:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-01-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "copy": {
        "name": "storagecopy",
        "count": 3
      },
      "properties": {}
    }
  ],
  "outputs": {}
}
```

Všimněte si, že název každého prostředku obsahuje funkci `copyIndex()`, která vrací aktuální iteraci ve smyčce. `copyIndex()` je založen na nule. V následujícím příkladu:

```json
"name": "[concat('storage', copyIndex())]",
```

Vytvoří tyto názvy:

* storage0
* storage1
* storage2.

Abyste odsadili hodnotu indexu, můžete hodnotu předat do funkce copyIndex(). Počet iterací je stále specifikován v elementu Copy, ale hodnota copyIndex je posunuta podle zadané hodnoty. V následujícím příkladu:

```json
"name": "[concat('storage', copyIndex(1))]",
```

Vytvoří tyto názvy:

* storage1
* storage2
* storage3

Operace kopírování je užitečná při práci s poli, protože můžete iterovat přes každý prvek v poli. Použijte funkci `length` v poli k určení počtu iterací a `copyIndex` k načtení aktuálního indexu v poli. V následujícím příkladu:

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

Ve výchozím nastavení Správce prostředků vytvoří paralelní prostředky. Neplatí pro počet paralelně nasazených prostředků, s výjimkou celkového limitu 800 prostředků v šabloně. Pořadí, ve kterém jsou vytvořeny, není zaručeno.

Můžete ale chtít určit, že se prostředky nasazují v pořadí. Například při aktualizaci produkčního prostředí můžete aktualizace rozložit, aby se v jednom okamžiku aktualizovalo jenom určité číslo. Pro sériové nasazení více než jedné instance prostředku nastavte `mode` na **sériové** a `batchSize` na počet instancí, které se nasadí v jednom okamžiku. V případě sériového režimu Správce prostředků ve smyčce vytvoří závislost na dřívějších instancích, takže nespustí jednu dávku, dokud se předchozí dávka nedokončí.

Pokud například chcete sériové nasazení účtů úložiště vytvořit dvakrát, použijte:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-01-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "copy": {
        "name": "storagecopy",
        "count": 4,
        "mode": "serial",
        "batchSize": 2
      },
      "properties": {}
    }
  ],
  "outputs": {}
}
```

Vlastnost Mode také akceptuje **paralelní**, což je výchozí hodnota.

Informace o použití kopírování s vnořenými šablonami naleznete v tématu [using Copy](linked-templates.md#using-copy).

## <a name="property-iteration"></a>Iterace vlastnosti

Chcete-li vytvořit více než jednu hodnotu pro vlastnost prostředku, přidejte pole `copy` v elementu Properties. Toto pole obsahuje objekty a každý objekt má následující vlastnosti:

* Název – název vlastnosti, pro kterou chcete vytvořit několik hodnot.
* Count – počet hodnot, které se mají vytvořit.
* Input – objekt, který obsahuje hodnoty, které chcete přiřadit vlastnosti.

Následující příklad ukazuje, jak použít `copy` na vlastnost datadisks na virtuálním počítači:

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2017-03-30",
  "name": "examplevm",
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

Všimněte si, že při použití `copyIndex` v rámci iterace vlastnosti je nutné zadat název iterace. Při použití s iterací prostředků není nutné zadávat název.

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
      ],
      ...
```

Element Copy je pole, abyste mohli zadat více než jednu vlastnost prostředku. Přidejte objekt pro každou vlastnost, která se má vytvořit.

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

## <a name="variable-iteration"></a>Iterace proměnné

Chcete-li vytvořit více instancí proměnné, použijte vlastnost `copy` v oddílu Variables. Vytvoříte pole prvků vytvořené z hodnoty vlastnosti `input`. Můžete použít vlastnost `copy` v rámci proměnné nebo na nejvyšší úrovni oddílu Variables. Při použití `copyIndex` uvnitř proměnné iterace je nutné zadat název iterace.

Jednoduchý příklad vytvoření pole řetězcových hodnot naleznete v tématu [copy Array Template](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/copy-array/azuredeploy.json).

Následující příklad ukazuje několik různých způsobů, jak vytvořit proměnné pole s dynamicky vytvořenými prvky. Ukazuje, jak použít kopírování uvnitř proměnné pro vytvoření polí objektů a řetězců. Ukazuje také, jak použít kopírování na nejvyšší úrovni k vytvoření polí objektů, řetězců a celých čísel.

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
        },
        {
          "name": "diskNames",
          "count": 5,
          "input": "[concat('myDataDisk', copyIndex('diskNames', 1))]"
        }
      ]
    },
    "copy": [
      {
        "name": "top-level-object-array",
        "count": 5,
        "input": {
          "name": "[concat('myDataDisk', copyIndex('top-level-object-array', 1))]",
          "diskSizeGB": "1",
          "diskIndex": "[copyIndex('top-level-object-array')]"
        }
      },
      {
        "name": "top-level-string-array",
        "count": 5,
        "input": "[concat('myDataDisk', copyIndex('top-level-string-array', 1))]"
      },
      {
        "name": "top-level-integer-array",
        "count": 5,
        "input": "[copyIndex('top-level-integer-array')]"
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
    "exampleObjectArray": {
      "value": "[variables('top-level-object-array')]",
      "type" : "array"
    },
    "exampleStringArray": {
      "value": "[variables('top-level-string-array')]",
      "type" : "array"
    },
    "exampleIntegerArray": {
      "value": "[variables('top-level-integer-array')]",
      "type" : "array"
    }
  }
}
```

Typ proměnné, která se vytvoří, závisí na vstupním objektu. Například proměnná s názvem **nejvyšší úrovně objektu-Array** v předchozím příkladu vrátí:

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
  },
  {
    "name": "myDataDisk4",
    "diskSizeGB": "1",
    "diskIndex": 3
  },
  {
    "name": "myDataDisk5",
    "diskSizeGB": "1",
    "diskIndex": 4
  }
]
```

A proměnná s názvem **Top-level-String-Array** vrátí:

```json
[
  "myDataDisk1",
  "myDataDisk2",
  "myDataDisk3",
  "myDataDisk4",
  "myDataDisk5"
]
```

## <a name="depend-on-resources-in-a-loop"></a>Závislá na prostředcích ve smyčce

Určíte, že prostředek bude nasazen po jiném prostředku pomocí elementu `dependsOn`. Chcete-li nasadit prostředek, který závisí na kolekci prostředků ve smyčce, zadejte název smyčky kopírování v elementu dependsOn. Následující příklad ukazuje, jak nasadit tři účty úložiště před nasazením virtuálního počítače. Nezobrazuje se úplná definice virtuálního počítače. Všimněte si, že element Copy má název nastavený na `storagecopy` a element dependsOn pro Virtual Machines je také nastaven na `storagecopy`.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-01-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "copy": {
        "name": "storagecopy",
        "count": 3
      },
      "properties": {}
    },
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2015-06-15",
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
U podřízeného prostředku nemůžete použít kopírovací smyčku. Chcete-li vytvořit více než jednu instanci prostředku, který obvykle definujete jako vnořený v rámci jiného prostředku, je nutné místo toho vytvořit tento prostředek jako prostředek nejvyšší úrovně. Pomocí vlastností typ a název můžete definovat relaci s nadřazeným prostředkem.

Předpokládejme například, že obvykle definujete datovou sadu jako podřízený prostředek v rámci objektu pro vytváření dat.

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
  ]
```

Pokud chcete vytvořit více než jednu datovou sadu, přesuňte ji mimo datovou továrnu. Datová sada musí být na stejné úrovni jako objekt pro vytváření dat, ale je stále podřízeným prostředkem objektu pro vytváření dat. Můžete zachovat vztah mezi datovou sadou a datovou továrnou prostřednictvím vlastností typu a názvu. Vzhledem k tomu, že typ již nelze odvodit z jeho pozice v šabloně, je nutné zadat plně kvalifikovaný typ ve formátu: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`.

Pokud chcete vytvořit relaci nadřazený-podřízený s instancí datové továrny, zadejte název datové sady, která zahrnuje název nadřazeného prostředku. Použijte formát: `{parent-resource-name}/{child-resource-name}`.

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
  },
  ...
}]
```

## <a name="example-templates"></a>Příklad šablony

Následující příklady znázorňují běžné scénáře pro vytvoření více než jedné instance prostředku nebo vlastnosti.

|Šablona  |Popis  |
|---------|---------|
|[Kopírovat úložiště](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystorage.json) |Nasadí více než jeden účet úložiště s číslem indexu v názvu. |
|[Úložiště sériového kopírování](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/serialcopystorage.json) |Nasadí několik účtů úložiště v jednom okamžiku. Název zahrnuje číslo indexu. |
|[Kopírování úložiště pomocí pole](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystoragewitharray.json) |Nasadí několik účtů úložiště. Název obsahuje hodnotu z pole. |
|[Nasazení virtuálního počítače s proměnným počtem datových disků](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Nasadí několik datových disků s virtuálním počítačem. |
|[Zkopírujte proměnné](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) |Ukazuje různé způsoby, jak iterace proměnných vymezit. |
|[Více pravidel zabezpečení](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |Nasadí několik pravidel zabezpečení do skupiny zabezpečení sítě. Vytvoří pravidla zabezpečení z parametru. Pro parametr viz [více souborů parametrů NSG](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json). |

## <a name="next-steps"></a>Další kroky

* Kurz najdete v tématu [kurz: vytvoření více instancí prostředků pomocí šablon Správce prostředků](template-tutorial-create-multiple-instances.md).

* Pokud se chcete dozvědět o oddílech šablony, přečtěte si téma [vytváření Azure Resource Manager šablon](template-syntax.md).
* Informace o tom, jak šablonu nasadit, najdete v tématu [nasazení aplikace pomocí šablony Azure Resource Manager](deploy-powershell.md).

