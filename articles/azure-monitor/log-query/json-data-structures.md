---
title: Práce s řetězci v Azure Monitorch dotazech protokolu | Microsoft Docs
description: Tento článek popisuje kurz použití Azure Monitor Log Analytics v Azure Portal k dotazování a analýze dat protokolu v Azure Monitor.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: 940c82e9ef7016639a3ab334040c408f83996e2b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75365304"
---
# <a name="working-with-json-and-data-structures-in-azure-monitor-log-queries"></a>Práce s JSON a datovými strukturami v Azure Monitorch dotazech protokolu

> [!NOTE]
> Před dokončením této lekce byste měli dokončit [Začínáme s Azure Monitor Log Analytics](get-started-portal.md) a [Začínáme s dotazy protokolu Azure monitor](get-started-queries.md) .

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Vnořené objekty jsou objekty, které obsahují jiné objekty v poli nebo mapa párů klíč-hodnota. Tyto objekty jsou reprezentovány jako řetězce JSON. Tento článek popisuje, jak se pomocí formátu JSON načítají data a analyzují vnořené objekty.

## <a name="working-with-json-strings"></a>Práce s řetězci JSON
Pro přístup k určitému prvku JSON ve známé cestě použijte `extractjson`. Tato funkce vyžaduje výraz cesty, který používá následující konvence.

- _$_ pro odkazování na kořenovou složku
- Pomocí závorky nebo tečky můžete odkazovat na indexy a elementy, jak je znázorněno v následujících příkladech.


Rozdělte prvky pomocí hranatých závorek a teček:

```Kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report
| extend status = extractjson("$.hosts[0].status", hosts_report)
```

Jedná se o stejný výsledek s použitím pouze zápisu závorek:

```Kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report 
| extend status = extractjson("$['hosts'][0]['status']", hosts_report)
```

Pokud je k dispozici pouze jeden prvek, lze použít pouze zápis tečky:

```Kusto
let hosts_report='{"location":"North_DC", "status":"running", "rate":5}';
print hosts_report 
| extend status = hosts_report.status
```


## <a name="working-with-objects"></a>Práce s objekty

### <a name="parsejson"></a>parsejson
Chcete-li získat přístup k více prvkům ve struktuře JSON, je snazší k němu přistupovat jako dynamický objekt. Použijte `parsejson` k přetypování textových dat do dynamického objektu. Po převedení na dynamický typ lze pomocí dalších funkcí analyzovat data.

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend status0=hosts_object.hosts[0].status, rate1=hosts_object.hosts[1].rate
```



### <a name="arraylength"></a>arraylength
Pomocí `arraylength` spočítat počet prvků v poli:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend hosts_num=arraylength(hosts_object.hosts)
```

### <a name="mvexpand"></a>mvexpand
Použijte `mvexpand` k přerušení vlastností objektu na samostatné řádky.

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| mvexpand hosts_object.hosts[0]
```

![mvexpand](media/json-data-structures/mvexpand.png)

### <a name="buildschema"></a>buildschema
Pomocí `buildschema` získat schéma, které připouštějí všechny hodnoty objektu:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```

Výstup je schéma ve formátu JSON:
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
Tento výstup popisuje názvy polí objektů a jejich odpovídajících datových typů. 

Vnořené objekty mohou mít různá schémata jako v následujícím příkladu:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"status":"stopped", "rate":"3", "range":100}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```


![Schéma sestavení](media/json-data-structures/buildschema.png)

## <a name="next-steps"></a>Další kroky
Další lekce k používání dotazů protokolu v Azure Monitor:

- [Operace s řetězci](string-operations.md)
- [Operace s datem a časem](datetime-operations.md)
- [Agregační funkce](aggregations.md)
- [Pokročilé agregace](advanced-aggregations.md)
- [Pokročilý zápis dotazů](advanced-query-writing.md)
- [Starat](joins.md)
- [Spojnic](charts.md)
