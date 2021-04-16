---
title: Nasazení více instancí prostředků
description: Pomocí operace kopírování a polí v šabloně Azure Resource Manager (šablona ARM) můžete typ prostředku mnohokrát nasadit.
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: 5ddb0cabf0acae1ffe9b9e77e6defa70f9cbd61b
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479963"
---
# <a name="resource-iteration-in-arm-templates"></a>Iterace prostředků v šablonách ARM

V tomto článku se dozvíte, jak vytvořit více než jednu instanci prostředku v šabloně Azure Resource Manager (šablona ARM). Přidáním smyčky kopírování do části Resources (prostředky) vaší šablony můžete dynamicky nastavit počet nasazených prostředků. Nemusíte se také vyhnout opakování syntaxe šablony.

Můžete také použít smyčku kopírování s [vlastnostmi](copy-properties.md), [proměnnými](copy-variables.md)a [výstupy](copy-outputs.md).

Pokud potřebujete určit, jestli je prostředek nasazený vůbec, viz [Podmínka elementu](conditional-resource-deployment.md).

## <a name="syntax"></a>Syntax

# <a name="json"></a>[JSON](#tab/json)

Přidáním `copy` elementu do části Resources (prostředky) vaší šablony můžete nasadit více instancí prostředku. `copy`Element má následující obecný formát:

```json
"copy": {
  "name": "<name-of-loop>",
  "count": <number-of-iterations>,
  "mode": "serial" <or> "parallel",
  "batchSize": <number-to-deploy-serially>
}
```

`name`Vlastnost je libovolná hodnota, která identifikuje smyčku. `count`Vlastnost určuje počet iterací, které chcete pro typ prostředku.

Pomocí `mode` vlastností a `batchSize` Určete, jestli jsou prostředky nasazené paralelně nebo v pořadí. Tyto vlastnosti jsou popsány v [sériové nebo paralelní](#serial-or-parallel).

# <a name="bicep"></a>[Bicep](#tab/bicep)

Smyčky lze použít k deklaraci několika prostředků:

- Iterace nad polem:

  ```bicep
  @batchSize(<number>)
  resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for <item> in <collection>: {
    <resource-properties>
  }]
  ```

- Iterace nad prvky pole

  ```bicep
  @batchSize(<number>)
  resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for (<item>, <index>) in <collection>: {
    <resource-properties>
  }]
  ```

- Index smyčky using

  ```bicep
  @batchSize(<number>)
  resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for <index> in range(<start>, <stop>): {
    <resource-properties>
  }]
  ```

---

## <a name="copy-limits"></a>Omezení kopírování

Počet nemůže být větší než 800.

Počet nemůže být záporné číslo. Pokud nasadíte šablonu s poslední verzí rozhraní příkazového řádku Azure CLI, PowerShellu nebo REST API, může to být nula. Konkrétně je nutné použít:

- Azure PowerShell **2,6** nebo novější
- Azure CLI **2.0.74** nebo novější
- REST API verze **2019-05-10** nebo novější
- [Odkazovaná nasazení](linked-templates.md) musí pro typ prostředku nasazení používat rozhraní API verze **2019-05-10** nebo novější.

Starší verze prostředí PowerShell, rozhraní příkazového řádku a REST API pro počet nepodporují nulu.

Při nasazení v [režimu úplného](deployment-modes.md) použití se smyčkou Copy buďte opatrní. Pokud znovu nasadíte v režimu úplného nasazení do skupiny prostředků, všechny prostředky, které nejsou zadané v šabloně po vyřešení smyčky kopírování, se odstraní.

## <a name="resource-iteration"></a>Iterace prostředku

Následující příklad vytvoří počet účtů úložiště zadaných v `storageCount` parametru.

# <a name="json"></a>[JSON](#tab/json)

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
  ]
}
```

Všimněte si, že název každého prostředku obsahuje `copyIndex()` funkci, která vrací aktuální iteraci ve smyčce. `copyIndex()` je založen na nule. V následujícím příkladu:

```json
"name": "[concat('storage', copyIndex())]",
```

Vytvoří tyto názvy:

- storage0
- storage1
- storage2.

K posunutí hodnoty indexu můžete předat hodnotu ve `copyIndex()` funkci. Počet iterací je stále specifikován v elementu Copy, ale hodnota `copyIndex` je posunuta podle zadané hodnoty. V následujícím příkladu:

```json
"name": "[concat('storage', copyIndex(1))]",
```

Vytvoří tyto názvy:

- storage1
- storage2
- storage3

Operace kopírování je užitečná při práci s poli, protože můžete iterovat přes každý prvek v poli. Použijte `length` funkci v poli k určení počtu iterací a `copyIndex` k načtení aktuálního indexu v poli.

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param storageCount int = 2

resource storage_id 'Microsoft.Storage/storageAccounts@2019-04-01' = [for i in range(0, storageCount): {
  name: '${i}storage${uniqueString(resourceGroup().id)}'
  location: resourceGroup().location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  properties: {}
}]
```

Všimněte si, že se index `i` používá při vytváření názvu prostředku účtu úložiště.

---

Následující příklad vytvoří jeden účet úložiště pro každý název zadaný v parametru.

# <a name="json"></a>[JSON](#tab/json)

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

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param storageNames array = [
  'contoso'
  'fabrikam'
  'coho'
]

resource storageNames_id 'Microsoft.Storage/storageAccounts@2019-04-01' = [for name in storageNames: {
  name: concat(name, uniqueString(resourceGroup().id))
  location: resourceGroup().location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  properties: {}
}]
```

---

Pokud chcete vrátit hodnoty z nasazených prostředků, můžete použít příkaz [Kopírovat v části výstupy](copy-outputs.md).

## <a name="serial-or-parallel"></a>Sériové nebo paralelní

Ve výchozím nastavení Správce prostředků vytvoří paralelní prostředky. Neplatí pro počet paralelně nasazených prostředků, s výjimkou celkového limitu 800 prostředků v šabloně. Pořadí, ve kterém jsou vytvořeny, není zaručeno.

Můžete ale chtít určit, že se prostředky nasazují v pořadí. Například při aktualizaci produkčního prostředí můžete aktualizace rozložit, aby se v jednom okamžiku aktualizovalo jenom určité číslo.

Pokud například chcete sériové nasazení účtů úložiště vytvořit dvakrát, použijte:

# <a name="json"></a>[JSON](#tab/json)

Pro sériové nasazení více než jedné instance prostředku nastavte `mode` **sériové** a `batchSize` na počet instancí, které se mají nasadit v jednom okamžiku. V případě sériového režimu Správce prostředků ve smyčce vytvoří závislost na dřívějších instancích, takže nespustí jednu dávku, dokud se předchozí dávka nedokončí.

Hodnota pro `batchSize` nemůže být větší než hodnota `count` elementu Copy.

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

`mode`Vlastnost také akceptuje **paralelní**, což je výchozí hodnota.

# <a name="bicep"></a>[Bicep](#tab/bicep)

Pro sériové nasazení více než jedné instance prostředku nastavte `batchSize` [dekoratér](./bicep-file.md#resource-and-module-decorators) na počet instancí, které se mají nasadit najednou. V případě sériového režimu Správce prostředků ve smyčce vytvoří závislost na dřívějších instancích, takže nespustí jednu dávku, dokud se předchozí dávka nedokončí.

```bicep
@batchSize(2)
resource storage_id 'Microsoft.Storage/storageAccounts@2019-04-01' = [for i in range(0, 4): {
  name: '${i}storage${uniqueString(resourceGroup().id)}'
  location: resourceGroup().location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  properties: {}
}]
```

---

## <a name="iteration-for-a-child-resource"></a>Iterace pro podřízený prostředek

U podřízeného prostředku nemůžete použít kopírovací smyčku. Chcete-li vytvořit více než jednu instanci prostředku, který obvykle definujete jako vnořený v rámci jiného prostředku, je nutné místo toho vytvořit tento prostředek jako prostředek nejvyšší úrovně. Pomocí vlastností typ a název můžete definovat relaci s nadřazeným prostředkem.

Předpokládejme například, že obvykle definujete datovou sadu jako podřízený prostředek v rámci objektu pro vytváření dat.

```json
"resources": [
{
  "type": "Microsoft.DataFactory/factories",
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

Pokud chcete vytvořit více než jednu datovou sadu, přesuňte ji mimo datovou továrnu. Datová sada musí být na stejné úrovni jako objekt pro vytváření dat, ale je stále podřízeným prostředkem objektu pro vytváření dat. Můžete zachovat vztah mezi datovou sadou a datovou továrnou prostřednictvím vlastností typu a názvu. Vzhledem k tomu, že typ již nelze odvodit z jeho pozice v šabloně, je nutné zadat plně kvalifikovaný typ ve formátu: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}` .

Pokud chcete vytvořit relaci nadřazený-podřízený s instancí datové továrny, zadejte název datové sady, která zahrnuje název nadřazeného prostředku. Použijte formát: `{parent-resource-name}/{child-resource-name}`.

Následující příklad ukazuje implementaci:

# <a name="json"></a>[JSON](#tab/json)

```json
"resources": [
{
  "type": "Microsoft.DataFactory/factories",
  "name": "exampleDataFactory",
  ...
},
{
  "type": "Microsoft.DataFactory/factories/datasets",
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

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource dataFactoryName_resource 'Microsoft.DataFactory/factories@2018-06-01' = {
  name: "exampleDataFactory"
  ...
}

resource dataFactoryName_ArmtemplateTestDatasetIn 'Microsoft.DataFactory/factories/datasets@2018-06-01' = [for i in range(0, 3): {
  name: 'exampleDataFactory/exampleDataset${i}'
  ...
}
```

---

## <a name="example-templates"></a>Příklady šablon

Následující příklady znázorňují běžné scénáře pro vytvoření více než jedné instance prostředku nebo vlastnosti.

|Template (Šablona)  |Popis  |
|---------|---------|
|[Kopírovat úložiště](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystorage.json) |Nasadí více než jeden účet úložiště s číslem indexu v názvu. |
|[Úložiště sériového kopírování](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/serialcopystorage.json) |Nasadí několik účtů úložiště v jednom okamžiku. Název zahrnuje číslo indexu. |
|[Kopírování úložiště pomocí pole](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystoragewitharray.json) |Nasadí několik účtů úložiště. Název obsahuje hodnotu z pole. |

## <a name="next-steps"></a>Další kroky

- Pokud chcete nastavit závislosti na prostředcích, které jsou vytvořené ve smyčce kopírování, přečtěte si téma [Definování pořadí nasazení prostředků v šablonách ARM](define-resource-dependency.md).
- Kurz najdete v tématu [kurz: vytvoření více instancí prostředků pomocí šablon ARM](template-tutorial-create-multiple-instances.md).
- Microsoft Learn modul, který pokrývá kopírování prostředků, najdete v tématu [Správa složitých nasazení cloudu pomocí pokročilých funkcí šablon ARM](/learn/modules/manage-deployments-advanced-arm-template-features/).
- Další použití smyčky kopírování najdete v těchto tématech:
  - [Iterace vlastnosti v šablonách ARM](copy-properties.md)
  - [Iterace proměnných v šablonách ARM](copy-variables.md)
  - [Výstupní iterace v šablonách ARM](copy-outputs.md)
- Informace o použití kopírování s vnořenými šablonami naleznete v tématu [using Copy](linked-templates.md#using-copy).
