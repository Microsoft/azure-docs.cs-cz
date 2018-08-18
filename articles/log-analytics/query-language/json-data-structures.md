---
title: Práce s řetězci v dotazech Azure Log Analytics | Dokumentace Microsoftu
description: Tento článek obsahuje kurz pro používání portálu Analytics psát dotazy v Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/16/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: f027754f26a9063aa5faa548fd01576624811005
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/17/2018
ms.locfileid: "40190054"
---
# <a name="working-with-json-and-data-structures-in-log-analytics-queries"></a>Práce s formátem JSON a datovými struktury v dotazy Log Analytics

> [!NOTE]
> By se měla Dokončit [začít používat portál Analytics](get-started-analytics-portal.md) a [Začínáme s dotazy](get-started-queries.md) před dokončením v této lekci.


Vnořené objekty jsou objekty, které obsahují další objekty v poli, nebo mapu párů klíč hodnota. Tyto objekty jsou reprezentovány jako řetězce JSON. Tento článek popisuje, jak se používá JSON data načíst a analyzovat vnořené objekty.

## <a name="working-with-json-strings"></a>Práce s řetězci JSON
Použití `extractjson` pro přístup k konkrétní elementu JSON v cestě známé. Tato funkce vyžaduje výraz cesty, který používá následující konvence.

- _$_ k odkazování na kořenové složce
- Zápis závorky a tečku slouží k odkazování na indexy a prvky, jak je znázorněno v následujícím příkladu.


Použijte závorky pro indexy a tečky k oddělování elementů:

```OQL
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report
| extend status = extractjson("$.hosts[0].status", hosts_report)
```

Toto je stejný výsledek, pouze závorky zápisem pomocí:

```OQL
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report 
| extend status = extractjson("$['hosts'][0]['status']", hosts_report)
```

Pokud existuje pouze jeden element, můžete použít pouze zápisu s tečkou:

```OQL
let hosts_report='{"location":"North_DC", "status":"running", "rate":5}';
print hosts_report 
| extend status = hosts_report.status
```


## <a name="working-with-objects"></a>Práce s objekty

### <a name="parsejson"></a>parsejson
Pro přístup k více elementům ve struktuře json, bude jednodušší přístup jako dynamický objekt. Použití `parsejson` přetypovat textová data na dynamický objekt. Jakmile převést na dynamický typ, další funkce umožňuje analyzovat data.

```OQL
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend status0=hosts_object.hosts[0].status, rate1=hosts_object.hosts[1].rate
```



### <a name="arraylength"></a>arraylength
Použití `arraylength` ke zjištění počtu prvků v poli:

```OQL
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend hosts_num=arraylength(hosts_object.hosts)
```

### <a name="mvexpand"></a>mvexpand
Použití `mvexpand` na vlastnosti objektu rozdělit na samostatné řádky.

```OQL
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| mvexpand hosts_object.hosts[0]
```

![mvexpand](media/json-data-structures/mvexpand.png)

### <a name="buildschema"></a>buildschema
Použití `buildschema` GET pro schéma, které zavést všechny hodnoty objektu:

```OQL
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

```OQL
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"status":"stopped", "rate":"3", "range":100}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```


![Vytvoření schématu](media/json-data-structures/buildschema.png)

## <a name="next-steps"></a>Další postup
Zobrazit další lekce pro používání dotazovací jazyk Log Analytics:

- [Operace s řetězci](string-operations.md)
- [Datum a čas operace](datetime-operations.md)
- [Agregační funkce](aggregations.md)
- [Pokročilé agregace](advanced-aggregations.md)
- [Zápis rozšířeného dotazu](advanced-query-writing.md)
- [Spojení](joins.md)
- [Grafy](charts.md)