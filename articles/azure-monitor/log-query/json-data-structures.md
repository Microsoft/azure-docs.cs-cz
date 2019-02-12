---
title: Práce s řetězci v dotazech protokolu Azure Monitor | Dokumentace Microsoftu
description: Tento článek obsahuje kurz použití Azure Monitor log analytics na portálu Azure portal k dotazování a analýze dat protokolů ve službě Azure Monitor.
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
ms.date: 08/16/2018
ms.author: bwren
ms.openlocfilehash: dda7ef2e4e974ea053f030a3ebe71a16872612b0
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55993720"
---
# <a name="working-with-json-and-data-structures-in-azure-monitor-log-queries"></a>Práce s formátem JSON a datovými struktury v dotazů na protokoly Azure monitoru

> [!NOTE]
> By se měla Dokončit [Začínáme se službou Azure Monitor log analytics](get-started-portal.md) a [Začínáme se službou Azure Monitor protokolu dotazy](get-started-queries.md) před dokončením v této lekci.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Vnořené objekty jsou objekty, které obsahují další objekty v poli, nebo mapu párů klíč hodnota. Tyto objekty jsou reprezentovány jako řetězce JSON. Tento článek popisuje, jak se používá JSON data načíst a analyzovat vnořené objekty.

## <a name="working-with-json-strings"></a>Práce s řetězci JSON
Použití `extractjson` pro přístup k konkrétní elementu JSON v cestě známé. Tato funkce vyžaduje výraz cesty, který používá následující konvence.

- _$_ k odkazování na kořenové složce
- Zápis závorky a tečku slouží k odkazování na indexy a prvky, jak je znázorněno v následujícím příkladu.


Použijte závorky pro indexy a tečky k oddělování elementů:

```Kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report
| extend status = extractjson("$.hosts[0].status", hosts_report)
```

Toto je stejný výsledek, pouze závorky zápisem pomocí:

```Kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report 
| extend status = extractjson("$['hosts'][0]['status']", hosts_report)
```

Pokud existuje pouze jeden element, můžete použít pouze zápisu s tečkou:

```Kusto
let hosts_report='{"location":"North_DC", "status":"running", "rate":5}';
print hosts_report 
| extend status = hosts_report.status
```


## <a name="working-with-objects"></a>Práce s objekty

### <a name="parsejson"></a>parsejson
Pro přístup k více prvků ve struktuře json, bude jednodušší přístup jako dynamický objekt. Použití `parsejson` přetypovat textová data na dynamický objekt. Jakmile převést na dynamický typ, další funkce umožňuje analyzovat data.

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend status0=hosts_object.hosts[0].status, rate1=hosts_object.hosts[1].rate
```



### <a name="arraylength"></a>arraylength
Použití `arraylength` ke zjištění počtu prvků v poli:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend hosts_num=arraylength(hosts_object.hosts)
```

### <a name="mvexpand"></a>mvexpand
Použití `mvexpand` na vlastnosti objektu rozdělit na samostatné řádky.

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| mvexpand hosts_object.hosts[0]
```

![mvexpand](media/json-data-structures/mvexpand.png)

### <a name="buildschema"></a>buildschema
Použití `buildschema` GET pro schéma, které zavést všechny hodnoty objektu:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```

Výstup je schématu ve formátu JSON:
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
Tento výstup popisuje názvy pole objektů a jejich odpovídající datové typy. 

Vnořené objekty mohou mít různé schémata, jako v následujícím příkladu:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"status":"stopped", "rate":"3", "range":100}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```


![Vytvoření schématu](media/json-data-structures/buildschema.png)

## <a name="next-steps"></a>Další postup
Zobrazit další lekce pro použití protokolu dotazů ve službě Azure Monitor:

- [Operace s řetězci](string-operations.md)
- [Datum a čas operace](datetime-operations.md)
- [Agregační funkce](aggregations.md)
- [Pokročilé agregace](advanced-aggregations.md)
- [Zápis rozšířeného dotazu](advanced-query-writing.md)
- [Spojení](joins.md)
- [Grafy](charts.md)