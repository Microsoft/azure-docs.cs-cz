---
title: Nasazení více instancí prostředků
description: Pomocí operace kopírování a polí v šabloně Azure Resource Manager nasazujte typ prostředků mnohokrát.
ms.topic: conceptual
ms.date: 09/27/2019
ms.openlocfilehash: e65ab93c21daffa0053e53d953fe95fa9f28e2a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153314"
---
# <a name="resource-iteration-in-arm-templates"></a>Iterace prostředků v šablonách ARM

Tento článek ukazuje, jak vytvořit více než jednu instanci prostředku v šabloně Správce prostředků Azure (ARM). Přidáním elementu **copy** do části prostředků šablony můžete dynamicky nastavit počet prostředků, které chcete nasadit. Také se vyhnete nutnosti opakovat syntaxi šablony.

Můžete také použít kopii s [vlastnostmi](copy-properties.md), [proměnnými](copy-variables.md) a [výstupy](copy-outputs.md).

Pokud potřebujete určit, zda je prostředek nasazen vůbec, viz [prvek podmínky](conditional-resource-deployment.md).

## <a name="resource-iteration"></a>Iterace prostředků

Prvek kopírování má následující obecný formát:

```json
"copy": {
  "name": "<name-of-loop>",
  "count": <number-of-iterations>,
  "mode": "serial" <or> "parallel",
  "batchSize": <number-to-deploy-serially>
}
```

Vlastnost **name** je libovolná hodnota, která identifikuje smyčku. Vlastnost **count** určuje počet iterací, které chcete pro typ prostředku.

Pomocí vlastností **mode** a **batchSize** určete, zda jsou prostředky nasazeny paralelně nebo postupně. Tyto vlastnosti jsou popsány v [sériové nebo paralelní](#serial-or-parallel).

Následující příklad vytvoří počet účtů úložiště zadaný v parametru **storageCount.**

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
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
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
    "outputs": {}
}
```

Všimněte si, že název `copyIndex()` každého prostředku obsahuje funkci, která vrací aktuální iteraci ve smyčce. `copyIndex()` je založen na nule. Takže následující příklad:

```json
"name": "[concat('storage', copyIndex())]",
```

Vytvoří tyto názvy:

* skladování0
* skladování1
* skladování2.

Abyste odsadili hodnotu indexu, můžete hodnotu předat do funkce copyIndex(). Počet iterací je stále zadán v elementu copy, ale hodnota copyIndex je posunuta zadanou hodnotou. Takže následující příklad:

```json
"name": "[concat('storage', copyIndex(1))]",
```

Vytvoří tyto názvy:

* skladování1
* skladování2
* skladování3

Operace kopírování je užitečná při práci s maticemi, protože můžete iterate prostřednictvím každého prvku v poli. Pomocí `length` funkce v poli určete počet iterací a `copyIndex` načtěte aktuální index v poli.

Následující příklad vytvoří jeden účet úložiště pro každý název uvedený v parametru.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "storageNames": {
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
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(parameters('storageNames')[copyIndex()], uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": "[length(parameters('storageNames'))]"
      }
    }
  ],
  "outputs": {}
}
```

Pokud chcete vrátit hodnoty z nasazených prostředků, můžete použít [kopii v části výstupy](copy-outputs.md).

## <a name="serial-or-parallel"></a>Sériové nebo paralelní

Ve výchozím nastavení správce prostředků vytváří prostředky paralelně. Neuplatňuje žádné omezení na počet prostředků nasazených paralelně, kromě celkového limitu 800 prostředků v šabloně. Pořadí, ve kterém jsou vytvořeny není zaručena.

Můžete však určit, že prostředky jsou nasazeny v pořadí. Například při aktualizaci produkčního prostředí můžete chtít rozložit aktualizace tak, aby byly aktualizovány pouze určité číslo v jednom okamžiku. Chcete-li sériově nasadit více než `mode` jednu `batchSize` instanci prostředku, nastavte na **sériové** a počet instancí nasadit najednou. V sériovém režimu správce prostředků vytvoří závislost na dřívějších instancích ve smyčce, takže nespustí jednu dávku, dokud se nedokončí předchozí dávka.

Chcete-li například sériově nasadit účty úložiště po dvou, použijte:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
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

Vlastnost mode také přijímá **paralelní**, což je výchozí hodnota.

## <a name="depend-on-resources-in-a-loop"></a>Závisí na zdrojích ve smyčce

Zadáte, že prostředek je nasazen za jiným `dependsOn` prostředkem pomocí prvku. Chcete-li nasadit prostředek, který závisí na kolekci prostředků ve smyčce, zadejte název smyčky kopírování v elementu dependsOn. Následující příklad ukazuje, jak nasadit tři účty úložiště před nasazením virtuálního počítače. Úplné definice virtuálního počítače se nezobrazí. Všimněte si, že prvek `storagecopy` kopírování má název nastaven a element `storagecopy`dependsOn pro virtuální počítač je také nastaven na .

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
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

## <a name="iteration-for-a-child-resource"></a>Iterace pro podřízený prostředek

Nelze použít kopírovat smyčky pro podřízený prostředek. Chcete-li vytvořit více než jednu instanci prostředku, který obvykle definujete jako vnořený v rámci jiného prostředku, musíte místo toho vytvořit tento prostředek jako prostředek nejvyšší úrovně. Vztah s nadřazeným zdrojem definujete pomocí vlastností typu a názvu.

Předpokládejme například, že obvykle definujete datovou sadu jako podřízený prostředek v rámci datové továrny.

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

Chcete-li vytvořit více než jednu sadu dat, přesuňte ji mimo továrnu na data. Datová sada musí být na stejné úrovni jako data factory, ale je to stále podřízený prostředek datové továrny. Vztah mezi sadou dat a továrnou na data zachováte prostřednictvím vlastností typu a názvu. Vzhledem k tomu, že typ již nelze odvodit z jeho pozice `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`v šabloně, je nutné zadat plně kvalifikovaný typ ve formátu: .

Chcete-li vytvořit vztah nadřazený/podřízený s instancí datové továrny, zadejte název pro sadu dat, která obsahuje název nadřazeného prostředku. Použijte formát: `{parent-resource-name}/{child-resource-name}`.

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

## <a name="copy-limits"></a>Kopírovat limity

Počet nesmí překročit 800.

Počet nemůže být záporné číslo. Pokud nasadíte šablonu s Azure PowerShell 2.6 nebo novější, Azure CLI 2.0.74 nebo novější, nebo REST API verze **2019-05-10** nebo novější, můžete nastavit počet na nulu. Dřívější verze PowerShellu, ROZHRANÍ CLI a rozhraní REST API nepodporují nulu pro počet.

Buďte opatrní při [použití úplného nasazení režimu](deployment-modes.md) s kopií. Pokud znovu nasadíte s úplným režimem do skupiny prostředků, všechny prostředky, které nejsou zadány v šabloně po vyřešení smyčky kopírování budou odstraněny.

## <a name="example-templates"></a>Ukázkové šablony

Následující příklady ukazují běžné scénáře pro vytvoření více než jedné instance prostředku nebo vlastnosti.

|Šablona  |Popis  |
|---------|---------|
|[Kopírovat úložiště](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystorage.json) |Nasadí více než jeden účet úložiště s číslem indexu v názvu. |
|[Úložiště sériových kopií](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/serialcopystorage.json) |Nasazuje několik účtů úložiště najednou. Název obsahuje číslo indexu. |
|[Kopírování úložiště pomocí pole](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystoragewitharray.json) |Nasazuje několik účtů úložiště. Název obsahuje hodnotu z pole. |
|[Nasazení virtuálního počítače s proměnným počtem datových disků](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Nasadí několik datových disků s virtuálním počítačem. |
|[Více bezpečnostních pravidel](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |Nasadí několik pravidel zabezpečení do skupiny zabezpečení sítě. Vytvoří pravidla zabezpečení z parametru. Parametr naleznete v [tématu více souboru parametrů nsg](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json). |

## <a name="next-steps"></a>Další kroky

* Chcete-li projít kurz, [najdete v tématu Kurz: vytvoření více instancí prostředků pomocí ARM šablony](template-tutorial-create-multiple-instances.md).
* Další použití prvku kopírování naleznete v následujících tématech:
  * [Iterace vlastností v šablonách ARM](copy-properties.md)
  * [Iterace proměnných v šablonách ARM](copy-variables.md)
  * [Výstupní iterace v šablonách ARM](copy-outputs.md)
* Informace o používání kopírování s vnořenými šablonami naleznete [v tématu Použití kopie](linked-templates.md#using-copy).
* Pokud se chcete dozvědět o částech šablony, [přečtěte si](template-syntax.md)část Vytváření šablon ARM .
* Informace o nasazení šablony najdete [v tématu Nasazení aplikace pomocí šablony ARM](deploy-powershell.md).

