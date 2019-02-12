---
title: Pokročilé dotazy ve službě Azure Azure Monitor | Dokumentace Microsoftu
description: Tento článek obsahuje kurz pro používání portálu Analytics psaní dotazů ve službě Azure Monitor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: bwren
ms.openlocfilehash: 4e5574fa5a615e43d94069a03716fa290b957342
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55993210"
---
# <a name="writing-advanced-queries-in-azure-monitor"></a>Zápis upřesňujících dotazů ve službě Azure Monitor

> [!NOTE]
> By se měla Dokončit [Začínáme se službou Azure Monitor log analytics](get-started-portal.md) a [Začínáme s dotazy](get-started-queries.md) před dokončením v této lekci.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

## <a name="reusing-code-with-let"></a>Opětovné použití kódu pomocí let
Použití `let` přiřadit výsledky k proměnné a si ji později v dotazu:

```Kusto
// get all events that have level 2 (indicates warning level)
let warning_events=
Event
| where EventLevel == 2;
// count the number of warning events per computer
warning_events
| summarize count() by Computer 
```

Můžete také přiřadit konstantní hodnoty pro proměnné. Tento atribut podporuje metodu nastavit parametry pro pole, která je třeba změnit pokaždé, když dotaz spustíte. Podle potřeby upravte tyto parametry. Chcete-li například vypočítat volné místo na disku a volné paměti (v percentil), v rámci daného časového intervalu:

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

To umožňuje snadno změnit počáteční čas ukončení při příštím spuštění dotazu.

### <a name="local-functions-and-parameters"></a>Parametry a lokální funkce
Použití `let` příkazy k vytvoření funkce, které lze použít ve stejném dotazu. Například Definujte funkci, která přijímá pole Datum a čas (ve formátu UTC) a převede ho na standardní formát USA. 

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

## <a name="print"></a>Vytisknout
`print` Vrátí tabulku s jedním sloupcem a jeden řádek, zobrazuje výsledek výpočtu. To se často používá v případech, kdy potřebujete jednoduché calcuation. Chcete-li například najít aktuální čas v PST a přidat sloupec s ESTU:

```Kusto
print nowPst = now()-8h
| extend nowEst = nowPst+3h
```

## <a name="datatable"></a>Objekt DataTable
`datatable` Umožňuje definovat sadu data. Zadejte schéma a sadu hodnot a potom přesměrujte tabulky do jiné elementy dotazu. Například k vytvoření tabulky využití paměti RAM a výpočet jejich průměrné hodnoty za hodinu:

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

Objekt DataTable konstrukce jsou velmi užitečné také při vytvoření vyhledávací tabulky. Například pro mapování tabulky dat, jako je například ID událostí z _SecurityEvent_ tabulky pro typy událostí uvedená jinde, vytvoření vyhledávací tabulky s typy událostí pomocí `datatable` a připojte se k tohoto objektu datatable s  _SecurityEvent_ dat:

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

## <a name="next-steps"></a>Další postup
Zobrazit další lekce pro použití [Průzkumník dat dotazovací jazyk](/azure/kusto/query/) službou Azure Monitor můžete vytvářet protokoly dat:

- [Operace s řetězci](string-operations.md)
- [Datum a čas operace](datetime-operations.md)
- [Agregační funkce](aggregations.md)
- [Pokročilé agregace](advanced-aggregations.md)
- [JSON a datových struktur](json-data-structures.md)
- [Spojení](joins.md)
- [Grafy](charts.md)