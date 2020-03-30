---
title: Spojení v dotazech protokolu Azure Monitor | Dokumenty společnosti Microsoft
description: Tento článek obsahuje lekci o používání spojení v dotazech protokolu Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: 2dace6968fbbe69f806c27fb7a46e60c63f78b4f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670198"
---
# <a name="joins-in-azure-monitor-log-queries"></a>Spojení v dotazech protokolu Azure Monitor

> [!NOTE]
> Před dokončením této lekce byste měli dokončit Začínáme s dotazy [azure monitoru log analytics](get-started-portal.md) a [Azure Monitor protokolu.](get-started-queries.md)

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Spojení umožňují analyzovat data z více tabulek ve stejném dotazu. Slučují řádky dvou sad dat odpovídajícími hodnotami zadaných sloupců.


```Kusto
SecurityEvent 
| where EventID == 4624     // sign-in events
| project Computer, Account, TargetLogonId, LogonTime=TimeGenerated
| join kind= inner (
    SecurityEvent 
    | where EventID == 4634     // sign-out events
    | project TargetLogonId, LogoffTime=TimeGenerated
) on TargetLogonId
| extend Duration = LogoffTime-LogonTime
| project-away TargetLogonId1 
| top 10 by Duration desc
```

V tomto příkladu první datová sada filtruje pro všechny události přihlášení. To je spojen s druhou datovou sadou, která filtruje pro všechny události odhlášení. Promítané sloupce jsou _Computer_, _Account_, _TargetLogonId_a _TimeGenerated_. Datové sady jsou korelovány sdíleným sloupcem _TargetLogonId_. Výstup je jeden záznam na korelaci, který má čas přihlášení i odhlášení.

Pokud obě datové sady mají sloupce se stejnými názvy, sloupce datové sady na pravé straně by mít číslo indexu, takže v tomto příkladu výsledky by se zobrazí _TargetLogonId_ s hodnotami z levé straně tabulky a _TargetLogonId1_ s hodnotami z pravé tabulky. V tomto případě druhý _TargetLogonId1_ sloupec byl `project-away` odebrán pomocí operátoru.

> [!NOTE]
> Chcete-li zlepšit výkon, zachovat pouze příslušné sloupce spojených `project` datových sad, pomocí operátoru.


Pomocí následující syntaxe můžete spojit dvě datové sady a spojený klíč má mezi dvěma tabulkami jiný název:
```
Table1
| join ( Table2 ) 
on $left.key1 == $right.key2
```

## <a name="lookup-tables"></a>Vyhledávací tabulky
Běžné použití spojení používá statické mapování hodnot `datatable` pomocí, které mohou pomoci při transformaci výsledků do reprezentativnější způsobem. Chcete-li například obohatit data události zabezpečení o název události pro každé ID události.

```Kusto
let DimTable = datatable(EventID:int, eventName:string)
  [
    4625, "Account activity",
    4688, "Process action",
    4634, "Account activity",
    4658, "The handle to an object was closed",
    4656, "A handle to an object was requested",
    4690, "An attempt was made to duplicate a handle to an object",
    4663, "An attempt was made to access an object",
    5061, "Cryptographic operation",
    5058, "Key file operation"
  ];
SecurityEvent
| join kind = inner
 DimTable on EventID
| summarize count() by eventName
```

![Spojení s datovou tabulkou](media/joins/dim-table.png)

## <a name="join-kinds"></a>Připojit druhy
Zadejte typ spojení s _argumentem druhu._ Každý typ provádí jinou shodu mezi záznamy daných tabulek, jak je popsáno v následující tabulce.

| Typ spojení | Popis |
|:---|:---|
| innerunique | Toto je výchozí režim spojení. Nejprve se najdou hodnoty odpovídajícího sloupce v levé tabulce a budou odebrány duplicitní hodnoty.  Potom je sada jedinečných hodnot porovnána s pravou tabulkou. |
| Vnitřní | Ve výsledcích jsou zahrnuty pouze odpovídající záznamy v obou tabulkách. |
| leftouter | Všechny záznamy v levé tabulce a odpovídající záznamy v pravé tabulce jsou zahrnuty ve výsledcích. Neodpovídající výstupní vlastnosti obsahují nulls.  |
| leftanti | Záznamy z levé strany, které nemají shody zprava, jsou zahrnuty ve výsledcích. Tabulka výsledků obsahuje pouze sloupce z levé tabulky. |
| leftsemi | Záznamy z levé strany, které mají shody zprava, jsou zahrnuty ve výsledcích. Tabulka výsledků obsahuje pouze sloupce z levé tabulky. |


## <a name="best-practices"></a>Osvědčené postupy

Pro optimální výkon zvažte následující body:

- Pomocí časového filtru v každé tabulce můžete snížit počet záznamů, které musí být vyhodnoceny pro spojení.
- Použijte `where` `project` a snížit počet řádků a sloupců ve vstupních tabulkách před spojením.
- Pokud je jedna tabulka vždy menší než druhá, použijte ji jako levou stranu spojení.


## <a name="next-steps"></a>Další kroky
Podívejte se na další lekce pro používání dotazů protokolu Azure Monitor:

- [Operace s řetězci](string-operations.md)
- [Agregační funkce](aggregations.md)
- [Pokročilé agregace](advanced-aggregations.md)
- [JSON a datové struktury](json-data-structures.md)
- [Psaní rozšířených dotazů](advanced-query-writing.md)
- [Grafy](charts.md)
