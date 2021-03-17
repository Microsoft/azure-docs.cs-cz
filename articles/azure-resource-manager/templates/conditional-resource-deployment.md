---
title: Podmíněné nasazení se šablonami
description: Popisuje postup podmíněného nasazení prostředku v šabloně Azure Resource Manager (šablona ARM).
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 409d258d7dfe3ed186e5cf97cc0dbe6dc149b849
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101741170"
---
# <a name="conditional-deployment-in-arm-templates"></a>Podmíněné nasazování v šablonách ARM

Někdy je potřeba nasadit prostředek do šablony Azure Resource Manager (šablona ARM) nebo do souboru bicep. Pro šablony JSON použijte `condition` element k určení, zda je prostředek nasazen. Pro bicep pomocí `if` klíčového slova určete, zda je prostředek nasazen. Hodnota podmínky se přeloží na true nebo false. Pokud je hodnota true, je prostředek vytvořen. Pokud je hodnota false, prostředek se nevytvoří. Hodnota se dá použít jenom u celého prostředku.

> [!NOTE]
> Podmíněné nasazení není kaskádou do [podřízených prostředků](child-resource-name-type.md). Pokud chcete podmíněně nasadit prostředek a jeho podřízené prostředky, je nutné použít stejnou podmínku pro každý typ prostředku.

## <a name="deploy-condition"></a>Nasadit podmínku

Můžete předat hodnotu parametru, která indikuje, jestli je prostředek nasazený. V následujícím příkladu se podmíněně nasadí zóna DNS.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "deployZone": {
      "type": "bool"
    }
  },
  "functions": [],
  "resources": [
    {
      "condition": "[parameters('deployZone')]",
      "type": "Microsoft.Network/dnsZones",
      "apiVersion": "2018-05-01",
      "name": "myZone",
      "location": "global"
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param deployZone bool

resource dnsZone 'Microsoft.Network/dnszones@2018-05-01' = if (deployZone) {
  name: 'myZone'
  location: 'global'
}
```

---

Složitější příklad najdete na [logickém serveru Azure SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server).

## <a name="new-or-existing-resource"></a>Nový nebo existující prostředek

Pomocí podmíněného nasazení můžete vytvořit nový prostředek nebo použít existující. Následující příklad ukazuje, jak buď nasadit nový účet úložiště, nebo použít existující účet úložiště.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "string"
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    },
    "newOrExisting": {
      "type": "string",
      "defaultValue": "new",
      "allowedValues": [
        "new",
        "existing"
      ]
    }
  },
  "functions": [],
  "resources": [
    {
      "condition": "[equals(parameters('newOrExisting'), 'new')]",
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
param storageAccountName string
param location string = resourceGroup().location

@allowed([
  'new'
  'existing'
])
param newOrExisting string = 'new'

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = if (newOrExisting == 'new') {
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

Pokud je parametr `newOrExisting` nastaven na hodnotu **New**, podmínka se vyhodnotí jako true. Účet úložiště je nasazený. Pokud je však `newOrExisting` vlastnost nastavena na hodnotu **stávající**, bude podmínka vyhodnocena jako NEPRAVDA a účet úložiště nebude nasazen.

Kompletní příklad šablony, která používá `condition` element, najdete v tématu [virtuální počítač s novým nebo existujícím Virtual Network, úložištěm a veřejnou IP adresou](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions).

## <a name="runtime-functions"></a>Běhové funkce

Použijete-li funkci [reference](template-functions-resource.md#reference) nebo [list](template-functions-resource.md#list) s prostředkem, který je podmíněně nasazen, je funkce vyhodnocena i v případě, že prostředek není nasazen. Pokud funkce odkazuje na prostředek, který neexistuje, zobrazí se chyba.

Použijte funkci [if](template-functions-logical.md#if) a ujistěte se, že je funkce vyhodnocena pouze pro podmínky při nasazení prostředku. Podívejte se na [funkci IF](template-functions-logical.md#if) pro ukázkovou šablonu, která používá `if` a `reference` s podmíněně nasazeným prostředkem.

Prostředek můžete nastavit [tak,](define-resource-dependency.md) aby byl závislý na podmíněném prostředku přesně stejně jako jakýkoliv jiný prostředek. Pokud není podmíněný prostředek nasazený, Azure Resource Manager ho automaticky odebere z požadovaných závislostí.

## <a name="complete-mode"></a>Režim dokončení

Pokud nasadíte šablonu s [režimem úplného](deployment-modes.md) nasazení a prostředek není nasazený, protože je `condition` vyhodnocen jako false, výsledek závisí na tom, která REST API verze, kterou použijete k nasazení šablony. Pokud používáte verzi starší než 2019-05-10, prostředek se **neodstraní**. Při 2019-05-10 nebo novějším se prostředek **odstraní**. Nejnovější verze Azure PowerShell a Azure CLI odstraní prostředek, pokud je podmínka nepravdivá.

## <a name="next-steps"></a>Další kroky

* Microsoft Learn modul, který pokrývá podmíněné nasazení, najdete v tématu [Správa složitých nasazení cloudu pomocí pokročilých funkcí šablon ARM](/learn/modules/manage-deployments-advanced-arm-template-features/).
* Doporučení k vytváření šablon najdete v tématu [osvědčené postupy pro šablonu ARM](template-best-practices.md).
* Pokud chcete vytvořit víc instancí prostředku, přečtěte si téma [iterace prostředků v šablonách ARM](copy-resources.md).
