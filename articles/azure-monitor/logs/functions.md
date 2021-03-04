---
title: Funkce v Azure Monitorch dotazech protokolu | Microsoft Docs
description: Tento článek popisuje, jak pomocí funkce volat dotaz z jiného dotazu protokolu v Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/31/2020
ms.openlocfilehash: 9b2466bbe061dc147d020104b13da197840fe40a
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102030779"
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




## <a name="use-a-function"></a>Použití funkce
Použijte funkci zahrnutím jejího aliasu do jiného dotazu. Dá se použít jako jakákoli jiná tabulka.

## <a name="function-parameters"></a>Parametry funkce 
Můžete přidat parametry do funkce, abyste při volání mohli zadat hodnoty pro určité proměnné. Jediným způsobem, jak aktuálně vytvořit funkci s parametry, je použití šablony Správce prostředků. Příklad najdete v tématu [Správce prostředků ukázek šablon pro dotazy protokolů v Azure monitor](./resource-manager-log-queries.md#parameterized-function) .

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

- [Operace s řetězci](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#string-operations)
- [Operace s datem a časem](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#date-and-time-operations)
- [Agregační funkce](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#aggregations)
- [Pokročilé agregace](/azure/data-explorer/write-queries#advanced-aggregations)
- [JSON a datové struktury](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#json-and-data-structures)
- [Spojení](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#joins)
- [Grafy](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#charts)