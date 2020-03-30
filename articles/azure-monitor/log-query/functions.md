---
title: Funkce v dotazech protokolu Azure Monitor | Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak pomocí funkcí volat dotaz z jiného dotazu protokolu v Azure Monitoru.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/15/2018
ms.openlocfilehash: 7d94e53abbe8f4d2953729aa2363c3906ce94f74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670215"
---
# <a name="using-functions-in-azure-monitor-log-queries"></a>Použití funkcí v dotazech protokolu Azure Monitor

Chcete-li použít dotaz protokolu s jiným dotazem, můžete jej uložit jako funkci. To umožňuje zjednodušit složité dotazy rozdělením na části a umožňuje znovu použít společný kód s více dotazy.

## <a name="create-a-function"></a>Vytvoření funkce

Vytvořte funkci s Log Analytics na webu Azure Portal kliknutím na **Uložit** a pak zadejte informace v následující tabulce.

| Nastavení | Popis |
|:---|:---|
| Name (Název)           | Zobrazovaný název dotazu v **průzkumníku dotazu**. |
| Uložit jako        | Funkce |
| Alias funkce | Krátký název pro použití funkce v jiných dotazech. Nesmí obsahovat mezery a musí být jedinečné. |
| Kategorie       | Kategorie pro uspořádání uložených dotazů a funkcí v **Průzkumníku dotazů**. |

> [!NOTE]
> Funkce ve službě Azure Monitor nemůže obsahovat jinou funkci.




## <a name="use-a-function"></a>Použití funkce
Použijte funkci zahrnutím jejího aliasu do jiného dotazu. Může být použit jako jakýkoli jiný stůl.

## <a name="example"></a>Příklad
Následující ukázkový dotaz vrátí všechny chybějící aktualizace zabezpečení hlášené za poslední den. Uložte tento dotaz jako funkci s aliasem _security_updates_last_day_. 

```Kusto
Update
| where TimeGenerated > ago(1d) 
| where Classification == "Security Updates" 
| where UpdateState == "Needed"
```

Vytvořte jiný dotaz a odkazte na funkci _security_updates_last_day_ a vyhledejte potřebné aktualizace zabezpečení související s SQL.

```Kusto
security_updates_last_day | where Title contains "SQL"
```

## <a name="next-steps"></a>Další kroky
Podívejte se na další lekce pro psaní dotazů protokolu Azure Monitor:

- [Operace s řetězci](string-operations.md)
- [Operace s datem a časem](datetime-operations.md)
- [Agregační funkce](aggregations.md)
- [Pokročilé agregace](advanced-aggregations.md)
- [JSON a datové struktury](json-data-structures.md)
- [Spojení](joins.md)
- [Grafy](charts.md)
