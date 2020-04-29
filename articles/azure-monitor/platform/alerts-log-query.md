---
title: Protokolovat dotazy výstrah v Azure Monitor | Microsoft Docs
description: Poskytuje doporučení pro psaní efektivních dotazů na výstrahy protokolu v Azure Monitor aktualizace a procesu pro převod existujících dotazů.
author: yossi-y
ms.author: yossiy
ms.topic: conceptual
ms.date: 02/19/2019
ms.subservice: alerts
ms.openlocfilehash: fdf492b8f103e725046b9b1cbbd079c4d249664a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77667784"
---
# <a name="log-alert-queries-in-azure-monitor"></a>Protokolovat dotazy výstrah v Azure Monitor
[Pravidla výstrah na základě protokolů Azure monitor](alerts-unified-log.md) běží v pravidelných intervalech, takže byste měli zajistit, aby byly zapsány pro minimalizaci režie a latence. Tento článek poskytuje doporučení k psaní efektivních dotazů na výstrahy protokolu a procesu pro převod existujících dotazů. 

## <a name="types-of-log-queries"></a>Typy dotazů protokolu
[Dotazy protokolu v Azure monitor](../log-query/log-query-overview.md) začínají buď s tabulkou, nebo s operátorem [hledání](/azure/kusto/query/searchoperator) nebo [sjednocení](/azure/kusto/query/unionoperator) .

Například následující dotaz je vymezen na tabulku _SecurityEvent_ a hledá konkrétní ID události. Toto je jediná tabulka, kterou musí dotaz zpracovat.

``` Kusto
SecurityEvent | where EventID == 4624 
```

Dotazy, které začínají `search` na `union` nebo umožňují prohledávat více sloupců v tabulce nebo dokonce i v několika tabulkách. Následující příklady znázorňují několik metod hledání termínu _paměti_:

```Kusto
search "Memory"
search * | where == "Memory"
search ObjectName: "Memory"
search ObjectName == "Memory"
union * | where ObjectName == "Memory"
```

Přestože `search` a `union` jsou užitečné při zkoumání dat, vyhledávání podmínek nad celým datovým modelem, je méně efektivní než použití tabulky, protože musí kontrolovat napříč více tabulkami. Vzhledem k tomu, že dotazy v pravidlech výstrah jsou spouštěny v pravidelných intervalech, může to mít za následek nadměrné režie přidání latence do výstrahy. Z důvodu této režie by dotazy na pravidla výstrah protokolů v Azure měly vždycky začít s tabulkou, která má definovat jasný rozsah, což zvyšuje výkon dotazů a relevanci výsledků.

## <a name="unsupported-queries"></a>Nepodporované dotazy
Od 11. ledna 2019, vytváření nebo úpravy pravidel upozornění protokolů, které `search`používají, `union` nebo operátory nebudou podporovány v Azure Portal. Při použití těchto operátorů v pravidle výstrahy dojde k vrácení chybové zprávy. Tato změna nemá vliv na existující pravidla a pravidla upozornění vytvořená a upravená pomocí rozhraní Log Analytics API. Stále byste měli zvážit změnu všech pravidel upozornění, která používají tyto typy dotazů, a tím i zlepšit jejich efektivitu.  

Tato změna neovlivní pravidla upozornění protokolu pomocí [dotazů na více zdrojů](../log-query/cross-workspace-query.md) `union`, což omezuje rozsah dotazu na konkrétní prostředky. Nejedná se o `union *` ekvivalent, který nelze použít.  Následující příklad bude platný v pravidle výstrahy protokolu:

```Kusto
union 
app('Contoso-app1').requests, 
app('Contoso-app2').requests, 
workspace('Contoso-workspace1').Perf 
```

>[!NOTE]
>[Dotaz na více prostředků](../log-query/cross-workspace-query.md) v upozorněních protokolu se podporuje v novém [rozhraní scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Ve výchozím nastavení používá Azure Monitor [starší rozhraní api Log Analytics výstrahy](api-alerts.md) pro vytváření nových pravidel upozornění protokolu z Azure Portal, pokud nepřepnete ze [starší verze rozhraní API upozornění protokolu](alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api). Po přepínači se nové rozhraní API nastaví jako výchozí pro nová pravidla upozornění v Azure Portal a umožní vám vytvořit pravidla pro výstrahy protokolu dotazů mezi prostředky. Pravidla upozornění protokolu [dotazu pro více prostředků](../log-query/cross-workspace-query.md) můžete vytvořit bez toho, aby byl přepínač použit pomocí [šablony ARM pro rozhraní scheduledQueryRules API](alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template) , ale toto pravidlo upozornění lze spravovat i v případě, že [rozhraní scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) , nikoli z Azure Portal.

## <a name="examples"></a>Příklady
Následující příklady zahrnují dotazy protokolů, které používají `search` `union` , a poskytují kroky, pomocí kterých můžete tyto dotazy upravovat pro použití s pravidly výstrah.

### <a name="example-1"></a>Příklad 1
Chcete vytvořit pravidlo upozornění protokolu pomocí následujícího dotazu, který načte informace o výkonu pomocí `search`: 

``` Kusto
search * | where Type == 'Perf' and CounterName == '% Free Space' 
| where CounterValue < 30 
| summarize count()
```
  

Chcete-li upravit tento dotaz, začněte pomocí následujícího dotazu Identifikujte tabulku, do které vlastnosti patří:

``` Kusto
search * | where CounterName == '% Free Space'
| summarize by $table
```
 

Výsledek tohoto dotazu by ukázal, že vlastnost _CounterName_ byla předána z tabulky _perf_ . 

Tento výsledek můžete použít k vytvoření následujícího dotazu, který byste použili pro pravidlo upozornění:

``` Kusto
Perf 
| where CounterName == '% Free Space' 
| where CounterValue < 30 
| summarize count()
```


### <a name="example-2"></a>Příklad 2
Chcete vytvořit pravidlo upozornění protokolu pomocí následujícího dotazu, který načte informace o výkonu pomocí `search`: 

``` Kusto
search ObjectName =="Memory" and CounterName=="% Committed Bytes In Use"  
| summarize Avg_Memory_Usage =avg(CounterValue) by Computer 
| where Avg_Memory_Usage between(90 .. 95)  
| count 
```
  

Chcete-li upravit tento dotaz, začněte pomocí následujícího dotazu Identifikujte tabulku, do které vlastnosti patří:

``` Kusto
search ObjectName=="Memory" and CounterName=="% Committed Bytes In Use" 
| summarize by $table 
```
 

Výsledek tohoto dotazu by ukázal, že vlastnost _ObjectName_ a _CounterName_ pochází z tabulky _perf_ . 

Tento výsledek můžete použít k vytvoření následujícího dotazu, který byste použili pro pravidlo upozornění:

``` Kusto
Perf 
| where ObjectName =="Memory" and CounterName=="% Committed Bytes In Use" 
| summarize Avg_Memory_Usage=avg(CounterValue) by Computer 
| where Avg_Memory_Usage between(90 .. 95)  
| count 
```
 

### <a name="example-3"></a>Příklad 3

Chcete vytvořit pravidlo upozornění protokolu pomocí následujícího dotazu, který používá obojí `search` a `union` k získání informací o výkonu: 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")  
| where Computer !in ((union * | where CounterName == "% Processor Utility" | summarize by Computer))
| summarize Avg_Idle_Time = avg(CounterValue) by Computer|  count  
```
 

Chcete-li upravit tento dotaz, začněte pomocí následujícího dotazu Identifikujte tabulku, do které vlastnosti v první části dotazu patří: 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")  
| summarize by $table 
```

Výsledek tohoto dotazu by ukázal, že všechny tyto vlastnosti byly získány z tabulky _perf_ . 

Nyní použijte `union` příkaz `withsource` with k určení, která zdrojová tabulka přispěla k jednotlivým řádkům.

``` Kusto
union withsource=table * | where CounterName == "% Processor Utility" 
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
| count 
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
)  
on Hour 
| count 
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

 
Výsledek značí, že vlastnosti na pravé straně spojení patří do tabulky prezenčního signálu. 

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
)  
on Hour 
| count 
```

## <a name="next-steps"></a>Další kroky
- Přečtěte si informace o [upozorněních protokolu](alerts-log.md) v Azure monitor.
- Přečtěte si o [dotazech protokolu](../log-query/log-query-overview.md).

