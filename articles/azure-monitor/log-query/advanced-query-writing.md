---
title: Pokročilé dotazy ve službě Azure Monitor | Dokumenty společnosti Microsoft
description: Tento článek obsahuje kurz pro použití portálu Analytics k psaní dotazů v Azure Monitoru.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/15/2018
ms.openlocfilehash: 3d228c62cd2d1bcb7f4515cd698186e2ebcbe929
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670283"
---
# <a name="writing-advanced-queries-in-azure-monitor"></a>Psaní pokročilých dotazů ve službě Azure Monitor

> [!NOTE]
> Před dokončením této lekce byste měli dokončit [Začínáme s Azure Monitor Log Analytics](get-started-portal.md) a [Začínáme s dotazy.](get-started-queries.md)

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

## <a name="reusing-code-with-let"></a>Opětovné použití kódu pomocí funkce let
Slouží `let` k přiřazení výsledků proměnné a odkazovat na něj dále v dotazu:

```Kusto
// get all events that have level 2 (indicates warning level)
let warning_events=
Event
| where EventLevel == 2;
// count the number of warning events per computer
warning_events
| summarize count() by Computer 
```

Proměnným můžete také přiřadit konstantní hodnoty. To podporuje metodu nastavení parametrů pro pole, která je třeba změnit při každém spuštění dotazu. Upravte tyto parametry podle potřeby. Chcete-li například vypočítat volné místo na disku a volnou paměť (v percentilech), v daném časovém okně:

```Kusto
let startDate = datetime(2018-08-01T12:55:02);
let endDate = datetime(2018-08-02T13:21:35);
let FreeDiskSpace =
Perf
| where TimeGenerated between (startDate .. endDate)
| where ObjectName=="Logical Disk" and CounterName=="Free Megabytes"
| summarize percentiles(CounterValue, 50, 75, 90, 99);
let FreeMemory =
Perf
| where TimeGenerated between (startDate .. endDate)
| where ObjectName=="Memory" and CounterName=="Available MBytes Memory"
| summarize percentiles(CounterValue, 50, 75, 90, 99);
union FreeDiskSpace, FreeMemory
```

To usnadňuje změnu času začátku ukončení při příštím spuštění dotazu.

### <a name="local-functions-and-parameters"></a>Místní funkce a parametry
Příkazy slouží `let` k vytvoření funkcí, které lze použít ve stejném dotazu. Například definujte funkci, která přebírá pole datetime (ve formátu UTC) a převede ji do standardního formátu USA. 

```Kusto
let utc_to_us_date_format = (t:datetime)
{
  strcat(getmonth(t), "/", dayofmonth(t),"/", getyear(t), " ",
  bin((t-1h)%12h+1h,1s), iff(t%24h<12h, "AM", "PM"))
};
Event 
| where TimeGenerated > ago(1h) 
| extend USTimeGenerated = utc_to_us_date_format(TimeGenerated)
| project TimeGenerated, USTimeGenerated, Source, Computer, EventLevel, EventData 
```

## <a name="print"></a>Tisk
`print`vrátí tabulku s jedním sloupcem a jedním řádkem, který zobrazuje výsledek výpočtu. To se často používá v případech, kdy potřebujete jednoduchý výpočet. Chcete-li například najít aktuální čas v pst a přidat sloupec s EST:

```Kusto
print nowPst = now()-8h
| extend nowEst = nowPst+3h
```

## <a name="datatable"></a>Datatable
`datatable`umožňuje definovat sadu dat. Zadáte schéma a sadu hodnot a potom potrubí tabulka do jiných prvků dotazu. Chcete-li například vytvořit tabulku využití paměti RAM a vypočítat jejich průměrnou hodnotu za hodinu:

```Kusto
datatable (TimeGenerated: datetime, usage_percent: double)
[
  "2018-06-02T15:15:46.3418323Z", 15.5,
  "2018-06-02T15:45:43.1561235Z", 20.2,
  "2018-06-02T16:16:49.2354895Z", 17.3,
  "2018-06-02T16:46:44.9813459Z", 45.7,
  "2018-06-02T17:15:41.7895423Z", 10.9,
  "2018-06-02T17:44:23.9813459Z", 24.7,
  "2018-06-02T18:14:59.7283023Z", 22.3,
  "2018-06-02T18:45:12.1895483Z", 25.4
]
| summarize avg(usage_percent) by bin(TimeGenerated, 1h)
```

Datatable konstrukce jsou také velmi užitečné při vytváření vyhledávací tabulky. Chcete-li například mapovat data tabulky, jako jsou ID událostí z tabulky _SecurityEvent,_ na typy `datatable` událostí uvedené jinde, vytvořte vyhledávací tabulku s typy událostí pomocí a připojte tuto datovou tabulku s daty _SecurityEvent:_

```Kusto
let eventCodes = datatable (EventID: int, EventType:string)
[
    4625, "Account activity",
    4688, "Process action",
    4634, "Account activity",
    4672, "Access",
    4624, "Account activity",
    4799, "Access management",
    4798, "Access management",
    5059, "Key operation",
    4648, "A logon was attempted using explicit credentials",
    4768, "Access management",
    4662, "Other",
    8002, "Process action",
    4904, "Security event management",
    4905, "Security event management",
];
SecurityEvent
| where TimeGenerated > ago(1h) 
| join kind=leftouter (
  eventCodes
) on EventID
| project TimeGenerated, Account, AccountType, Computer, EventType
```

## <a name="next-steps"></a>Další kroky
Podívejte se na další lekce pro používání [dotazovacího jazyka Kusto](/azure/kusto/query/) s daty protokolu Azure Monitor:

- [Operace s řetězci](string-operations.md)
- [Operace s datem a časem](datetime-operations.md)
- [Agregační funkce](aggregations.md)
- [Pokročilé agregace](advanced-aggregations.md)
- [JSON a datové struktury](json-data-structures.md)
- [Spojení](joins.md)
- [Grafy](charts.md)
