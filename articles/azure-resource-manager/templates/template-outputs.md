---
title: Výstupy v šablonách
description: Popisuje, jak definovat výstupní hodnoty v šabloně Azure Resource Manager (šablona ARM) a souboru bicep.
ms.topic: conceptual
ms.date: 02/19/2021
ms.openlocfilehash: 2b6a6afa127bf43102103baadae576233843f00d
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102123407"
---
# <a name="outputs-in-arm-templates"></a>Výstupy v šablonách ARM

Tento článek popisuje, jak v šabloně Azure Resource Manager (šablona ARM) a souboru bicep definovat výstupní hodnoty. Výstupy použijete, když potřebujete vrátit hodnoty z nasazených prostředků.

Formát každé výstupní hodnoty se musí přeložit na jeden z [datových typů](data-types.md).

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="define-output-values"></a>Definovat výstupní hodnoty

Následující příklad ukazuje, jak vrátit vlastnost z nasazeného prostředku.

# <a name="json"></a>[JSON](#tab/json)

Pro JSON přidejte `outputs` oddíl do šablony. Výstupní hodnota získá plně kvalifikovaný název domény pro veřejnou IP adresu.

```json
"outputs": {
  "hostname": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))).dnsSettings.fqdn]"
    },
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

Pro bicep použijte `output` klíčové slovo.

V následujícím příkladu `publicIP` je identifikátor veřejné IP adresy nasazené v souboru bicep. Výstupní hodnota získá plně kvalifikovaný název domény pro veřejnou IP adresu.

```bicep
output hostname string = publicIP.properties.dnsSettings.fqdn
```

---

Pokud potřebujete výstup vlastnosti, která má v názvu pomlčku, místo zápisu tečky použijte hranaté závorky kolem názvu. Použijte například  `['property-name']` místo `.property-name` .

# <a name="json"></a>[JSON](#tab/json)

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "user": {
            "user-name": "Test Person"
        }
    },
    "resources": [
    ],
    "outputs": {
        "nameResult": {
            "type": "string",
            "value": "[variables('user')['user-name']]"
        }
    }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var user = {
  'user-name': 'Test Person'
}

output stringOutput string = user['user-name']
```

---

## <a name="conditional-output"></a>Podmíněný výstup

Můžete podmíněně vracet hodnotu. Obvykle používáte podmíněný výstup, když jste [provedli podmíněně nasazení](conditional-resource-deployment.md) prostředku. Následující příklad ukazuje, jak podmíněně vracet ID prostředku pro veřejnou IP adresu na základě toho, zda byla nasazena nová:

# <a name="json"></a>[JSON](#tab/json)

Ve formátu JSON přidejte `condition` element pro definování, zda je vrácen výstup.

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

K určení podmíněného výstupu v bicep použijte `?` operátor. Následující příklad buďto vrátí adresu URL koncového bodu nebo prázdný řetězec v závislosti na podmínce.

```bicep
param deployStorage bool = true
param storageName string
param location string = resourceGroup().location

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = if (deployStorage) {
  name: storageName
  location: location
  kind: 'StorageV2'
  sku:{
    name:'Standard_LRS'
    tier: 'Standard'
  }
  properties: {
    accessTier: 'Hot'
  }
}

output endpoint string = deployStorage ? sa.properties.primaryEndpoints.blob : ''
```

---

Jednoduchý příklad podmíněného výstupu naleznete v tématu [podmíněná výstupní šablona](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json).

## <a name="dynamic-number-of-outputs"></a>Dynamický počet výstupů

V některých scénářích neznáte počet instancí hodnoty, které potřebujete vrátit při vytváření šablony. Můžete vrátit proměnný počet hodnot pomocí iterativního výstupu.

# <a name="json"></a>[JSON](#tab/json)

Ve formátu JSON přidejte `copy` element pro iteraci výstupu.

```json
"outputs": {
  "storageEndpoints": {
    "type": "array",
    "copy": {
      "count": "[parameters('storageCount')]",
      "input": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

Iterativní výstup není v současné době pro bicep k dispozici.

---

Další informace najdete v tématu [výstupní iterace v šablonách ARM](copy-outputs.md).

## <a name="linked-templates"></a>Propojené šablony

V šablonách JSON můžete související šablony nasadit pomocí [propojených šablon](linked-templates.md). K načtení výstupní hodnoty z propojené šablony použijte [odkazovou](template-functions-resource.md#reference) funkci v nadřazené šabloně. Syntaxe v nadřazené šabloně je:

```json
"[reference('<deploymentName>').outputs.<propertyName>.value]"
```

Následující příklad ukazuje, jak nastavit IP adresu v nástroji pro vyrovnávání zatížení načtením hodnoty z propojené šablony.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

Pokud název vlastnosti obsahuje spojovník, místo notace tečky použijte hranaté závorky kolem názvu.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs['resource-ID'].value]"
}
```

Funkci nelze použít `reference` v části výstupy [vnořené šablony](linked-templates.md#nested-template). Chcete-li vrátit hodnoty nasazeného prostředku ve vnořené šabloně, převeďte vnořenou šablonu na propojenou šablonu.

[Šablona veřejné IP adresy](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) vytvoří veřejnou IP adresu a vypíše ID prostředku. [Šablona nástroje pro vyrovnávání zatížení](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) odkazuje na předchozí šablonu. Při vytváření nástroje pro vyrovnávání zatížení používá ID prostředku ve výstupu.

## <a name="modules"></a>Moduly

V souborech bicep můžete k nasazení souvisejících šablon použít moduly. K načtení výstupní hodnoty z modulu použijte následující syntaxi:

```bicep
<module-name>.outputs.<property-name>
```

Následující příklad ukazuje, jak nastavit IP adresu v nástroji pro vyrovnávání zatížení načtením hodnoty z modulu. Název modulu je `publicIP` .

```bicep
publicIPAddress: {
  id: publicIP.outputs.resourceID
}
```

## <a name="example-template"></a>Příklad šablony

Následující šablona neimplementuje žádné prostředky. Ukazuje několik způsobů vrácení výstupů různých typů.

# <a name="json"></a>[JSON](#tab/json)

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/outputs.json":::

# <a name="bicep"></a>[Bicep](#tab/bicep)

Bicep v současné době nepodporuje smyčky.

:::code language="bicep" source="~/resourcemanager-templates/azure-resource-manager/outputs.bicep":::

---

## <a name="get-output-values"></a>Získat výstupní hodnoty

Po úspěšném nasazení se výstupní hodnoty automaticky vrátí do výsledků nasazení.

Chcete-li získat výstupní hodnoty z historie nasazení, můžete použít skript.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
(Get-AzResourceGroupDeployment `
  -ResourceGroupName <resource-group-name> `
  -Name <deployment-name>).Outputs.resourceID.value
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az deployment group show \
  -g <resource-group-name> \
  -n <deployment-name> \
  --query properties.outputs.resourceID.value
```

---

## <a name="next-steps"></a>Další kroky

* Další informace o dostupných vlastnostech výstupů najdete v tématu [pochopení struktury a syntaxe šablon ARM](template-syntax.md).
