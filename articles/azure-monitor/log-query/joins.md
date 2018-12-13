---
title: Spojení v Azure Log Analytics dotazů | Dokumentace Microsoftu
description: Tento článek obsahuje lekce týkající se použití spojení ve dotazovací jazyk Log Analytics.
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
ms.openlocfilehash: c1f578c11fff963ee4bec47bd3737cd224b14720
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52882317"
---
# <a name="joins-in-log-analytics-queries"></a>Spojení v dotazy Log Analytics

> [!NOTE]
> By se měla Dokončit [začít používat portál Analytics](get-started-portal.md) a [Začínáme s dotazy](get-started-queries.md) před dokončením v této lekci.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Spojení umožňují analyzovat data z více tabulek, ve stejném dotazu. Sloučí řádky dvou datových sad porovnáním hodnot zadaných sloupců.


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

V tomto příkladu první datovou sadu filtrů pro všechny události přihlášení. To je spojen s druhou datovou sadu, která filtruje všech odhlašování přes protokol událostí. Předpokládané sloupce jsou _počítače_, _účet_, _TargetLogonId_, a _TimeGenerated_. Datové sady se korelují podle sdílené sloupce _TargetLogonId_. Výstup je jeden záznam za korelace, které mají čas přihlášení a odhlášení.

Pokud obě datové sady sloupců se stejnými názvy, sloupce datové sady, na pravé straně by předávat číslo indexu, takže v tomto příkladu by zobrazil výsledky _TargetLogonId_ s hodnotami z levé tabulky a  _TargetLogonId1_ s hodnotami z tabulky na pravé straně. V takovém případě druhý _TargetLogonId1_ sloupec byl odebrán pomocí `project-away` operátor.

> [!NOTE]
> Kvůli zvýšení výkonu se zachovat relevantní sloupce připojené k doméně – sady dat, pomocí `project` operátor.


Použijte následující syntax k dvě datové sady a připojené k doméně klíč má jiný název mezi dvěma tabulkami:
```
Table1
| join ( Table2 ) 
on $left.key1 == $right.key2
```

## <a name="lookup-tables"></a>Vyhledávací tabulky
Běžné použití spojení je pomocí statické mapování hodnot pomocí `datatable` , které mohou pomoci při transformaci výsledky do více prezentovatelný způsobem. Například rozšířit zabezpečení dat událostí s názvem události pro každou jednotlivou událost ID.

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

![Seznamte se s datatable](media/joins/dim-table.png)

## <a name="join-kinds"></a>Připojte se k typy
Zadejte typ spojení s _druh_ argument. Každý typ provádí různé shodné záznamy z dané tabulky, jak je popsáno v následující tabulce.

| Typ spojení | Popis |
|:---|:---|
| innerunique | Toto je výchozí režim join. Nejprve jsou nalezeny hodnoty odpovídající sloupce v levé tabulce a duplicitní hodnoty se odeberou.  Pak sadu jedinečné hodnoty hledána pravé tabulky. |
| vnitřní | Pouze odpovídající záznamy v obou tabulkách jsou zahrnuty ve výsledcích. |
| leftouter | Všechny záznamy v levé tabulce a odpovídající záznamy v pravé tabulce jsou zahrnuty ve výsledcích. Bezkonkurenční výstupní vlastnosti obsahovat hodnoty Null.  |
| leftanti | Záznamy z levé strany, které nemají shody zprava, jsou zahrnuty ve výsledcích. Tabulka výsledků obsahuje pouze sloupce z levé tabulky. |
| leftsemi | Záznamy z levé strany, jejichž shody zprava, jsou zahrnuty ve výsledcích. Tabulka výsledků obsahuje pouze sloupce z levé tabulky. |


## <a name="best-practices"></a>Osvědčené postupy

Vezměte v úvahu následující body pro zajištění optimálního výkonu:

- Použijte filtr času ke snížení záznamy, které musí být vyhodnocen pro spojení na každou tabulku.
- Použití `where` a `project` ke snížení počtu řádků a sloupců ve vstupní tabulky před spojení.
* Pokud jedna tabulka je vždy menší než ten druhý, použijte ho jako levé strany spojení.


## <a name="next-steps"></a>Další postup
Zobrazit další lekce pro používání dotazovací jazyk Log Analytics:

- [Operace s řetězci](string-operations.md)
- [Agregační funkce](aggregations.md)
- [Pokročilé agregace](advanced-aggregations.md)
- [JSON a datových struktur](json-data-structures.md)
- [Zápis rozšířeného dotazu](advanced-query-writing.md)
- [Grafy](charts.md)