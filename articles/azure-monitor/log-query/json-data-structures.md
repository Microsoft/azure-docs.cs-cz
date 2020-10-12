---
title: Práce s řetězci v Azure Monitorch dotazech protokolu | Microsoft Docs
description: Tento článek popisuje kurz použití Azure Monitor Log Analytics v Azure Portal k dotazování a analýze dat protokolu v Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: 5021b815da1419052ceb4d6114fbb2bc676fe8d6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90088395"
---
# <a name="working-with-json-and-data-structures-in-azure-monitor-log-queries"></a>Práce s JSON a datovými strukturami v Azure Monitorch dotazech protokolu

> [!NOTE]
> Před dokončením této lekce byste měli dokončit [Začínáme s Azure Monitor Log Analytics](get-started-portal.md) a [Začínáme s dotazy protokolu Azure monitor](get-started-queries.md) .

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Vnořené objekty jsou objekty, které obsahují jiné objekty v poli nebo mapa párů klíč-hodnota. Tyto objekty jsou reprezentovány jako řetězce JSON. Tento článek popisuje, jak se pomocí formátu JSON načítají data a analyzují vnořené objekty.

## <a name="working-with-json-strings"></a>Práce s řetězci JSON
Použijte `extractjson` pro přístup k určitému prvku JSON ve známé cestě. Tato funkce vyžaduje výraz cesty, který používá následující konvence.

- _$_ Postup pro odkazování na kořenovou složku
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
let hosts_report=dynamic({"location":"North_DC", "status":"running", "rate":5});
print hosts_report 
| extend status = hosts_report.status
```


## <a name="working-with-objects"></a>Práce s objekty

### <a name="parsejson"></a>parsejson
Chcete-li získat přístup k více prvkům ve struktuře JSON, je snazší k němu přistupovat jako dynamický objekt. Slouží `parsejson` k přetypování textových dat do dynamického objektu. Po převedení na dynamický typ lze pomocí dalších funkcí analyzovat data.

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend status0=hosts_object.hosts[0].status, rate1=hosts_object.hosts[1].rate
```



### <a name="arraylength"></a>arraylength
Slouží `arraylength` k počítání počtu prvků v poli:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend hosts_num=arraylength(hosts_object.hosts)
```

### <a name="mvexpand"></a>mvexpand
Slouží `mvexpand` k rozdělení vlastností objektu na samostatné řádky.

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| mvexpand hosts_object.hosts[0]
```

![Snímek obrazovky zobrazuje hosts_0 s hodnotami pro umístění, stav a rychlost.](media/json-data-structures/mvexpand.png)

### <a name="buildschema"></a>buildschema
Slouží `buildschema` k získání schématu, které připouštějí všechny hodnoty objektu:

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
- [Psaní rozšířených dotazů](advanced-query-writing.md)
- [Spojení](joins.md)
- [Grafy](charts.md)
