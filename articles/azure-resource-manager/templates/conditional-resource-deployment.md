---
title: Podmíněné nasazení se šablonami
description: Popisuje postup podmíněného nasazení prostředku v šabloně Azure Resource Manager.
ms.topic: conceptual
ms.date: 12/03/2019
ms.openlocfilehash: 8ffff0794357ed772bcaea9dce1f93fc7601295a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75485114"
---
# <a name="conditional-deployment-in-resource-manager-templates"></a>Podmíněné nasazení v šablonách Správce prostředků

Někdy je potřeba v šabloně volitelně nasadit prostředek. Pomocí elementu `condition` určete, zda je prostředek nasazen. Hodnota pro tento prvek je přeložena na hodnotu true nebo false. Pokud je hodnota true, je prostředek vytvořen. Pokud je hodnota false, prostředek se nevytvoří. Hodnota se dá použít jenom u celého prostředku.

## <a name="new-or-existing-resource"></a>Nový nebo existující prostředek

Pomocí podmíněného nasazení můžete vytvořit nový prostředek nebo použít existující. Následující příklad ukazuje, jak použít podmínku k nasazení nového účtu úložiště nebo k použití existujícího účtu úložiště.

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[parameters('location')]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

Pokud je parametr **newOrExisting** nastaven na **New**, podmínka se vyhodnotí jako true. Účet úložiště je nasazený. Pokud je však vlastnost **newOrExisting** nastavena na hodnotu **stávající**, bude podmínka vyhodnocena jako NEPRAVDA a účet úložiště nebude nasazen.

Kompletní příklad šablony, která používá `condition` element, najdete v tématu [virtuální počítač s novým nebo existujícím Virtual Network, úložištěm a veřejnou IP adresou](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions).

## <a name="allow-condition"></a>Podmínka povolení

Můžete předat hodnotu parametru, která označuje, zda je podmínka povolena. V následujícím příkladu je nasazený SQL Server a volitelně povoluje IP adresy Azure.

```json
{
    "type": "Microsoft.Sql/servers",
    "name": "[parameters('serverName')]",
    "apiVersion": "2015-05-01-preview",
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
            "name": "AllowAllWindowsAzureIps",
            "apiVersion": "2015-05-01-preview",
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

Použijte funkci [if](template-functions-logical.md#if) a ujistěte se, že je funkce vyhodnocena pouze pro podmínky při nasazení prostředku. Podívejte se na [funkci IF](template-functions-logical.md#if) pro ukázkovou šablonu, která používá if a odkaz s podmíněně nasazeným prostředkem.

Prostředek můžete nastavit [tak,](define-resource-dependency.md) aby byl závislý na podmíněném prostředku přesně stejně jako jakýkoliv jiný prostředek. Pokud není podmíněný prostředek nasazený, Azure Resource Manager ho automaticky odebere z požadovaných závislostí.

## <a name="condition-with-complete-mode"></a>Podmínka s režimem úplného zobrazení

Pokud nasadíte šablonu s [režimem úplného](deployment-modes.md) nasazení a prostředek není nasazen, protože podmínka je vyhodnocena jako NEPRAVDA, výsledek závisí na tom, která REST API verze, kterou použijete k nasazení šablony. Pokud používáte verzi starší než 2019-05-10, prostředek se **neodstraní**. Při 2019-05-10 nebo novějším se prostředek **odstraní**. Nejnovější verze Azure PowerShell a Azure CLI odstraní prostředek, pokud je podmínka nepravdivá.

## <a name="next-steps"></a>Další kroky

* Doporučení k vytváření šablon najdete v tématu [osvědčené postupy pro šablonu Azure Resource Manager](template-best-practices.md).
* Chcete-li vytvořit více instancí prostředku, přečtěte si článek o [zdroji, vlastnosti nebo iteraci proměnných v šablonách Azure Resource Manager](create-multiple-instances.md).