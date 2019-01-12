---
title: Dotazy na výstrahy protokolu ve službě Azure Monitor | Dokumentace Microsoftu
description: Poskytuje doporučení na zápis efektivní dotazy na výstrahy protokolu v proces pro převod existujících dotazů a aktualizace Azure monitoru.
author: yossi-y
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 01/08/2019
ms.author: bwren
ms.component: alerts
ms.openlocfilehash: 9cd745cedd6d870262e412709e4e104c1406558e
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2019
ms.locfileid: "54234078"
---
# <a name="log-alert-queries-in-azure-monitor"></a>Dotazy na výstrahy protokolu ve službě Azure Monitor
[Upozornění pravidla založená na protokoly Azure monitoru](alerts-unified-log.md) v pravidelných intervalech spouští, proto měli byste zajistit, že se zapisují pro minimalizaci režie a latenci. Tento článek obsahuje doporučení pro psaní efektivní dotazy na výstrahy protokolu a proces pro převod existujících dotazů. 

## <a name="types-of-log-queries"></a>Typy dotazů protokolu
[Dotazy v Log Analytics](../log-query/log-query-overview.md) začínat buď tabulku nebo [hledání](/azure/kusto/query/searchoperator) nebo [sjednocení](/azure/kusto/query/unionoperator) operátor.

Například následující dotaz je vymezen _SecurityEvent_ tabulky a vyhledá ID konkrétních událostí. Toto je pouze tabulky, která musí zpracovat dotaz.

``` Kusto
SecurityEvent | where EventID == 4624 
```

Dotazy, které začínají `search` nebo `union` umožňují prohledávat napříč více více sloupců v tabulce nebo dokonce více tabulek. Následující příklady ukazují několik metod pro hledání termín _paměti_:

```Kusto
search "Memory"

search * | where == "Memory"

search ObjectName: "Memory"

search ObjectName == "Memory"

union * | where ObjectName == "Memory"
```
 

I když `search` a `union` jsou užitečné při zkoumání dat, podmínek vyhledávání celého datového modelu, jsou míň efektivní než pomocí tabulky, protože musí zkontrolovat z více tabulek. Protože jsou v pravidelných intervalech spouští dotazy v pravidel upozornění, to může způsobit nadměrnému zatížení přidání latence výstrahy. Z důvodu režie měli vždy začít dotazy pro pravidla upozornění protokolů v Azure s tabulkou a určete obor jasné, což zvyšuje výkon dotazů a relevance výsledků.

## <a name="unsupported-queries"></a>Nepodporovaná dotazy
Od ledna 11,2019, vytvoření nebo úprava pravidel upozornění protokolů, které používají `search`, nebo `union` operátory nebudou podporovány v portálu Azure portal. Používání těchto operátorů v blocích pravidlo upozornění vrátí chybovou zprávu. Tato změna neovlivní stávající pravidla upozornění a pravidla výstrah vytvářejí se a upravují pomocí rozhraní API služby Log Analytics. Stále byste měli zvážit, že změny jakékoliv výstrahy pravidla, která používají tyto typy dotazů, i když ke zvýšení své efektivity.  

Pomocí pravidel upozornění protokolů [dotazy napříč prostředky](../log-query/cross-workspace-query.md) nejsou touto změnou ovlivněny od dotazy napříč prostředky používají `union`, což omezuje rozsah dotazu ke konkrétním prostředkům. Toto není ekvivalentní `union *` které nelze použít.  V následujícím příkladu by byly platné ve pravidel upozornění protokolů:

```Kusto
union 
app('Contoso-app1').requests, 
app('Contoso-app2').requests, 
workspace('Contoso-workspace1').Perf 
```
 

## <a name="examples"></a>Příklady
Následující příklady dotazů na protokoly, které používají `search` a `union` a popisuje kroky, které vám umožní upravit tyto dotazy pro použití s pravidla upozornění.

### <a name="example-1"></a>Příklad 1
Rozhodli jste se vytvořit pravidlo upozornění protokolu pomocí následujícího dotazu, který načte výkonu informací pomocí `search`: 

``` Kusto
search * | where Type == 'Perf' and CounterName == '% Free Space' 
| where CounterValue < 30 
| summarize count()
```
  

Tento dotaz změnit tak, začněte pomocí následujícího dotazu k identifikaci v tabulce, které vlastnosti patří:

``` Kusto
search * | where CounterName == '% Free Space'
| summarize by $table
```
 

Výsledek tohoto dotazu, který by zobrazil _CounterName_ vlastnost pochází _výkonu_ tabulky. 

Tento výsledek můžete použít k vytvoření následující dotaz, který používáte pro pravidlo výstrahy:

``` Kusto
Perf 
| where CounterName == '% Free Space' 
| where CounterValue < 30 
| summarize count()
```


### <a name="example-2"></a>Příklad 2
Rozhodli jste se vytvořit pravidlo upozornění protokolu pomocí následujícího dotazu, který načte výkonu informací pomocí `search`: 

``` Kusto
search ObjectName =="Memory" and CounterName=="% Committed Bytes In Use"  
| summarize Avg_Memory_Usage =avg(CounterValue) by Computer 
| where Avg_Memory_Usage between(90 .. 95)  
| count 
```
  

Tento dotaz změnit tak, začněte pomocí následujícího dotazu k identifikaci v tabulce, které vlastnosti patří:

``` Kusto
search ObjectName=="Memory" and CounterName=="% Committed Bytes In Use" 
| summarize by $table 
```
 

Výsledek tohoto dotazu, který by zobrazil _ObjectName_ a _CounterName_ vlastnost pochází _výkonu_ tabulky. 

Tento výsledek můžete použít k vytvoření následující dotaz, který používáte pro pravidlo výstrahy:

``` Kusto
Perf 
| where ObjectName =="Memory" and CounterName=="% Committed Bytes In Use" 
| summarize Avg_Memory_Usage=avg(CounterValue) by Computer 
| where Avg_Memory_Usage between(90 .. 95)  
| count 
```
 

### <a name="example-3"></a>Příklad 3

Rozhodli jste se vytvořit pravidlo upozornění protokolu pomocí následujícího dotazu, který používá obě `search` a `union` k načtení informací o výkonu: 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")  
| where Computer !in ((union * | where CounterName == "% Processor Utility" | summarize by Computer))
| summarize Avg_Idle_Time = avg(CounterValue) by Computer|  count  
```
 

Tento dotaz změnit tak, začněte pomocí následujícího dotazu k identifikaci tabulku, do které patří vlastnosti v první části dotazu: 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")  
| summarize by $table 
```

Výsledek tohoto dotazu, který by zobrazil, tyto vlastnosti pochází ze _výkonu_ tabulky. 

Teď pomocí `union` s `withsource` příkaz k identifikaci které zdrojová tabulka přispěl každý řádek.

``` Kusto
union withsource=table * | where CounterName == "% Processor Utility" 
| summarize by table 
```
 

Výsledek tohoto dotazu, který by zobrazil, tyto vlastnosti také pochází ze _výkonu_ tabulky. 

Tyto výsledky můžete použít k vytvoření následující dotaz, který používáte pro pravidlo výstrahy: 

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

### <a name="example-4"></a>Příklad 4:
Rozhodli jste se vytvořit pravidlo upozornění protokolu pomocí následujícího dotazu, které spojí výsledky dvou `search` dotazy:

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
 

Pokud chcete upravit dotaz, spusťte pomocí následujícího dotazu k identifikaci tabulku obsahující vlastnosti v levé strany spojení: 

``` Kusto
search Type == 'SecurityEvent' and EventID == '4625' 
| summarize by $table 
```
 

Výsledek označuje, že vlastnosti v levé strany spojení patřit _SecurityEvent_ tabulky. 

Teď použijte tento dotaz k identifikaci tabulku obsahující vlastnosti na pravé straně spojení: 

 
``` Kusto
search in (Heartbeat) OSType == 'Windows' 
| summarize by $table 
```

 
Výsledek označuje, že vlastnosti na pravé straně spojení nepatří do tabulky prezenčního signálu. 

Tyto výsledky můžete použít k vytvoření následující dotaz, který používáte pro pravidlo výstrahy: 


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

## <a name="next-steps"></a>Další postup
- Další informace o [upozornění protokolů](alerts-log.md) ve službě Azure Monitor.
- Další informace o [protokolu dotazy](../log-query/log-query-overview.md).