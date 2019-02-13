---
title: Zobrazení protokolů aktivit Azure pro monitorování prostředků | Dokumentace Microsoftu
description: Použití protokolů aktivit do akce kontroly uživatelů a chyby. Zobrazuje portálového prostředí Azure PowerShell, rozhraní příkazového řádku Azure a REST.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
ms.assetid: fcdb3125-13ce-4c3b-9087-f514c5e41e73
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: tomfitz
ms.openlocfilehash: 0bc53edb00ea13fdfcab3cfc398e122e6ecd7632
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56106320"
---
# <a name="view-activity-logs-to-audit-actions-on-resources"></a>Zobrazení protokolů aktivit pro auditování akcí u prostředků

Na základě protokolů aktivit můžete zjistit:

* jaké operace provedené na prostředky v rámci vašeho předplatného
* kdo spustit operaci
* Při operaci došlo k chybě
* Stav operace
* Hodnoty dalších vlastností, které vám mohou pomoci při zkoumání operace

Protokol aktivit obsahuje všechny operace zápisu (PUT, POST, DELETE) prováděné s vašimi prostředky. Neobsahuje operace čtení (GET). Seznam prostředků akcí najdete v tématu [operace poskytovatele prostředků Azure Resource Manageru](../role-based-access-control/resource-provider-operations.md). Protokoly auditu můžete použít k vyhledání chyby při odstraňování potíží nebo k monitorování, jak uživatel ve vaší organizaci změnil prostředek.

Protokoly aktivit se uchovávají po dobu 90 dnů. Můžete se dotazovat na libovolný rozsah kalendářních dat, jako počáteční datum není více než 90 dnů v minulosti.

Můžete načíst informace z protokolů aktivit na portálu, Powershellu, rozhraní příkazového řádku Azure Insights REST API, nebo [knihovna .NET Insights](https://www.nuget.org/packages/Microsoft.Azure.Insights/).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="the-azure-portal"></a>Azure Portal

1. Pokud chcete zobrazit protokoly aktivit na portálu, vyberte **monitorování**.

    ![Vyberte monitorování](./media/resource-group-audit/select-monitor.png)

1. Vyberte **protokolu aktivit**.

    ![Vyberte protokol aktivit](./media/resource-group-audit/select-activity-log.png)

1. Zobrazí souhrn posledních operací. Výchozí sada filtry platí pro operace.

    ![Zobrazit souhrn, poslední operací](./media/resource-group-audit/audit-summary.png)

1. K rychlému spouštění předem definovanou sadu filtrů, vyberte **rychlých přehledů** a vyberte jednu z možností.

    ![Výběr dotazu](./media/resource-group-audit/quick-insights.png)

1. Zaměřte se na určité operace, změňte filtry nebo použít nové značky. Například následující obrázek ukazuje novou hodnotu **Timespan** a **typ prostředku** je nastavena na účty úložiště. 

    ![nastavení filtru](./media/resource-group-audit/set-filter.png)

1. Pokud je potřeba znovu spustit dotaz, vyberte **připnout aktuální filtry**.

    ![Filtry PIN kódu](./media/resource-group-audit/pin-filters.png)

1. Zadejte název filtru.

    ![Filtry názvů](./media/resource-group-audit/name-filters.png)

1. Filtr je dostupný na řídicím panelu.

    ![Zobrazit na řídicím panelu filtru](./media/resource-group-audit/show-dashboard.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Pokud chcete načíst položky protokolu, spusťte **Get-AzLog** příkazu. Další parametry pro filtrování seznamu položek, které zadáte. Pokud nechcete zadat počáteční a koncový čas, jsou vráceny položky za posledních sedm dní.

  ```azurepowershell-interactive
  Get-AzLog -ResourceGroup ExampleGroup
  ```

    Následující příklad ukazuje způsob použití protokolu aktivit do výzkumu operací provedených v určitou dobu. Počáteční a koncové datum jsou zadány ve formátu data.

  ```azurepowershell-interactive
  Get-AzLog -ResourceGroup ExampleGroup -StartTime 2019-01-09T06:00 -EndTime 2019-01-15T06:00
  ```

    Nebo datové funkce můžete zadat rozsah dat, jako je například posledních 14 dní.

  ```azurepowershell-interactive
  Get-AzLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14)
  ```

* Můžete vyhledat akce prováděné konkrétní uživatel, dokonce i pro skupinu prostředků, která již existuje.

  ```azurepowershell-interactive
  Get-AzLog -ResourceGroup deletedgroup -StartTime (Get-Date).AddDays(-14) -Caller someone@contoso.com
  ```

* Můžete filtrovat pro neúspěšné operace.

  ```azurepowershell-interactive
  Get-AzLog -ResourceGroup ExampleGroup -Status Failed
  ```

* Pohledem na stavové zprávy pro tuto položku se můžete soustředit na jednu chybu.

  ```azurepowershell-interactive
  ((Get-AzLog -ResourceGroup ExampleGroup -Status Failed).Properties[0].Content.statusMessage | ConvertFrom-Json).error
  ```

* Můžete vybrat konkrétní hodnoty omezit data, která je vrácena.

  ```azurepowershell-interactive
  Get-AzLog -ResourceGroupName ExampleGroup | Format-table EventTimeStamp, Caller, @{n='Operation'; e={$_.OperationName.value}}, @{n='Status'; e={$_.Status.value}}, @{n='SubStatus'; e={$_.SubStatus.LocalizedValue}}
  ```

* V závislosti na čas spuštění, který zadáte může vrátit předchozí příkazy dlouhý seznam operací pro skupinu prostředků. Můžete filtrovat výsledky pro co jste hledali zadáním kritérií hledání. Například můžete filtrovat podle typu operace.

  ```azurepowershell-interactive
  Get-AzLog -ResourceGroup ExampleGroup | Where-Object {$_.OperationName.value -eq "Microsoft.Resources/deployments/write"}
  ```

## <a name="azure-cli"></a>Azure CLI

* Pokud chcete načíst položky protokolu, spusťte [az monitor protokolu aktivit seznamu](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) příkaz posun k označení časové období.

  ```azurecli-interactive
  az monitor activity-log list --resource-group ExampleGroup --offset 7d
  ```

  Následující příklad ukazuje způsob použití protokolu aktivit do výzkumu operací provedených v určitou dobu. Počáteční a koncové datum jsou zadány ve formátu data.

  ```azurecli-interactive
  az monitor activity-log list -g ExampleGroup --start-time 2019-01-01 --end-time 2019-01-15
  ```

* Můžete vyhledat akce prováděné konkrétní uživatel, dokonce i pro skupinu prostředků, která již existuje.

  ```azurecli-interactive
  az monitor activity-log list -g ExampleGroup --caller someone@contoso.com --offset 5d
  ```

* Můžete filtrovat pro neúspěšné operace.

  ```azurecli-interactive
  az monitor activity-log list -g demoRG --status Failed --offset 1d
  ```

* Pohledem na stavové zprávy pro tuto položku se můžete soustředit na jednu chybu.

  ```azurecli-interactive
  az monitor activity-log list -g ExampleGroup --status Failed --offset 1d --query [].properties.statusMessage
  ```

* Můžete vybrat konkrétní hodnoty omezit data, která je vrácena.

  ```azurecli-interactive
  az monitor activity-log list -g ExampleGroup --offset 1d --query '[].{Operation: operationName.value, Status: status.value, SubStatus: subStatus.localizedValue}'
  ```

* V závislosti na čas spuštění, který zadáte může vrátit předchozí příkazy dlouhý seznam operací pro skupinu prostředků. Můžete filtrovat výsledky pro co jste hledali zadáním kritérií hledání. Například můžete filtrovat podle typu operace.

  ```azurecli-interactive
  az monitor activity-log list -g ExampleGroup --offset 1d --query "[?operationName.value=='Microsoft.Storage/storageAccounts/write']"
  ```

## <a name="rest-api"></a>REST API

Operace REST pro práci s protokolu aktivit jsou součástí [rozhraní REST API služby Insights](https://msdn.microsoft.com/library/azure/dn931943.aspx). Pokud chcete načíst události protokolu aktivit, naleznete v tématu [seznamu událostí správy v rámci předplatného](https://msdn.microsoft.com/library/azure/dn931934.aspx).

## <a name="next-steps"></a>Další postup

* Protokoly aktivit Azure je možné s Power BI a získejte větší přehled o akcích ve vašem předplatném. Zobrazit [zobrazení a analýza protokolů aktivit Azure v Power BI a další](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/).
* Další informace o nastavení zásad zabezpečení najdete v tématu [řízení přístupu na základě Role v Azure](../role-based-access-control/role-assignments-portal.md).
* Další informace o příkazech pro zobrazení operací nasazení, najdete v článku [zobrazení operací nasazení](resource-manager-deployment-operations.md).
* Naučte se, abyste zabránili odstranění prostředku pro všechny uživatele, najdete v článku [zamknutí prostředků pomocí Azure Resource Manageru](resource-group-lock-resources.md).
* Pokud chcete zobrazit seznam operací dostupných pro každého zprostředkovatele Microsoft Azure Resource Manageru, najdete v článku [operace poskytovatele prostředků Azure Resource Manageru](../role-based-access-control/resource-provider-operations.md)
