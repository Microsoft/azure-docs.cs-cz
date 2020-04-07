---
title: Práce s řetězci v dotazech protokolu Azure Monitor | Dokumenty společnosti Microsoft
description: Tento článek obsahuje kurz pro použití Azure Monitor Log Analytics na webu Azure Portal k dotazování a analýze dat protokolu v Azure Monitoru.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: f792820b7b0dff20e647031410ba87ac26c2495a
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80672977"
---
# <a name="working-with-json-and-data-structures-in-azure-monitor-log-queries"></a>Práce s JSON a datovými strukturami v dotazech protokolu Azure Monitor

> [!NOTE]
> Před dokončením této lekce byste měli dokončit [Začínáme s Azure Monitor Log Analytics](get-started-portal.md) a [Začínáme s dotazy protokolu Azure Monitor.](get-started-queries.md)

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Vnořené objekty jsou objekty, které obsahují jiné objekty v poli nebo mapu párů klíč-hodnota. Tyto objekty jsou reprezentovány jako řetězce JSON. Tento článek popisuje, jak json se používá k načtení dat a analyzovat vnořené objekty.

## <a name="working-with-json-strings"></a>Práce s řetězci JSON
Slouží `extractjson` k přístupu k určitému prvku JSON ve známé cestě. Tato funkce vyžaduje výraz cesty, který používá následující konvence.

- _$_ odkazovat na kořenovou složku
- Pomocí zápisu závorky nebo tečky můžete odkazovat na indexy a prvky, jak je znázorněno v následujících příkladech.


Pomocí závorek pro indexy a tečky oddělte prvky:

```Kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report
| extend status = extractjson("$.hosts[0].status", hosts_report)
```

To je stejný výsledek pouze pomocí zápisu závorek:

```Kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report 
| extend status = extractjson("$['hosts'][0]['status']", hosts_report)
```

Pokud existuje pouze jeden prvek, můžete použít pouze tečka zápis:

```Kusto
let hosts_report=dynamic({"location":"North_DC", "status":"running", "rate":5});
print hosts_report 
| extend status = hosts_report.status
```


## <a name="working-with-objects"></a>Práce s objekty

### <a name="parsejson"></a>parsejson
Přístup k více prvkům ve struktuře json je snadnější přístup jako dynamický objekt. Slouží `parsejson` k přetypovat textová data na dynamický objekt. Po převodu na dynamický typ lze k analýze dat použít další funkce.

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend status0=hosts_object.hosts[0].status, rate1=hosts_object.hosts[1].rate
```



### <a name="arraylength"></a>délka pole
Slouží `arraylength` ke spočítání počtu prvků v poli:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend hosts_num=arraylength(hosts_object.hosts)
```

### <a name="mvexpand"></a>mvexpand
Slouží `mvexpand` k rozdělení vlastností objektu do samostatných řádků.

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| mvexpand hosts_object.hosts[0]
```

![mvexpand](media/json-data-structures/mvexpand.png)

### <a name="buildschema"></a>buildschema
Slouží `buildschema` k získání schématu, které přijímá všechny hodnoty objektu:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```

Výstupje schéma ve formátu JSON:
```json
{
    "hosts":
    {
        "indexer":
        {
            "location": "string",
            "rate": "int",
            "status": "string"
        }
    }
}
```
Tento výstup popisuje názvy polí objektů a jejich odpovídající datové typy. 

Vnořené objekty mohou mít různá schémata, například v následujícím příkladu:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"status":"stopped", "rate":"3", "range":100}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```


![Sestavení schématu](media/json-data-structures/buildschema.png)

## <a name="next-steps"></a>Další kroky
Podívejte se na další lekce pro používání dotazů protokolu v Azure Monitoru:

- [Operace s řetězci](string-operations.md)
- [Operace s datem a časem](datetime-operations.md)
- [Agregační funkce](aggregations.md)
- [Pokročilé agregace](advanced-aggregations.md)
- [Psaní rozšířených dotazů](advanced-query-writing.md)
- [Spojení](joins.md)
- [Grafy](charts.md)
