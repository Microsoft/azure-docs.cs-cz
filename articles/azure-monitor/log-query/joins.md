---
title: Spojení v Azure Monitor dotazy protokolu | Microsoft Docs
description: Tento článek obsahuje lekci o použití spojení v Azure Monitorch dotazech protokolu.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: 2dace6968fbbe69f806c27fb7a46e60c63f78b4f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "77670198"
---
# <a name="joins-in-azure-monitor-log-queries"></a>Spojení v Azure Monitor dotazy protokolu

> [!NOTE]
> Před dokončením této lekce byste měli dokončit [Začínáme s Azure Monitor Log Analytics](get-started-portal.md) a [dotazy protokolu Azure monitor](get-started-queries.md) .

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Spojení umožňují analyzovat data z více tabulek ve stejném dotazu. Sloučí řádky dvou datových sad tak, že odpovídají hodnotám zadaných sloupců.


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

V tomto příkladu se první datová sada filtruje pro všechny přihlašovací události. Je spojen s druhou datovou sadou, která filtruje všechny události odhlášení. Mezi plánované sloupce patří _Computer_, _account_, _TargetLogonId_a _TimeGenerated_. Datové sady jsou korelují sdíleným sloupcem, _TargetLogonId_. Výstupem je jeden záznam na korelační, který má jak přihlašovací, tak i čas odhlášení.

Pokud obě datové sady mají sloupce se stejnými názvy, budou mít sloupce datové sady na pravé straně číslo indexu, takže v tomto příkladu by se v tomto příkladu zobrazily _TargetLogonId_ s hodnotami z tabulky na levé straně a _TargetLogonId1_  s hodnotami z tabulky na pravé straně. V tomto případě byl druhý sloupec _TargetLogonId1_ odebraný pomocí `project-away` operátoru.

> [!NOTE]
> Chcete-li zvýšit výkon, zachovejte pouze příslušné sloupce připojených datových sad pomocí `project` operátoru.


Použijte následující syntaxi pro spojení dvou datových sad a připojeného klíče má jiný název mezi dvěma tabulkami:
```
Table1
| join ( Table2 ) 
on $left.key1 == $right.key2
```

## <a name="lookup-tables"></a>Vyhledávací tabulky
Běžné použití spojení používá statické mapování hodnot pomocí `datatable` , které může pomoci při transformaci výsledků do více předodesílaných způsobů. Například pro rozšíření dat události zabezpečení s názvem události pro každé ID události.

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

![Spojení s objektem DataTable](media/joins/dim-table.png)

## <a name="join-kinds"></a>Typy spojení
Zadejte typ spojení s argumentem _druh_ . Každý typ provádí odlišnou shodu mezi záznamy v daných tabulkách, jak je popsáno v následující tabulce.

| Typ spojení | Popis |
|:---|:---|
| innerunique | Toto je výchozí režim připojení. Nejprve jsou nalezeny hodnoty odpovídající sloupce v levé tabulce a duplicitní hodnoty budou odebrány.  Pak se sada jedinečných hodnot bude shodovat s pravou tabulkou. |
| vnořen | Ve výsledcích jsou zahrnuty pouze vyhovující záznamy v obou tabulkách. |
| leftouter | Všechny záznamy v levé tabulce a vyhovující záznamy v pravé tabulce jsou zahrnuty ve výsledcích. Nespárované výstupní vlastnosti obsahují hodnoty null.  |
| leftanti | Do výsledků jsou zahrnuty záznamy z levé strany, které nemají shodu s právem. Tabulka výsledků má pouze sloupce z levé tabulky. |
| leftsemi | Do výsledků jsou zahrnuty záznamy z levé strany, které mají shodu s právem. Tabulka výsledků má pouze sloupce z levé tabulky. |


## <a name="best-practices"></a>Osvědčené postupy

Pro zajištění optimálního výkonu Vezměte v úvahu následující body:

- V každé tabulce použijte časový filtr pro snížení záznamů, které je nutné pro spojení vyhodnotit.
- Pomocí `where` a `project` můžete snížit počet řádků a sloupců ve vstupních tabulkách před připojením.
- Je-li jedna tabulka vždy menší než druhá, použijte ji jako levou stranu spojení.


## <a name="next-steps"></a>Další kroky
Přečtěte si další lekce týkající se použití Azure Monitorch dotazů protokolu:

- [Operace s řetězci](string-operations.md)
- [Agregační funkce](aggregations.md)
- [Pokročilé agregace](advanced-aggregations.md)
- [JSON a datové struktury](json-data-structures.md)
- [Psaní rozšířených dotazů](advanced-query-writing.md)
- [Grafy](charts.md)
