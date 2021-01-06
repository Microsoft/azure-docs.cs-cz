---
title: Podmíněné nasazení se šablonami
description: Popisuje postup podmíněného nasazení prostředku v šabloně Azure Resource Manager (šablona ARM).
ms.topic: conceptual
ms.date: 12/17/2020
ms.openlocfilehash: 5650f7fb9f1483f2dc7059607732ecc68cbb7b9d
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2021
ms.locfileid: "97934777"
---
# <a name="conditional-deployment-in-arm-templates"></a>Podmíněné nasazení v šablonách ARM

Někdy potřebujete volitelně nasadit prostředek do šablony Azure Resource Manager (šablona ARM). Použijte `condition` element k určení, zda je prostředek nasazen. Hodnota pro tento prvek je přeložena na hodnotu true nebo false. Pokud je hodnota true, je prostředek vytvořen. Pokud je hodnota false, prostředek se nevytvoří. Hodnota se dá použít jenom u celého prostředku.

> [!NOTE]
> Podmíněné nasazení není kaskádou do [podřízených prostředků](child-resource-name-type.md). Pokud chcete podmíněně nasadit prostředek a jeho podřízené prostředky, je nutné použít stejnou podmínku pro každý typ prostředku.

## <a name="new-or-existing-resource"></a>Nový nebo existující prostředek

Pomocí podmíněného nasazení můžete vytvořit nový prostředek nebo použít existující. Následující příklad ukazuje, jak použít `condition` k nasazení nového účtu úložiště nebo k použití existujícího účtu úložiště.

```json
{
  "condition": "[equals(parameters('newOrExisting'),'new')]",
  "type": "Microsoft.Storage/storageAccounts",
  "apiVersion": "2017-06-01",
  "name": "[variables('storageAccountName')]",
  "location": "[parameters('location')]",
  "sku": {
    "name": "[variables('storageAccountType')]"
  },
  "kind": "Storage",
  "properties": {}
}
```

Pokud je parametr `newOrExisting` nastaven na hodnotu **New**, podmínka se vyhodnotí jako true. Účet úložiště je nasazený. Pokud je však `newOrExisting` vlastnost nastavena na hodnotu **stávající**, bude podmínka vyhodnocena jako NEPRAVDA a účet úložiště nebude nasazen.

Kompletní příklad šablony, která používá `condition` element, najdete v tématu [virtuální počítač s novým nebo existujícím Virtual Network, úložištěm a veřejnou IP adresou](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions).

## <a name="allow-condition"></a>Podmínka povolení

Můžete předat hodnotu parametru, která označuje, zda je podmínka povolena. V následujícím příkladu je nasazený SQL Server a volitelně povoluje IP adresy Azure.

```json
{
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2015-05-01-preview",
  "name": "[parameters('serverName')]",
  "location": "[parameters('location')]",
  "properties": {
    "administratorLogin": "[parameters('administratorLogin')]",
    "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
    "version": "12.0"
  },
  "resources": [
    {
      "condition": "[parameters('allowAzureIPs')]",
      "type": "firewallRules",
      "apiVersion": "2015-05-01-preview",
      "name": "AllowAllWindowsAzureIps",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.Sql/servers/', parameters('serverName'))]"
      ],
      "properties": {
        "endIpAddress": "0.0.0.0",
        "startIpAddress": "0.0.0.0"
      }
    }
  ]
}
```

Úplnou šablonu najdete v tématu [logický Server Azure SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server).

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
