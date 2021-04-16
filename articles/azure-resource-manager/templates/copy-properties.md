---
title: Definování více instancí vlastnosti
description: Pomocí operace kopírování v šabloně Azure Resource Manager (šablona ARM) můžete iterovat několikrát při vytváření vlastnosti prostředku.
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: 16c293f1c3aff64aeb8b6cae4b7f1aa14dcd0a77
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479997"
---
# <a name="property-iteration-in-arm-templates"></a>Iterace vlastnosti v šablonách ARM

V tomto článku se dozvíte, jak vytvořit více než jednu instanci vlastnosti v šabloně Azure Resource Manager (šablona ARM). Přidáním smyčky kopírování do oddílu vlastností prostředku ve vaší šabloně můžete dynamicky nastavit počet položek pro vlastnost během nasazování. Nemusíte se také vyhnout opakování syntaxe šablony.

Můžete použít pouze kopírovací smyčku s prostředky nejvyšší úrovně, i když použijete smyčku kopírování na vlastnost. Další informace o změně podřízeného prostředku na prostředek nejvyšší úrovně najdete v tématu [iterace u podřízeného prostředku](copy-resources.md#iteration-for-a-child-resource).

Můžete také použít kopírovací smyčku s [prostředky](copy-resources.md), [proměnnými](copy-variables.md)a [výstupy](copy-outputs.md).

## <a name="syntax"></a>Syntax

# <a name="json"></a>[JSON](#tab/json)

Přidejte `copy` element do části Resources (prostředky) vaší šablony a nastavte počet položek pro vlastnost. Element Copy má následující obecný formát:

```json
"copy": [
  {
    "name": "<name-of-property>",
    "count": <number-of-iterations>,
    "input": <values-for-the-property>
  }
]
```

Pro `name` Zadejte název vlastnosti prostředku, kterou chcete vytvořit.

`count`Vlastnost určuje počet iterací, které chcete pro vlastnost.

`input`Vlastnost určuje vlastnosti, které chcete opakovat. Vytvoříte pole prvků konstruovaných z hodnoty `input` Vlastnosti.

# <a name="bicep"></a>[Bicep](#tab/bicep)

Smyčky lze použít k deklaraci více vlastností:

- Iterace nad polem:

  ```bicep
  <property-name>: [for <item> in <collection>: {
    <properties>
  }]
  ```

- Iterace nad prvky pole

  ```bicep
  <property-name>: [for (<item>, <index>) in <collection>: {
    <properties>
  }]
  ```

- Index smyčky using

  ```bicep
  <property-name>: [for <index> in range(<start>, <stop>): {
    <properties>
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

## <a name="property-iteration"></a>Iterace vlastnosti

Následující příklad ukazuje, jak použít smyčku kopírování na `dataDisks` vlastnost na virtuálním počítači:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numberOfDataDisks": {
      "type": "int",
      "minValue": 0,
      "maxValue": 16,
      "defaultValue": 3,
      "metadata": {
        "description": "The number of dataDisks to create."
      }
    },
    ...
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2020-06-01",
      ...
      "properties": {
        "storageProfile": {
          ...
          "copy": [
            {
              "name": "dataDisks",
              "count": "[parameters('numberOfDataDisks')]",
              "input": {
                "lun": "[copyIndex('dataDisks')]",
                "createOption": "Empty",
                "diskSizeGB": 1023
              }
            }
          ]
        }
        ...
      }
    }
  ]
}
```

Všimněte si, že při použití `copyIndex` v rámci iterace vlastnosti je nutné zadat název iterace. Iterace vlastnosti také podporuje argument posunu. Posun musí být zadán za názvem iterace, například `copyIndex('dataDisks', 1)` .

Nasazená šablona bude:

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2020-06-01",
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

`copy`Element je pole, abyste mohli zadat více než jednu vlastnost pro daný prostředek.

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

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minValue(0)
@maxValue(16)
@description('The number of dataDisks to be returned in the output array.')
param numberOfDataDisks int = 16

resource vmName 'Microsoft.Compute/virtualMachines@2020-06-01' = {
  ...
  properties: {
    storageProfile: {
      ...
      dataDisks: [for i in range(0, numberOfDataDisks): {
        lun: i
        createOption: 'Empty'
        diskSizeGB: 1023
      }]
    }
    ...
  }
}
```

Nasazená šablona bude:

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2020-06-01",
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

---

Můžete použít iteraci prostředků a vlastností společně. Odkázat na iteraci vlastnosti podle názvu.

# <a name="json"></a>[JSON](#tab/json)

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

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource vnetname_resource 'Microsoft.Network/virtualNetworks@2018-04-01' = [for i in range(0, 2): {
  name: concat(vnetname, i)
  location: resourceGroup().location
  properties: {
    addressSpace: {
      addressPrefixes: [
        addressPrefix
      ]
    }
    subnets: [for j in range(0, 2): {
      name: 'subnet-${j}'
      properties: {
        addressPrefix: subnetAddressPrefix[j]
      }
    }]
  }
}]
```

---

## <a name="example-templates"></a>Příklady šablon

Následující příklad ukazuje běžný scénář pro vytvoření více než jedné hodnoty pro vlastnost.

|Template (Šablona)  |Popis  |
|---------|---------|
|[Nasazení virtuálního počítače s proměnným počtem datových disků](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Nasadí několik datových disků s virtuálním počítačem. |

## <a name="next-steps"></a>Další kroky

- Kurz najdete v tématu [kurz: vytvoření více instancí prostředků pomocí šablon ARM](template-tutorial-create-multiple-instances.md).
- Další použití smyčky kopírování najdete v těchto tématech:
  - [Iterace prostředků v šablonách ARM](copy-resources.md)
  - [Iterace proměnných v šablonách ARM](copy-variables.md)
  - [Výstupní iterace v šablonách ARM](copy-outputs.md)
- Pokud se chcete dozvědět o oddílech šablony, přečtěte si téma [pochopení struktury a syntaxe šablon ARM](template-syntax.md).
- Informace o tom, jak šablonu nasadit, najdete v tématu [nasazení prostředků pomocí šablon ARM a Azure PowerShell](deploy-powershell.md).
