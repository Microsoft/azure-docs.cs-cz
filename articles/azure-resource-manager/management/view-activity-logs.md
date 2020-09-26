---
title: Zobrazení protokolů aktivit Azure pro monitorování prostředků
description: Protokoly aktivit můžete použít ke kontrole akcí a chyb uživatele. Zobrazuje Azure Portal PowerShell, rozhraní příkazového řádku Azure a REST.
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: 14015e9b2792515e6818af551b8bd9f54c686bee
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91371588"
---
# <a name="view-activity-logs-to-monitor-actions-on-resources"></a>Zobrazení protokolů aktivit pro monitorování akcí u prostředků

Na základě protokolů aktivit můžete zjistit:

* jaké operace byly provedeny u prostředků ve vašem předplatném
* Kdo operaci zahájil
* Při výskytu operace
* stav operace
* hodnoty dalších vlastností, které vám mohou při průzkumu operace.

Protokol aktivit obsahuje všechny operace zápisu (PUT, POST, DELETE) pro vaše prostředky. Nezahrnuje operace čtení (GET). Seznam akcí prostředků najdete v tématu [operace poskytovatele prostředků Azure](../../role-based-access-control/resource-provider-operations.md). Protokoly aktivit můžete použít k vyhledání chyby při řešení potíží nebo k monitorování, jak uživatel ve vaší organizaci změnil prostředek.

Protokoly aktivit se uchovávají po dobu 90 dnů. Můžete se dotazovat na libovolný rozsah kalendářních dat, pokud počáteční datum neleží více než 90 dnů v minulosti.

Informace z protokolů aktivit můžete načíst prostřednictvím portálu, PowerShellu, Azure CLI, Insights REST API nebo [knihovny .NET Insights](https://www.nuget.org/packages/Microsoft.Azure.Insights/).

## <a name="azure-portal"></a>portál Azure

Pokud chcete zobrazit protokoly aktivit prostřednictvím portálu, postupujte podle těchto kroků:

1. V nabídce Azure Portal vyberte **monitorovat**, nebo vyhledejte a vyberte **monitorování** z libovolné stránky.

    ![Vybrat monitorování](./media/view-activity-logs/select-monitor-from-menu.png)

1. Vyberte **Protokol aktivit**.

    ![Vybrat protokol aktivit](./media/view-activity-logs/select-activity-log.png)

1. Zobrazí se souhrn posledních operací. Pro operace se použije výchozí sada filtrů. Všimněte si, že informace o souhrnu obsahují informace o tom, kdo akci zahájil a kdy k tomu došlo.

    ![Zobrazit souhrn nedávných operací](./media/view-activity-logs/audit-summary.png)

1. Chcete-li rychle spustit předem definovanou sadu filtrů, vyberte možnost **rychlé přehledy**.

    ![Vybrat rychlé přehledy](./media/view-activity-logs/select-quick-insights.png)

1. Vyberte jednu z možností. Pokud například chcete zobrazit chyby z nasazení, vyberte **neúspěšná nasazení** .

    ![Vybrat neúspěšná nasazení](./media/view-activity-logs/select-failed-deployments.png)

1. Všimněte si, že se filtry změnily tak, aby se v posledních 24 hodinách zaměřily na chyby nasazení. Zobrazí se pouze operace, které odpovídají filtrům.

    ![Zobrazení filtrů](./media/view-activity-logs/view-filters.png)

1. Pokud se chcete zaměřit na konkrétní operace, změňte filtry nebo použijte nové. Například následující obrázek ukazuje novou hodnotu pro **časový** rozsah a **typ prostředku** je nastaven na účty úložiště.

    ![Nastavit možnosti filtru](./media/view-activity-logs/set-filter.png)

1. Pokud budete potřebovat dotaz znovu spustit později, vyberte **připnout aktuální filtry**.

    ![Připnout filtry](./media/view-activity-logs/pin-filters.png)

1. Dejte filtru název.

    ![Filtry názvů](./media/view-activity-logs/name-filters.png)

1. Filtr je k dispozici na řídicím panelu. V nabídce webu Azure Portal vyberte **Řídicí panel**.

    ![Zobrazit filtr na řídicím panelu](./media/view-activity-logs/activity-log-on-dashboard.png)

1. Z portálu můžete zobrazit změny prostředku. Vraťte se do výchozího zobrazení v monitorování a vyberte operaci, která se zabývá změnou prostředku.

    ![Vybrat operaci](./media/view-activity-logs/select-operation.png)

1. Vyberte možnost **historie změn (Preview)** a vyberte jednu z dostupných operací.

    ![Vybrat historii změn](./media/view-activity-logs/select-change-history.png)

1. Zobrazí se změny v prostředku.

    ![Zobrazit změny](./media/view-activity-logs/show-changes.png)

Další informace o historii změn najdete v tématu [získání změn prostředků](../../governance/resource-graph/how-to/get-resource-changes.md).

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Chcete-li načíst položky protokolu, spusťte příkaz **Get-AzLog** . Zadáním dalších parametrů můžete filtrovat seznam položek. Pokud nezadáte čas začátku a konce, budou vráceny položky za posledních sedm dní.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup
```

Následující příklad ukazuje, jak používat protokol aktivit k výzkumu operací provedených během zadaného času. Počáteční a koncové datum jsou zadány ve formátu data.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime 2019-05-05T06:00 -EndTime 2019-05-09T06:00
```

Nebo můžete použít funkce date k určení rozsahu kalendářních dat, například posledních 14 dní.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14)
```

Můžete vyhledat akce provedené konkrétním uživatelem.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14) -Caller someone@contoso.com
```

Můžete filtrovat neúspěšné operace.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -Status Failed
```

Můžete se zaměřit na jednu chybu tím, že si prohlížíte stavovou zprávu pro tuto položku.

```azurepowershell-interactive
(Get-AzLog -ResourceGroup ExampleGroup -Status Failed).Properties.Content.statusMessage | ConvertFrom-Json
```

Můžete vybrat konkrétní hodnoty pro omezení vrácených dat.

```azurepowershell-interactive
Get-AzLog -ResourceGroupName ExampleGroup | Format-table EventTimeStamp, Caller, @{n='Operation'; e={$_.OperationName.value}}, @{n='Status'; e={$_.Status.value}}, @{n='SubStatus'; e={$_.SubStatus.LocalizedValue}}
```

V závislosti na počátečním čase, který zadáte, můžou předchozí příkazy vracet dlouhý seznam operací pro skupinu prostředků. Výsledky hledání můžete filtrovat podle toho, co hledáte. Například můžete filtrovat podle typu operace.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup | Where-Object {$_.OperationName.value -eq "Microsoft.Resources/deployments/write"}
```

Pomocí grafu prostředků můžete zobrazit historii změn pro určitý prostředek. Další informace najdete v tématu [získání změn prostředků](../../governance/resource-graph/how-to/get-resource-changes.md).

## <a name="azure-cli"></a>Azure CLI

Chcete-li načíst položky protokolu, spusťte pomocí příkazu [AZ monitor Activity-log list](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) s posunem, aby označoval časový rozsah.

```azurecli-interactive
az monitor activity-log list --resource-group ExampleGroup --offset 7d
```

Následující příklad ukazuje, jak používat protokol aktivit k výzkumu operací provedených během zadaného času. Počáteční a koncové datum jsou zadány ve formátu data.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --start-time 2019-05-01 --end-time 2019-05-15
```

Můžete vyhledat akce provedené konkrétním uživatelem, a to i pro skupinu prostředků, která už neexistuje.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --caller someone@contoso.com --offset 5d
```

Můžete filtrovat neúspěšné operace.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --status Failed --offset 1d
```

Můžete se zaměřit na jednu chybu tím, že si prohlížíte stavovou zprávu pro tuto položku.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --status Failed --offset 1d --query [].properties.statusMessage
```

Můžete vybrat konkrétní hodnoty pro omezení vrácených dat.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --offset 1d --query '[].{Operation: operationName.value, Status: status.value, SubStatus: subStatus.localizedValue}'
```

V závislosti na počátečním čase, který zadáte, můžou předchozí příkazy vracet dlouhý seznam operací pro skupinu prostředků. Výsledky hledání můžete filtrovat podle toho, co hledáte. Například můžete filtrovat podle typu operace.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --offset 1d --query "[?operationName.value=='Microsoft.Storage/storageAccounts/write']"
```

Pomocí grafu prostředků můžete zobrazit historii změn pro určitý prostředek. Další informace najdete v tématu [získání změn prostředků](../../governance/resource-graph/how-to/get-resource-changes.md).

## <a name="rest-api"></a>REST API

Operace REST pro práci s protokolem aktivit jsou součástí [rozhraní REST API služby Insights](/rest/api/monitor/). Pokud chcete načíst události z protokolu aktivit, navštivte stránku věnovanou [výpisu událostí správy v předplatném](/rest/api/monitor/activitylogs).

## <a name="next-steps"></a>Další kroky

* Protokoly aktivit Azure se dají použít s Power BI k získání dalších přehledů o akcích ve vašem předplatném. Podívejte se [na téma zobrazení a analýza protokolů aktivit Azure v Power BI a dalších](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/).
* Další informace o nastavení zásad zabezpečení najdete v tématu [řízení přístupu na základě role v Azure (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md).
* Pokud chcete zobrazit další podrobnosti o změnách vašich aplikací z infrastruktury infrastruktury všech způsobů nasazení aplikace, přečtěte si téma [použití analýzy změn aplikace v Azure monitor](../../azure-monitor/app/change-analysis.md).
* Další informace o příkazech pro zobrazení operací nasazení najdete v tématu [Zobrazení operací nasazení](../templates/deployment-history.md).
* Informace o tom, jak zabránit odstranění prostředků pro všechny uživatele, najdete v tématu [uzamčení prostředků pomocí Azure Resource Manager](lock-resources.md).
* Pokud chcete zobrazit seznam operací dostupných pro každého poskytovatele Microsoft Azure Správce prostředků, přečtěte si téma [operace poskytovatele prostředků Azure](../../role-based-access-control/resource-provider-operations.md) .
