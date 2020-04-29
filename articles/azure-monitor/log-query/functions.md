---
title: Funkce v Azure Monitorch dotazech protokolu | Microsoft Docs
description: Tento článek popisuje, jak pomocí funkce volat dotaz z jiného dotazu protokolu v Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/15/2018
ms.openlocfilehash: 7d94e53abbe8f4d2953729aa2363c3906ce94f74
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77670215"
---
# <a name="using-functions-in-azure-monitor-log-queries"></a>Použití funkcí v Azure Monitorch dotazech protokolu

Pokud chcete použít dotaz protokolu s jiným dotazem, můžete ho uložit jako funkci. To vám umožní zjednodušit složité dotazy tím, že je rozdělíte do částí a umožníte znovu použít společný kód s více dotazy.

## <a name="create-a-function"></a>Vytvoření funkce

Vytvořte funkci s Log Analytics v Azure Portal kliknutím na **Uložit** a zadáním informací v následující tabulce.

| Nastavení | Popis |
|:---|:---|
| Název           | Zobrazovaný název dotazu v **Průzkumníku dotazů** |
| Uložit jako        | Funkce |
| Alias funkce | Krátký název, který bude používat funkci v jiných dotazech. Nesmí obsahovat mezery a musí být jedinečný. |
| Kategorie       | Kategorie pro uspořádání uložených dotazů a funkcí v **Průzkumníku dotazů**. |

> [!NOTE]
> Funkce v Azure Monitor nemůže obsahovat jinou funkci.




## <a name="use-a-function"></a>Použití funkce
Použijte funkci zahrnutím jejího aliasu do jiného dotazu. Dá se použít jako jakákoli jiná tabulka.

## <a name="example"></a>Příklad
Následující vzorový dotaz vrátí všechny chybějící aktualizace zabezpečení hlášené za poslední den. Uložte tento dotaz jako funkci s aliasem _security_updates_last_day_. 

```Kusto
Update
| where TimeGenerated > ago(1d) 
| where Classification == "Security Updates" 
| where UpdateState == "Needed"
```

Vytvořte další dotaz a odkaz na funkci _security_updates_last_day_ a vyhledejte aktualizace zabezpečení potřebné pro SQL.

```Kusto
security_updates_last_day | where Title contains "SQL"
```

## <a name="next-steps"></a>Další kroky
Přečtěte si další lekce pro zápis Azure Monitorch dotazů protokolu:

- [Operace s řetězci](string-operations.md)
- [Operace s datem a časem](datetime-operations.md)
- [Agregační funkce](aggregations.md)
- [Pokročilé agregace](advanced-aggregations.md)
- [JSON a datové struktury](json-data-structures.md)
- [Spojení](joins.md)
- [Grafy](charts.md)
