---
title: Deklarace prostředků v šablonách
description: Popisuje, jak deklarovat prostředky k nasazení v šabloně Azure Resource Manager (šablona ARM).
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 13f4a08162c40cbb36173627d88a4a8202a4ed26
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744857"
---
# <a name="resource-declaration-in-arm-templates"></a>Deklarace prostředků v šablonách ARM

K nasazení prostředku pomocí šablony Azure Resource Manager (šablona ARM) přidáte deklaraci prostředku. Použijte `resources` pole pro šablonu JSON nebo `resource` klíčové slovo pro bicep.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="set-resource-type-and-version"></a>Nastavit typ a verzi prostředku

Při přidávání prostředku do šablony Začněte nastavením typu prostředku a verze rozhraní API. Tyto hodnoty určují další vlastnosti, které jsou k dispozici pro daný prostředek.

Následující příklad ukazuje, jak nastavit typ prostředku a verzi rozhraní API pro účet úložiště. V tomto příkladu se nezobrazuje úplná deklarace prostředku.

# <a name="json"></a>[JSON](#tab/json)

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    ...
  }
]
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  ...
}
```

---

Pro bicep můžete nastavit symbolický název prostředku. V předchozím příkladu je symbolický název `sa` . Můžete použít libovolnou hodnotu symbolického názvu, ale nemůže být stejná jako jiný prostředek, parametr nebo proměnná v šabloně. Symbolický název není stejný jako název prostředku. Pomocí symbolického názvu se snadno odkazuje na prostředek v jiných částech šablony.

## <a name="set-resource-name"></a>Nastavit název prostředku

Každý prostředek má název. Při nastavování názvu prostředku věnujte pozornost [pravidlům a omezením názvů prostředků](../management/resource-name-rules.md).

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "storageAccountName": {
    "type": "string",
    "minLength": 3,
    "maxLength": 24
  }
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    "name": "[parameters('storageAccountName')]",
    ...
  }
]
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minLength(3)
@maxLength(24)
param storageAccountName string

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageAccountName
  ...
}
```

---

## <a name="set-location"></a>Nastavení umístění

Mnoho prostředků vyžaduje umístění. Můžete určit, zda prostředek potřebuje umístění buď prostřednictvím technologie IntelliSense, nebo [odkazem na šablonu](/azure/templates/). Následující příklad přidá parametr Location, který se používá pro účet úložiště.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "storageAccountName": {
    "type": "string",
    "minLength": 3,
    "maxLength": 24
  },
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    "name": "[parameters('storageAccountName')]",
    "location": "[parameters('location')]",
    ...
  }
]
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minLength(3)
@maxLength(24)
param storageAccountName string
param location string = resourceGroup().location

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageAccountName
  location: location
  ...
}
```

---

Další informace najdete v tématu [Nastavení umístění prostředků v ŠABLONĚ ARM](resource-location.md).

## <a name="set-tags"></a>Nastavit značky

V průběhu nasazování můžete použít značky na prostředek. Značky vám pomůžou logicky organizovat nasazené prostředky. Příklady různých způsobů, jak lze zadat značky, naleznete v tématu [Tagy šablony ARM](../management/tag-resources.md#arm-templates).

## <a name="set-resource-specific-properties"></a>Nastavení vlastností specifických pro prostředky

Předchozí vlastnosti jsou obecné pro většinu typů prostředků. Po nastavení těchto hodnot je potřeba nastavit vlastnosti, které jsou specifické pro typ prostředku, který nasazujete.

Použijte technologii IntelliSense nebo [odkaz na šablonu](/azure/templates/) k určení, které vlastnosti jsou k dispozici a které jsou požadovány. Následující příklad nastaví zbývající vlastnosti účtu úložiště.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "string",
      "minLength": 3,
      "maxLength": 24
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
  },
  "functions": [],
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[parameters('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "StorageV2",
      "properties": {
        "accessTier": "Hot"
      }
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minLength(3)
@maxLength(24)
param storageAccountName string
param location string = resourceGroup().location

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageAccountName
  location: location
  sku: {
    name: 'Standard_LRS'
    tier: 'Standard'
  }
  kind: 'StorageV2'
  properties: {
    accessTier: 'Hot'
  }
}
```

---

## <a name="next-steps"></a>Další kroky

* Postup při podmíněném nasazení prostředku najdete [v tématu podmíněné nasazení v šablonách ARM](conditional-resource-deployment.md).
* Pokud chcete nastavit závislosti prostředků, přečtěte si téma [Definování pořadí nasazení prostředků v šablonách ARM](define-resource-dependency.md).
