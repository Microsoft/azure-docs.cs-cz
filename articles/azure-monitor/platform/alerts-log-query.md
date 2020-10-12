---
title: Optimalizace dotazů na výstrahy protokolu | Microsoft Docs
description: Doporučení pro psaní efektivních dotazů na výstrahy
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 02/19/2019
ms.subservice: alerts
ms.openlocfilehash: 7f03858b2427b2a2069ebe2c9d06425e7a741e2b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91294355"
---
# <a name="optimizing-log-alert-queries"></a>Optimalizace dotazů na výstrahy protokolu
Tento článek popisuje, jak zapsat a převést dotazy na [výstrahy protokolu](alerts-unified-log.md) , abyste dosáhli optimálního výkonu. Optimalizované dotazy omezují latenci a zatížení výstrah, které se často spouštějí.

## <a name="how-to-start-writing-an-alert-log-query"></a>Jak začít psát dotaz protokolu výstrah

Dotazy na výstrahy začínají [dotazem na data protokolu v Log Analytics](alerts-log.md#create-a-log-alert-rule-with-the-azure-portal) , která tento problém signalizují. V [tématu Příklady dotazů na výstrahy](../log-query/saved-queries.md) můžete pochopit, co můžete zjistit. Můžete také začít [psát vlastní dotaz](../log-query/get-started-portal.md). 

### <a name="queries-that-indicate-the-issue-and-not-the-alert"></a>Dotazy, které indikují problém, a ne upozornění

Tok výstrah byl vytvořen pro transformaci výsledků, které upozorňují na problém výstrahy. Například v případě dotazu jako:

``` Kusto
SecurityEvent
| where EventID == 4624
```

Pokud je záměrem uživatele výstraha, když dojde k tomuto typu události, logika výstrahy se připojí `count` k dotazu. Dotaz, který se spustí, bude:

``` Kusto
SecurityEvent
| where EventID == 4624
| count
```

Není nutné přidávat do dotazu logiku pro upozorňování a provádět v nich dokonce problémy. Pokud v příkladu výše zahrnete `count` do dotazu, bude vždy výsledkem hodnota 1, protože služba Výstrahy bude provádět `count` `count` .

### <a name="avoid-limit-and-take-operators"></a>Vyhnout `limit` se `take` operátorům a

Použití `limit` a `take` v dotazech může zvýšit latenci a zatížení výstrah, protože výsledky nejsou v průběhu času konzistentní. Doporučuje se používat ho pouze v případě potřeby.

## <a name="log-query-constraints"></a>Omezení dotazů na protokol
[Dotazy protokolu v Azure monitor](../log-query/log-query-overview.md) začínají buď s tabulkou, [`search`](/azure/kusto/query/searchoperator) nebo [`union`](/azure/kusto/query/unionoperator) operátorem.

Dotazy na pravidla upozornění protokolu by měly vždy začínat tabulkou, která má definovat jasný rozsah, což zvyšuje výkon dotazů a relevanci výsledků. Dotazy v pravidlech výstrah běží často, takže pomocí `search` a `union` můžou mít za následek nadměrné nároky na přidání latence k výstraze, protože to vyžaduje skenování napříč více tabulkami. Tyto operátory také omezují schopnost služby upozorňování na optimalizaci dotazu.

Nepodporujeme vytváření a úpravu pravidel upozornění protokolů, které `search` používají `union` operátory nebo, a očekávat pro ně dotazy mezi prostředky.

Například následující dotaz pro upozorňování je vymezen na tabulku _SecurityEvent_ a hledá konkrétní ID události. Je to jediná tabulka, kterou musí dotaz zpracovat.

``` Kusto
SecurityEvent
| where EventID == 4624
```

Tato změna neovlivní pravidla upozornění protokolu pomocí [dotazů na více prostředků](../log-query/cross-workspace-query.md) , protože dotazy na více zdrojů používají typ `union` , který omezuje obor dotazu na konkrétní prostředky. Následující příklad bude platný dotaz na výstrahu protokolu:

```Kusto
union
app('Contoso-app1').requests,
app('Contoso-app2').requests,
workspace('Contoso-workspace1').Perf 
```

>[!NOTE]
> [Dotazy na více prostředků](../log-query/cross-workspace-query.md) jsou podporovány v novém [rozhraní scheduledQueryRules API](/rest/api/monitor/scheduledqueryrules). Pokud pořád používáte [starší rozhraní API Log Analytics výstrah](api-alerts.md) pro vytváření upozornění protokolů, můžete se přepínat [tady](alerts-log-api-switch.md).

## <a name="examples"></a>Příklady
Následující příklady zahrnují dotazy protokolů, které používají, a `search` `union` poskytují kroky, pomocí kterých můžete tyto dotazy upravovat pro použití v pravidlech výstrah.

### <a name="example-1"></a>Příklad 1
Chcete vytvořit pravidlo upozornění protokolu pomocí následujícího dotazu, který načte informace o výkonu pomocí `search` : 

``` Kusto
search *
| where Type == 'Perf' and CounterName == '% Free Space'
| where CounterValue < 30
```

Chcete-li upravit tento dotaz, začněte pomocí následujícího dotazu Identifikujte tabulku, do které vlastnosti patří:

``` Kusto
search *
| where CounterName == '% Free Space'
| summarize by $table
```

Výsledek tohoto dotazu by ukázal, že vlastnost _CounterName_ byla předána z tabulky _perf_ .

Pomocí tohoto výsledku můžete vytvořit následující dotaz, který byste použili pro pravidlo upozornění:

``` Kusto
Perf
| where CounterName == '% Free Space'
| where CounterValue < 30
```

### <a name="example-2"></a>Příklad 2
Chcete vytvořit pravidlo upozornění protokolu pomocí následujícího dotazu, který načte informace o výkonu pomocí `search` : 

``` Kusto
search ObjectName =="Memory" and CounterName=="% Committed Bytes In Use"
| summarize Avg_Memory_Usage =avg(CounterValue) by Computer
| where Avg_Memory_Usage between(90 .. 95)  
```

Chcete-li upravit tento dotaz, začněte pomocí následujícího dotazu Identifikujte tabulku, do které vlastnosti patří:

``` Kusto
search ObjectName=="Memory" and CounterName=="% Committed Bytes In Use"
| summarize by $table
```

Výsledek tohoto dotazu by ukázal, že vlastnost _ObjectName_ a _CounterName_ pochází z tabulky _perf_ .

Pomocí tohoto výsledku můžete vytvořit následující dotaz, který byste použili pro pravidlo upozornění:

``` Kusto
Perf
| where ObjectName =="Memory" and CounterName=="% Committed Bytes In Use"
| summarize Avg_Memory_Usage=avg(CounterValue) by Computer
| where Avg_Memory_Usage between(90 .. 95)
```

### <a name="example-3"></a>Příklad 3

Chcete vytvořit pravidlo upozornění protokolu pomocí následujícího dotazu, který používá obojí `search` a `union` k získání informací o výkonu: 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")
| where Computer !in (
    union *
    | where CounterName == "% Processor Utility"
    | summarize by Computer)
| summarize Avg_Idle_Time = avg(CounterValue) by Computer
```

Chcete-li upravit tento dotaz, začněte pomocí následujícího dotazu Identifikujte tabulku, do které vlastnosti v první části dotazu patří: 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")
| summarize by $table
```

Výsledek tohoto dotazu by ukázal, že všechny tyto vlastnosti byly získány z tabulky _perf_ .

Nyní použijte `union` `withsource` příkaz with k určení, která zdrojová tabulka přispěla k jednotlivým řádkům.

``` Kusto
union withsource=table *
| where CounterName == "% Processor Utility"
| summarize by table
```

Výsledek tohoto dotazu by ukázal, že tyto vlastnosti také pocházejí z tabulky _perf_ .

Pomocí těchto výsledků můžete vytvořit následující dotaz, který byste použili pro pravidlo upozornění: 

``` Kusto
Perf
| where ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total"
| where Computer !in (
    (Perf
    | where CounterName == "% Processor Utility"
    | summarize by Computer))
| summarize Avg_Idle_Time = avg(CounterValue) by Computer
``` 

### <a name="example-4"></a>Příklad 4
Chcete vytvořit pravidlo upozornění protokolu pomocí následujícího dotazu, který spojuje výsledky dvou `search` dotazů:

```Kusto
search Type == 'SecurityEvent' and EventID == '4625'
| summarize by Computer, Hour = bin(TimeGenerated, 1h)
| join kind = leftouter (
    search in (Heartbeat) OSType == 'Windows'
    | summarize arg_max(TimeGenerated, Computer) by Computer , Hour = bin(TimeGenerated, 1h)
    | project Hour , Computer
) on Hour
```

Chcete-li upravit dotaz, začněte použitím následujícího dotazu k identifikaci tabulky, která obsahuje vlastnosti v levé straně spojení: 

``` Kusto
search Type == 'SecurityEvent' and EventID == '4625'
| summarize by $table
```

Výsledek značí, že vlastnosti na levé straně spojení patří do tabulky _SecurityEvent_ . 

Nyní použijte následující dotaz k identifikaci tabulky, která obsahuje vlastnosti na pravé straně spojení: 
 
``` Kusto
search in (Heartbeat) OSType == 'Windows'
| summarize by $table
```
 
Výsledek značí, že vlastnosti na pravé straně spojení patří do tabulky _prezenčního signálu_ .

Pomocí těchto výsledků můžete vytvořit následující dotaz, který byste použili pro pravidlo upozornění: 

``` Kusto
SecurityEvent
| where EventID == '4625'
| summarize by Computer, Hour = bin(TimeGenerated, 1h)
| join kind = leftouter (
    Heartbeat
    | where OSType == 'Windows'
    | summarize arg_max(TimeGenerated, Computer) by Computer , Hour = bin(TimeGenerated, 1h)
    | project Hour , Computer
) on Hour
```

## <a name="next-steps"></a>Další kroky
- Přečtěte si informace o [upozorněních protokolu](alerts-log.md) v Azure monitor.
- Přečtěte si o [dotazech protokolu](../log-query/log-query-overview.md).
