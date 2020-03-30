---
title: Zobrazení protokolů aktivit Azure pro monitorování prostředků
description: Protokoly aktivit slouží ke kontrole akcí a chyb uživatelů. Zobrazuje Azure portal PowerShell, Azure CLI a REST.
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: 9511090099894fad6708843f106570ed029f0c1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75478133"
---
# <a name="view-activity-logs-to-monitor-actions-on-resources"></a>Zobrazení protokolů aktivit ke sledování akcí na zdrojích

Na základě protokolů aktivit můžete zjistit:

* jaké operace byly podniknuty s prostředky ve vašem předplatném
* kdo zahájil operaci
* v případě, že došlo k operaci
* stav operace
* hodnoty jiných vlastností, které vám mohou pomoci prozkoumat provoz

Protokol aktivit obsahuje všechny operace zápisu (PUT, POST, DELETE) pro vaše prostředky. Nezahrnuje operace čtení (GET). Seznam akcí prostředků najdete v tématu [Operace zprostředkovatele prostředků Azure Resource Manager](../../role-based-access-control/resource-provider-operations.md). Protokoly aktivit můžete použít k vyhledání chyby při řešení potíží nebo k monitorování, jak uživatel ve vaší organizaci změnil prostředek.

Protokoly aktivit se uchovávají po dobu 90 dnů. Můžete se dotazovat na libovolný rozsah kalendářních dat, pokud počáteční datum neleží více než 90 dnů v minulosti.

Informace z protokolů aktivit můžete načíst prostřednictvím portálu, Prostředí PowerShell, azure CLI, rozhraní API přehledů nebo [knihovny .NET .](https://www.nuget.org/packages/Microsoft.Azure.Insights/)

## <a name="azure-portal"></a>portál Azure

Chcete-li zobrazit protokoly aktivit prostřednictvím portálu, postupujte takto:

1. V nabídce portálu Azure vyberte **Monitor**nebo vyhledejte a vyberte **Monitor** na libovolné stránce.

    ![Vybrat monitor](./media/view-activity-logs/select-monitor-from-menu.png)

1. Vyberte **protokol aktivit**.

    ![Vybrat protokol aktivit](./media/view-activity-logs/select-activity-log.png)

1. Zobrazí se souhrn nedávných operací. Na operace se použije výchozí sada filtrů. Všimněte si, že informace o souhrnu zahrnují, kdo akci zahájil a kdy k ní došlo.

    ![Zobrazit souhrn posledních operací](./media/view-activity-logs/audit-summary.png)

1. Chcete-li rychle spustit předdefinovanou sadu filtrů, vyberte **rychlé přehledy**.

    ![Výběr rychlých přehledů](./media/view-activity-logs/select-quick-insights.png)

1. Vyberte jednu z možností. Chcete-li například zobrazit chyby z nasazení, vyberte **možnost Neúspěšná nasazení.**

    ![Vybrat neúspěšná nasazení](./media/view-activity-logs/select-failed-deployments.png)

1. Všimněte si, že filtry byly změněny tak, aby se zaměřily na chyby nasazení za posledních 24 hodin. Zobrazí se pouze operace, které odpovídají filtrům.

    ![Zobrazení filtrů](./media/view-activity-logs/view-filters.png)

1. Chcete-li se zaměřit na konkrétní operace, změňte filtry nebo použijte nové. Například následující obrázek ukazuje novou hodnotu pro **Timespan** a **typ prostředku** je nastavena na účty úložiště.

    ![Nastavení možností filtru](./media/view-activity-logs/set-filter.png)

1. Pokud budete dotaz potřebovat spustit později, vyberte **Připnout aktuální filtry**.

    ![Připnout filtry](./media/view-activity-logs/pin-filters.png)

1. Pojmenujte filtr.

    ![Filtry názvů](./media/view-activity-logs/name-filters.png)

1. Filtr je k dispozici na řídicím panelu. V nabídce webu Azure Portal vyberte **Řídicí panel**.

    ![Zobrazit filtr na řídicím panelu](./media/view-activity-logs/activity-log-on-dashboard.png)

1. Na portálu můžete zobrazit změny prostředku. Vraťte se do výchozího zobrazení v monitoru a vyberte operaci, která zahrnovala změnu prostředku.

    ![Vybrat operaci](./media/view-activity-logs/select-operation.png)

1. Vyberte **Změnit historii (náhled)** a vyberte jednu z dostupných operací.

    ![Vybrat historii změn](./media/view-activity-logs/select-change-history.png)

1. Zobrazí se změny v prostředku.

    ![Zobrazit změny](./media/view-activity-logs/show-changes.png)

Další informace o historii změn najdete v [tématu Získání změn zdrojů](../../governance/resource-graph/how-to/get-resource-changes.md).

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Chcete-li načíst položky protokolu, spusťte příkaz **Get-AzLog.** Zadáte další parametry pro filtrování seznamu položek. Pokud nezadáte čas zahájení a ukončení, budou vráceny položky za posledních sedm dní.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup
```

Následující příklad ukazuje, jak používat protokol aktivit k výzkumným operacím prováděným během zadaného času. Počáteční a koncové datum je určeno ve formátu data.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime 2019-05-05T06:00 -EndTime 2019-05-09T06:00
```

Nebo můžete použít funkce data k určení období, například posledních 14 dnů.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14)
```

Můžete vyhledat akce provedené určitým uživatelem.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14) -Caller someone@contoso.com
```

Můžete filtrovat neúspěšné operace.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -Status Failed
```

Můžete se zaměřit na jednu chybu pohledem na stavovou zprávu pro tuto položku.

```azurepowershell-interactive
(Get-AzLog -ResourceGroup ExampleGroup -Status Failed).Properties.Content.statusMessage | ConvertFrom-Json
```

Můžete vybrat konkrétní hodnoty pro omezení dat, která je vrácena.

```azurepowershell-interactive
Get-AzLog -ResourceGroupName ExampleGroup | Format-table EventTimeStamp, Caller, @{n='Operation'; e={$_.OperationName.value}}, @{n='Status'; e={$_.Status.value}}, @{n='SubStatus'; e={$_.SubStatus.LocalizedValue}}
```

V závislosti na zadaném počátečním čase mohou předchozí příkazy vrátit dlouhý seznam operací pro skupinu prostředků. Výsledky můžete filtrovat pro to, co hledáte, zadáním kritérií vyhledávání. Můžete například filtrovat podle typu operace.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup | Where-Object {$_.OperationName.value -eq "Microsoft.Resources/deployments/write"}
```

Pomocí grafu prostředků můžete zobrazit historii změn pro prostředek. Další informace naleznete v tématu [Získání změn prostředků](../../governance/resource-graph/how-to/get-resource-changes.md).

## <a name="azure-cli"></a>Azure CLI

Chcete-li načíst položky protokolu, spusťte příkaz [az monitor u seznamu aktivit az](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) s posunem, který označuje časový rozsah.

```azurecli-interactive
az monitor activity-log list --resource-group ExampleGroup --offset 7d
```

Následující příklad ukazuje, jak používat protokol aktivit k výzkumným operacím prováděným během zadaného času. Počáteční a koncové datum je určeno ve formátu data.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --start-time 2019-05-01 --end-time 2019-05-15
```

Můžete vyhledat akce provedené určitým uživatelem, a to i pro skupinu prostředků, která již neexistuje.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --caller someone@contoso.com --offset 5d
```

Můžete filtrovat neúspěšné operace.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --status Failed --offset 1d
```

Můžete se zaměřit na jednu chybu pohledem na stavovou zprávu pro tuto položku.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --status Failed --offset 1d --query [].properties.statusMessage
```

Můžete vybrat konkrétní hodnoty pro omezení dat, která je vrácena.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --offset 1d --query '[].{Operation: operationName.value, Status: status.value, SubStatus: subStatus.localizedValue}'
```

V závislosti na zadaném počátečním čase mohou předchozí příkazy vrátit dlouhý seznam operací pro skupinu prostředků. Výsledky můžete filtrovat pro to, co hledáte, zadáním kritérií vyhledávání. Můžete například filtrovat podle typu operace.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --offset 1d --query "[?operationName.value=='Microsoft.Storage/storageAccounts/write']"
```

Pomocí grafu prostředků můžete zobrazit historii změn pro prostředek. Další informace naleznete v tématu [Získání změn prostředků](../../governance/resource-graph/how-to/get-resource-changes.md).

## <a name="rest-api"></a>REST API

Operace REST pro práci s protokolem aktivit jsou součástí [rozhraní INSIGHTS REST API](/rest/api/monitor/). Informace o načtení událostí protokolu aktivit naleznete [v tématu Seznam událostí správy v předplatném](/rest/api/monitor/activitylogs).

## <a name="next-steps"></a>Další kroky

* Protokoly aktivit Azure se dají použít s Power BI, aby se získaly lepší přehledy o akcích ve vašem předplatném. Viz [Zobrazení a analýza protokolů aktivit Azure v Power BI a další .](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/)
* Další informace o nastavení zásad zabezpečení najdete v [tématu Řízení přístupu na základě rolí Azure](../../role-based-access-control/role-assignments-portal.md).
* Další podrobnosti o změnách vašich aplikací z vrstvy infrastruktury až po nasazení aplikace najdete v tématu [Použití analýzy změn aplikací v Azure Monitoru](../../azure-monitor/app/change-analysis.md).
* Informace o příkazech pro zobrazení operací nasazení naleznete v [tématu Zobrazení operací nasazení](../templates/deployment-history.md).
* Informace o tom, jak zabránit odstranění prostředků pro všechny uživatele, najdete v [tématu Uzamčení prostředků pomocí Správce prostředků Azure](lock-resources.md).
* Seznam operací dostupných pro každého poskytovatele Microsoft Azure Resource Manageru najdete v tématu [Operace zprostředkovatele prostředků Azure Správce prostředků](../../role-based-access-control/resource-provider-operations.md)
