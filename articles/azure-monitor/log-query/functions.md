---
title: Funkce v Azure Monitorch dotazech protokolu | Microsoft Docs
description: Tento článek popisuje, jak pomocí funkce volat dotaz z jiného dotazu protokolu v Azure Monitor.
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
ms.date: 11/15/2018
ms.author: bwren
ms.openlocfilehash: 75beb7b66863efd2fb3679f034a3663dca4a6d2f
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076704"
---
# <a name="using-functions-in-azure-monitor-log-queries"></a>Použití funkcí v Azure Monitorch dotazech protokolu

Pokud chcete použít dotaz protokolu s jiným dotazem, můžete ho uložit jako funkci. To vám umožní zjednodušit složité dotazy tím, že je rozdělíte do částí a umožníte znovu použít společný kód s více dotazy.

## <a name="create-a-function"></a>Vytvoření funkce

Vytvořte funkci s Log Analytics v Azure Portal kliknutím na **Uložit** a zadáním informací v následující tabulce.

| Nastavení | Popis |
|:---|:---|
| Name           | Zobrazovaný název dotazu v **Průzkumníku dotazů** |
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
- [Starat](joins.md)
- [Spojnic](charts.md)
