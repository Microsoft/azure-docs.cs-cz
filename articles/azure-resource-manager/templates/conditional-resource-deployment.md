---
title: Podmíněné nasazení se šablonami
description: Popisuje, jak podmíněně nasadit prostředek v šabloně Azure Resource Manager.
ms.topic: conceptual
ms.date: 12/03/2019
ms.openlocfilehash: f170710118c0e3de6f3643b6216ed55b83b5c7df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153416"
---
# <a name="conditional-deployment-in-arm-templates"></a>Podmíněné nasazení v šablonách ARM

Někdy je potřeba volitelně nasadit prostředek v šabloně Azure Resource Manager (ARM). Pomocí `condition` prvku určete, zda je prostředek nasazen. Hodnota pro tento prvek překládá na true nebo false. Pokud je hodnota true, je vytvořen prostředek. Pokud je hodnota false, prostředek není vytvořen. Hodnotu lze použít pouze pro celý zdroj.

## <a name="new-or-existing-resource"></a>Nový nebo existující zdroj

Podmíněné nasazení můžete použít k vytvoření nového prostředku nebo použít existující prostředek. Následující příklad ukazuje, jak použít podmínku k nasazení nového účtu úložiště nebo použít existující účet úložiště.

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

Když je parametr **newOrExisting** nastaven na **hodnotu new**, podmínka se vyhodnotí jako true. Účet úložiště se nasadí. Však když **newOrExisting** je nastavena na **existující**, podmínka vyhodnotí false a účet úložiště není nasazen.

Kompletní ukázková šablona, `condition` která používá prvek, najdete [v tématu Virtuální počítač s novou nebo existující virtuální sítí, úložištěm a veřejnou IP adresou](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions).

## <a name="allow-condition"></a>Povolit podmínku

Můžete předat hodnotu parametru, která označuje, zda je podmínka povolena. Následující příklad nasazuje sql server a volitelně umožňuje IP adresy Azure.

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

Kompletní šablonu najdete v tématu [Logický server Azure SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server).

## <a name="runtime-functions"></a>Runtime funkce

Pokud používáte [odkaz](template-functions-resource.md#reference) nebo [seznam](template-functions-resource.md#list) funkce s prostředek, který je podmíněně nasazen, funkce se vyhodnotí i v případě, že prostředek není nasazen. Zobrazí se chyba, pokud funkce odkazuje na prostředek, který neexistuje.

Pomocí funkce [if](template-functions-logical.md#if) se ujistěte, že je funkce vyhodnocena pouze pro podmínky nasazení prostředku. Podívejte se na [funkci if](template-functions-logical.md#if) pro ukázkovou šablonu, která používá if a odkaz s podmíněně nasazeným prostředkem.

Prostředek nastavíte [jako závislý](define-resource-dependency.md) na podmíněném prostředku přesně tak, jako byste nastavili jakýkoli jiný prostředek. Když se podmíněný prostředek nenasadí, Azure Resource Manager ho automaticky odebere z požadovaných závislostí.

## <a name="condition-with-complete-mode"></a>Stav s kompletním režimem

Pokud nasadíte šablonu s [režimem dokončení](deployment-modes.md) a prostředek není nasazen, protože podmínka vyhodnotí na false, výsledek závisí na verzi rozhraní REST API, kterou používáte k nasazení šablony. Pokud používáte verzi starší než 2019-05-10, prostředek **se neodstraní**. S 2019-05-10 nebo novější, prostředek **je odstraněn**. Nejnovější verze Azure PowerShell a Azure CLI odstranit prostředek, pokud je podmínka false.

## <a name="next-steps"></a>Další kroky

* Doporučení týkající se vytváření šablon naleznete v doporučených [postupech pro šablony ARM](template-best-practices.md).
* Chcete-li vytvořit více instancí prostředku, [přečtěte si informace o iteraci prostředků v šablonách ARM](copy-resources.md).