---
title: Podmíněné nasazení pomocí šablon Azure Resource Manager
description: Popisuje postup podmíněného nasazení prostředku v šabloně Azure Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: tomfitz
ms.openlocfilehash: 88f8b6a8dcce0e498a7b81b8741072bcf4cfcad8
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259506"
---
# <a name="conditional-deployment-in-resource-manager-templates"></a>Podmíněné nasazení v šablonách Správce prostředků

Někdy je potřeba v šabloně volitelně nasadit prostředek. `condition` Použijte element k určení, zda je prostředek nasazen. Hodnota pro tento prvek je přeložena na hodnotu true nebo false. Pokud je hodnota true, je prostředek vytvořen. Pokud je hodnota false, prostředek se nevytvoří. Hodnota se dá použít jenom u celého prostředku.

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

Použijete-li funkci [reference](resource-group-template-functions-resource.md#reference) nebo [list](resource-group-template-functions-resource.md#list) s prostředkem, který je podmíněně nasazen, je funkce vyhodnocena i v případě, že prostředek není nasazen. Pokud funkce odkazuje na prostředek, který neexistuje, zobrazí se chyba.

Použijte funkci [if](resource-group-template-functions-logical.md#if) a ujistěte se, že je funkce vyhodnocena pouze pro podmínky při nasazení prostředku. Podívejte se na [funkci IF](resource-group-template-functions-logical.md#if) pro ukázkovou šablonu, která používá if a odkaz s podmíněně nasazeným prostředkem.

## <a name="next-steps"></a>Další kroky

* Doporučení k vytváření šablon najdete v tématu [osvědčené postupy pro šablonu Azure Resource Manager](template-best-practices.md).
* Chcete-li vytvořit více instancí prostředku, přečtěte si článek o [zdroji, vlastnosti nebo iteraci proměnných v šablonách Azure Resource Manager](resource-group-create-multiple.md).