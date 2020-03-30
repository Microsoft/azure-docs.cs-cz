---
title: Protokolovat dotazy s výstrahami v Azure Monitoru | Dokumenty společnosti Microsoft
description: Poskytuje doporučení pro psaní efektivní dotazy pro výstrahy protokolu v aktualizacích Azure Monitor a proces pro převod existujících dotazů.
author: yossi-y
ms.author: yossiy
ms.topic: conceptual
ms.date: 02/19/2019
ms.subservice: alerts
ms.openlocfilehash: fdf492b8f103e725046b9b1cbbd079c4d249664a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77667784"
---
# <a name="log-alert-queries-in-azure-monitor"></a>Protokolovat dotazy s výstrahami v Azure Monitoru
[Pravidla výstrah založená na protokolech Azure Monitor](alerts-unified-log.md) ustavují v pravidelných intervalech, takže byste měli zajistit, aby byly zapsány, aby se minimalizovala režie a latence. Tento článek obsahuje doporučení pro psaní efektivní dotazy pro výstrahy protokolu a proces pro převod existujících dotazů. 

## <a name="types-of-log-queries"></a>Typy dotazů protokolu
[Protokolovat dotazy v Azure Monitoru](../log-query/log-query-overview.md) začínají buď s tabulkou nebo [vyhledávacím](/azure/kusto/query/searchoperator) nebo [odborovým](/azure/kusto/query/unionoperator) operátorem.

Například následující dotaz je vymezen na tabulku _SecurityEvent_ a vyhledá konkrétní ID události. Toto je jediná tabulka, kterou musí dotaz zpracovat.

``` Kusto
SecurityEvent | where EventID == 4624 
```

Dotazy, které `search` začínají `union` nebo umožňují prohledávat více sloupců v tabulce nebo dokonce více tabulek. Následující příklady ukazují více metod pro vyhledávání termínu _Paměť_:

```Kusto
search "Memory"
search * | where == "Memory"
search ObjectName: "Memory"
search ObjectName == "Memory"
union * | where ObjectName == "Memory"
```

I `search` `union` když a jsou užitečné při zkoumání dat, hledání termíny v celém datovém modelu, jsou méně efektivní než pomocí tabulky, protože musí skenovat ve více tabulkách. Vzhledem k tomu, že dotazy v pravidlech výstrah jsou spouštěny v pravidelných intervalech, může to mít za následek nadměrné režie přidání latence výstrahy. Z důvodu této režie by měly dotazy na pravidla výstrah protokolu v Azure vždy začínat s tabulkou, která definuje jasný obor, který zlepšuje výkon dotazů a relevanci výsledků.

## <a name="unsupported-queries"></a>Nepodporované dotazy
ledna 2019, vytváření nebo úpravy pravidel upozornění `search`protokolu, `union` které používají , nebo operátory nebudou podporovány na webu Azure Portal. Použití těchto operátorů v pravidle výstrahy vrátí chybovou zprávu. Existující pravidla výstrah a pravidla výstrah vytvořené a upravené pomocí rozhraní API analýzy protokolů nejsou touto změnou ovlivněny. Stále byste měli zvážit změnu všech pravidel výstrah, které používají tyto typy dotazů i když ke zlepšení jejich efektivity.  

Protokolovat pravidla výstrah pomocí dotazů mezi prostředky nejsou touto změnou `union` [ovlivněna,](../log-query/cross-workspace-query.md) protože používají dotazy mezi zdroji , což omezuje obor dotazu na konkrétní prostředky. To není ekvivalent, `union *` který nelze použít.  Následující příklad by byl platný v pravidle výstrahy protokolu:

```Kusto
union 
app('Contoso-app1').requests, 
app('Contoso-app2').requests, 
workspace('Contoso-workspace1').Perf 
```

>[!NOTE]
>[Dotaz mezi prostředky](../log-query/cross-workspace-query.md) ve výstrahách protokolu je podporován v novém [rozhraní API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Ve výchozím nastavení Azure Monitor používá [starší rozhraní API upozornění log Analytics](api-alerts.md) pro vytváření nových pravidel upozornění protokolu z webu Azure Portal, pokud nepřepnete z [staršího rozhraní API pro výstrahy protokolů](alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api). Po přepnutí se nové rozhraní API stane výchozím pro nová pravidla výstrah na webu Azure Portal a umožňuje vytvářet pravidla upozornění protokolu dotazů mezi prostředky. Můžete vytvořit pravidla výstrah [protokolu dotazů mezi prostředky](../log-query/cross-workspace-query.md) bez provedení přepínače pomocí šablony ARM pro rozhraní API [scheduledQueryRules](alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template) – ale toto pravidlo výstrahy je spravovatelné prostřednictvím [rozhraní ScheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) a nikoli z portálu Azure.

## <a name="examples"></a>Příklady
Mezi následující příklady patří dotazy `search` `union` protokolu, které používají a poskytují kroky, které můžete použít k úpravě těchto dotazů pro použití s pravidly výstrah.

### <a name="example-1"></a>Příklad 1
Chcete vytvořit pravidlo výstrahprotokolu pomocí následujícího dotazu, `search`který načítá informace o výkonu pomocí : 

``` Kusto
search * | where Type == 'Perf' and CounterName == '% Free Space' 
| where CounterValue < 30 
| summarize count()
```
  

Chcete-li tento dotaz upravit, začněte pomocí následujícího dotazu k identifikaci tabulky, do které vlastnosti patří:

``` Kusto
search * | where CounterName == '% Free Space'
| summarize by $table
```
 

Výsledek tohoto dotazu by ukázat, že _CounterName_ vlastnost pochází z tabulky _Perf._ 

Tento výsledek můžete použít k vytvoření následujícího dotazu, který byste použili pro pravidlo výstrahy:

``` Kusto
Perf 
| where CounterName == '% Free Space' 
| where CounterValue < 30 
| summarize count()
```


### <a name="example-2"></a>Příklad 2
Chcete vytvořit pravidlo výstrahprotokolu pomocí následujícího dotazu, `search`který načítá informace o výkonu pomocí : 

``` Kusto
search ObjectName =="Memory" and CounterName=="% Committed Bytes In Use"  
| summarize Avg_Memory_Usage =avg(CounterValue) by Computer 
| where Avg_Memory_Usage between(90 .. 95)  
| count 
```
  

Chcete-li tento dotaz upravit, začněte pomocí následujícího dotazu k identifikaci tabulky, do které vlastnosti patří:

``` Kusto
search ObjectName=="Memory" and CounterName=="% Committed Bytes In Use" 
| summarize by $table 
```
 

Výsledek tohoto dotazu by ukázat, že _ObjectName_ a _CounterName_ vlastnost pochází z tabulky _Perf._ 

Tento výsledek můžete použít k vytvoření následujícího dotazu, který byste použili pro pravidlo výstrahy:

``` Kusto
Perf 
| where ObjectName =="Memory" and CounterName=="% Committed Bytes In Use" 
| summarize Avg_Memory_Usage=avg(CounterValue) by Computer 
| where Avg_Memory_Usage between(90 .. 95)  
| count 
```
 

### <a name="example-3"></a>Příklad 3

Chcete vytvořit pravidlo výstrahprotokolu pomocí následujícího dotazu, který používá jak informace o výkonu, `search` `union` tak pro načtení informací o výkonu: 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")  
| where Computer !in ((union * | where CounterName == "% Processor Utility" | summarize by Computer))
| summarize Avg_Idle_Time = avg(CounterValue) by Computer|  count  
```
 

Chcete-li tento dotaz upravit, začněte pomocí následujícího dotazu k identifikaci tabulky, do které patří vlastnosti v první části dotazu: 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")  
| summarize by $table 
```

Výsledek tohoto dotazu by ukázat, že všechny tyto vlastnosti pocházejí z tabulky _Perf._ 

Nyní `union` použijte `withsource` příkaz k identifikaci zdrojové tabulky, která přispěla každý řádek.

``` Kusto
union withsource=table * | where CounterName == "% Processor Utility" 
| summarize by table 
```
 

Výsledek tohoto dotazu by ukázat, že tyto vlastnosti také pochází z tabulky _Perf._ 

Tyto výsledky můžete použít k vytvoření následujícího dotazu, který byste použili pro pravidlo výstrahy: 

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
Chcete vytvořit pravidlo výstrahprotokolu pomocí následujícího dotazu, který `search` spojuje výsledky dvou dotazů:

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
 

Chcete-li dotaz upravit, začněte pomocí následujícího dotazu k identifikaci tabulky, která obsahuje vlastnosti na levé straně spojení: 

``` Kusto
search Type == 'SecurityEvent' and EventID == '4625' 
| summarize by $table 
```
 

Výsledek označuje, že vlastnosti na levé straně spojení patří do tabulky _SecurityEvent._ 

Nyní použijte následující dotaz k identifikaci tabulky, která obsahuje vlastnosti na pravé straně spojení: 

 
``` Kusto
search in (Heartbeat) OSType == 'Windows' 
| summarize by $table 
```

 
Výsledek označuje, že vlastnosti na pravé straně spojení patří do tabulky Prezenční signál. 

Tyto výsledky můžete použít k vytvoření následujícího dotazu, který byste použili pro pravidlo výstrahy: 


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
- Další informace o [výstrahách protokolů](alerts-log.md) v Azure Monitoru.
- Informace o [dotazech protokolu](../log-query/log-query-overview.md).

