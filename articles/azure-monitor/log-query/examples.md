---
title: Příklady dotazů Azure Monitor Log Analytics | Dokumentace Microsoftu
description: Příklady dotazů ve službě Log Analytics v jazyce Kusto.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/03/2018
ms.author: bwren
ms.openlocfilehash: d5cad3869e74f33a2d1a56352c658bb9c8f23db6
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52882459"
---
# <a name="log-analytics-query-examples"></a>Příklady dotazů analýzy protokolů
Tento článek obsahuje příklady různých [dotazy](../../azure-monitor/log-query/log-query-overview.md) pomocí [Kusto jazyk](https://docs.microsoft.com/azure/kusto/query/) načíst různé typy dat ze služby Log Analytics. Různé metody slouží ke konsolidaci a analýzu dat, takže tyto ukázky můžete použít k identifikaci různé strategie, které můžete použít pro vaše požadavky.  

Najdete v článku [referenční informace k jazyku Kusto](https://docs.microsoft.com/azure/kusto/query/) podrobnosti o jiná klíčová slova použít v těchto ukázek. Projděte si [lekce týkající se vytváření dotazů](get-started-queries.md) Pokud začínáte ke službě Log Analytics.

## <a name="events"></a>Události

### <a name="search-application-level-events-described-as-cryptographic"></a>Vyhledávání událostí na úrovni aplikace popsat jako "Kryptografický"
Tento příklad vyhledá **události** tabulky pro záznamy, ve kterém **EventLog** je _aplikace_ a **RenderedDescription** obsahuje _kryptografických_. Obsahuje záznamy z posledních 24 hodin.

```Kusto
Event
| where EventLog == "Application" 
| where TimeGenerated > ago(24h) 
| where RenderedDescription == "cryptographic"
```

### <a name="search-events-related-to-unmarshaling"></a>Hledat události související s unmarshaling
Hledat tabulky **události** a **SecurityEvents** pro záznamů tento údaj _unmashaling_.

```Kusto
search in (Event, SecurityEvent) "unmarshaling"
```

## <a name="heartbeat"></a>Prezenční signál

### <a name="chart-a-week-over-week-view-of-the-number-of-computers-sending-data"></a>Týden over týden zobrazení počtu počítačů odesílajících data grafu

Následující příklad grafy počet různých počítačů, které odeslaly prezenční signály, každý týden.

```Kusto
Heartbeat
| where TimeGenerated >= startofweek(ago(21d))
| summarize dcount(Computer) by endofweek(TimeGenerated) | render barchart kind=default
```

### <a name="find-stale-computers"></a>Žádné zastaralé počítače

Následující příklad vyhledá počítače, které byly aktivní v poslední den, ale neodeslal prezenční signály za poslední hodinu.

```Kusto
Heartbeat
| where TimeGenerated > ago(1d)
| summarize LastHeartbeat = max(TimeGenerated) by Computer
| where isnotempty(Computer)
| where LastHeartbeat < ago(1h)
```

### <a name="get-the-latest-heartbeat-record-per-computer-ip"></a>Získat nejnovější záznam prezenčního signálu a IP adresu počítače

V tomto příkladu vrátí poslední záznam prezenčního signálu pro každou IP adresu počítače.
```Kusto
Heartbeat
| summarize arg_max(TimeGenerated, *) by ComputerIP
```

### <a name="match-protected-status-records-with-heartbeat-records"></a>Odpovídá chráněné stav záznamů se záznamy prezenčního signálu

Tento příklad vyhledá související ochrany stav záznamů a záznamy prezenčního signálu, odpovídá na počítači a čas.
Poznámka: pole s časem se zaokrouhlí na nejbližší minutu. Výpočet bin runtime jsme použili na to: `round_time=bin(TimeGenerated, 1m)`.

```Kusto
let protection_data = ProtectionStatus
    | project Computer, DetectionId, round_time=bin(TimeGenerated, 1m);
let heartbeat_data = Heartbeat
    | project Computer, Category, round_time=bin(TimeGenerated, 1m);
protection_data | join (heartbeat_data) on Computer, round_time
```

### <a name="server-availability-rate"></a>Míra dostupnosti serveru

Vypočítejte sazbu dostupnost serveru podle záznamy prezenčního signálu. Dostupnost se definuje jako "aspoň 1 prezenční signál za hodinu".
Takže pokud server byla k dispozici 98 100 hodin, míra dostupnosti je 98 %.

```Kusto
let start_time=startofday(datetime("2018-03-01"));
let end_time=now();
Heartbeat
| where TimeGenerated > start_time and TimeGenerated < end_time
| summarize heartbeat_per_hour=count() by bin_at(TimeGenerated, 1h, start_time), Computer
| extend available_per_hour=iff(heartbeat_per_hour>0, true, false)
| summarize total_available_hours=countif(available_per_hour==true) by Computer 
| extend total_number_of_buckets=round((end_time-start_time)/1h)+1
| extend availability_rate=total_available_hours*100/total_number_of_buckets
```


## <a name="multiple-data-types"></a>Více typů dat

### <a name="chart-the-record-count-per-table"></a>Graf počet záznamů na tabulku
Následující příklad počty, kolik záznamů se v každé tabulce a shromažďuje všechny záznamy všechny tabulky z posledních pět hodin. Výsledky jsou zobrazeny v časový graf.

```Kusto
union withsource=sourceTable *
| where TimeGenerated > ago(5h) 
| summarize count() by bin(TimeGenerated,10m), sourceTable
| render timechart
```

### <a name="count-all-logs-collected-over-the-last-hour-by-type"></a>Počítat všechny protokoly typu shromážděné za poslední hodinu
V následujícím příkladu prohledává všechno, co hlášené za poslední hodinu a vrátí počet záznamů z každé tabulky podle **typ**. Výsledky se zobrazí v pruhovém grafu.

```Kusto
search *
| where TimeGenerated > ago(1h) 
| summarize CountOfRecords = count() by Type
| render barchart
```

## <a name="azurediagnostics"></a>AzureDiagnostics

### <a name="count-azure-diagnostics-records-per-category"></a>Počet záznamů diagnostiky Azure na kategorii
V tomto příkladu vrátí všechny záznamy diagnostiky Azure pro každou kategorii jedinečný.

```Kusto
AzureDiagnostics 
| where TimeGenerated > ago(1d)
| summarize count() by Category
```

### <a name="get-a-random-record-for-each-unique-category"></a>Získat náhodné záznam pro každou jedinečnou kategorii
Tento příklad načte záznam jeden náhodné diagnostiky Azure pro každou kategorii jedinečný.

```Kusto
AzureDiagnostics
| where TimeGenerated > ago(1d) 
| summarize any(*) by Category
```

### <a name="get-the-latest-record-per-category"></a>Získat nejnovější záznam na kategorii
Tento příklad načte nejnovější záznam diagnostiky Azure v jednotlivých kategoriích jedinečný.

```Kusto
AzureDiagnostics
| where TimeGenerated > ago(1d) 
| summarize arg_max(TimeGenerated, *) by Category
```

## <a name="network-monitoring"></a>Monitorování sítě

### <a name="computers-with-unhealthy-latency"></a>Počítače s latencí není v pořádku
Tento příklad vytvoří seznam jedinečných počítačů s latencí není v pořádku.

```Kusto
NetworkMonitoring 
| where LatencyHealthState <> "Healthy" 
| where Computer != "" 
| distinct Computer
```

## <a name="performance"></a>Výkon

### <a name="join-computer-perf-records-to-correlate-memory-and-cpu"></a>Připojte se k počítači záznamy výkonu ke korelaci paměti a procesoru
V tomto příkladu koreluje určitého počítače **výkonu** záznamy a vytvoří dva grafy, průměrné využití procesoru a maximální velikost paměti.

```Kusto
let StartTime = now()-5d;
let EndTime = now()-4d;
Perf
| where CounterName == "% Processor Time"  
| where TimeGenerated > StartTime and TimeGenerated < EndTime
and TimeGenerated < EndTime
| project TimeGenerated, Computer, cpu=CounterValue 
| join kind= inner (
   Perf
    | where CounterName == "% Used Memory"  
    | where TimeGenerated > StartTime and TimeGenerated < EndTime
    | project TimeGenerated , Computer, mem=CounterValue 
) on TimeGenerated, Computer
| summarize avgCpu=avg(cpu), maxMem=max(mem) by TimeGenerated bin=30m  
| render timechart
```

### <a name="perf-cpu-utilization-graph-per-computer"></a>Graf využití výkonu procesoru na jeden počítač
Tento příklad vypočítá a grafy využití procesoru počítače, které začínají _Contoso_.

```Kusto
Perf
| where TimeGenerated > ago(4h)
| where Computer startswith "Contoso" 
| where CounterName == @"% Processor Time"
| summarize avg(CounterValue) by Computer, bin(TimeGenerated, 15m) 
| render timechart
```

## <a name="protection-status"></a>Stav ochrany

### <a name="computers-with-non-reporting-protection-status-duration"></a>Počítače s doba trvání stavu nehlásících ochrany
Tento příklad zobrazí seznam počítačů, které obsahovaly stavu ochrany _negenerují se sestavy_ a doby trvání, byly v tomto stavu.

```Kusto
ProtectionStatus
| where ProtectionStatus == "Not Reporting"
| summarize count(), startNotReporting = min(TimeGenerated), endNotReporting = max(TimeGenerated) by Computer, ProtectionStatusDetails
| join ProtectionStatus on Computer
| summarize lastReporting = max(TimeGenerated), startNotReporting = any(startNotReporting), endNotReporting = any(endNotReporting) by Computer
| extend durationNotReporting = endNotReporting - startNotReporting
```

### <a name="match-protected-status-records-with-heartbeat-records"></a>Odpovídá chráněné stav záznamů se záznamy prezenčního signálu
Tento příklad vyhledá související ochrany stav záznamů a záznamy prezenčního signálu porovnáváno podle počítače i čas.
Pole s časem se zaokrouhlí na nejbližší minutu pomocí **bin**.

```Kusto
let protection_data = ProtectionStatus
    | project Computer, DetectionId, round_time=bin(TimeGenerated, 1m);
let heartbeat_data = Heartbeat
    | project Computer, Category, round_time=bin(TimeGenerated, 1m);
protection_data | join (heartbeat_data) on Computer, round_time
```


## <a name="security-records"></a>Záznamy zabezpečení

### <a name="count-security-events-by-activity-id"></a>Počet událostí zabezpečení pomocí ID aktivity


V tomto příkladu spoléhá na pevnou struktury **aktivity** sloupec: \<ID\>-\<název\>.
Analyzuje **aktivity** hodnoty do dva nové sloupce a počet výskytů jednotlivých **activityID**.

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m) 
| project Activity 
| parse Activity with activityID " - " activityDesc
| summarize count() by activityID
```

### <a name="count-security-events-related-to-permissions"></a>Počet událostí zabezpečení souvisejících s oprávněními
Tento příklad ukazuje počet **securityEvent** záznamy, ve kterém **aktivity** sloupec obsahuje celé období _oprávnění_. Dotaz se týká záznamy vytvořené za posledních 30 minut.

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m)
| summarize EventCount = countif(Activity has "Permissions")
```

### <a name="find-accounts-that-failed-to-log-in-from-computers-with-a-security-detection"></a>Najít účty, které se nepodařilo přihlásit z počítačů s detekce zabezpečení
Tento příklad vyhledá a počty účty, které se nepodařilo přihlásit z počítačů, na kterých uvedeme detekce zabezpečení.

```Kusto
let detections = toscalar(SecurityDetection
| summarize makeset(Computer));
SecurityEvent
| where Computer in (detections) and EventID == 4624
| summarize count() by Account
```

### <a name="is-my-security-data-available"></a>Je k dispozici data zabezpečení?
Spouští se data často zkoumání začíná Kontrola dostupnosti dat. Tento příklad ukazuje počet **SecurityEvent** záznamy za posledních 30 minut.

```Kusto
SecurityEvent 
| where TimeGenerated  > ago(30m) 
| count
```

### <a name="parse-activity-name-and-id"></a>Parsovat název aktivity a ID
Následující dva příklady spoléhají na pevný struktury **aktivity** sloupec: \<ID\>-\<název\>. V prvním příkladu se používá **analyzovat** operátor přiřazení hodnoty k dva nové sloupce: **activityID** a **activityDesc**.

```Kusto
SecurityEvent
| take 100
| project Activity 
| parse Activity with activityID " - " activityDesc
```

V tomto příkladu **rozdělit** operátor vytvoří pole ze samostatných hodnot
```Kusto
SecurityEvent
| take 100
| project Activity 
| extend activityArr=split(Activity, " - ") 
| project Activity , activityArr, activityId=activityArr[0]
```

### <a name="explicit-credentials-processes"></a>Explicitní přihlašovací údaje procesy
Následující příklad ukazuje výsečový graf procesy, které používaly explicitní přihlašovací údaje za poslední týden

```Kusto
SecurityEvent
| where TimeGenerated > ago(7d)
// filter by id 4648 ("A logon was attempted using explicit credentials")
| where EventID == 4648
| summarize count() by Process
| render piechart 
```

### <a name="top-running-processes"></a>Horní spuštěných procesů

Následující příklad ukazuje časová osa aktivity pro pět nejčastěji používané procesy za poslední tři dny.

```Kusto
// Find all processes that started in the last three days. ID 4688: A new process has been created.
let RunProcesses = 
    SecurityEvent
    | where TimeGenerated > ago(3d)
    | where EventID == "4688";
// Find the 5 processes that were run the most
let Top5Processes =
RunProcesses
| summarize count() by Process
| top 5 by count_;
// Create a time chart of these 5 processes - hour by hour
RunProcesses 
| where Process in (Top5Processes) 
| summarize count() by bin (TimeGenerated, 1h), Process
| render timechart
```


### <a name="find-repeating-failed-login-attempts-by-the-same-account-from-different-ips"></a>Najít s opakováním neúspěšných pokusů o přihlášení pomocí stejného účtu z různých IP adres

Následující příklad vyhledá neúspěšných pokusů o přihlášení pomocí stejného účtu z více než pět různých IP adres za posledních šest hodin.

```Kusto
SecurityEvent 
| where AccountType == "User" and EventID == 4625 and TimeGenerated > ago(6h) 
| summarize IPCount = dcount(IpAddress), makeset(IpAddress)  by Account
| where IPCount > 5
| sort by IPCount desc
```

### <a name="find-user-accounts-that-failed-to-log-in"></a>Nalezení uživatelských účtů, které se nepodařilo přihlásit 
Následující příklad určuje uživatelské účty, které se nepodařilo zaznamenat do protokolu ve víc než pětkrát za poslední den, a při jejich poslední pokus o přihlášení.

```Kusto
let timeframe = 1d;
SecurityEvent
| where TimeGenerated > ago(1d)
| where AccountType == 'User' and EventID == 4625 // 4625 - failed log in
| summarize failed_login_attempts=count(), latest_failed_login=arg_max(TimeGenerated, Account) by Account 
| where failed_login_attempts > 5
| project-away Account1
```

Pomocí **spojení**, a **nechat** příkazy lze kontrolujeme Pokud stejné podezřelé účty byly později moct úspěšně přihlásit.

```Kusto
let timeframe = 1d;
let suspicious_users = 
    SecurityEvent
    | where TimeGenerated > ago(timeframe)
    | where AccountType == 'User' and EventID == 4625 // 4625 - failed login
    | summarize failed_login_attempts=count(), latest_failed_login=arg_max(TimeGenerated, Account) by Account 
    | where failed_login_attempts > 5
    | project-away Account1;
let suspicious_users_that_later_logged_in = 
    suspicious_users 
    | join kind=innerunique (
        SecurityEvent
        | where TimeGenerated > ago(timeframe)
        | where AccountType == 'User' and EventID == 4624 // 4624 - successful login,
        | summarize latest_successful_login=arg_max(TimeGenerated, Account) by Account
    ) on Account
    | extend was_login_after_failures = iif(latest_successful_login>latest_failed_login, 1, 0)
    | where was_login_after_failures == 1
;
suspicious_users_that_later_logged_in
```

## <a name="usage"></a>Využití

### <a name="calculate-the-average-size-of-perf-usage-reports-per-computer"></a>Vypočítá průměrná velikost sestavy o výkonu využití na jeden počítač

Tento příklad vypočítá průměrná velikost sestavy o výkonu využití na jeden počítač, za poslední 3 hodiny.
Výsledky jsou zobrazeny v pruhovém grafu.
```Kusto
Usage 
| where TimeGenerated > ago(3h)
| where DataType == "Perf" 
| where QuantityUnit == "MBytes" 
| summarize avg(Quantity) by Computer
| sort by avg_Quantity desc nulls last
| render barchart
```

### <a name="timechart-latency-percentiles-50-and-95"></a>50 až 95. percentily latence Promítnu

Tento příklad vypočítá a grafy 50. a 95. percentily hlášené **avgLatency** za hodinu za posledních 24 hodin.

```Kusto
Usage
| where TimeGenerated > ago(24h)
| summarize percentiles(AvgLatencyInSeconds, 50, 95) by bin(TimeGenerated, 1h) 
| render timechart
```

### <a name="usage-of-specific-computers-today"></a>Použití určitých počítačích už dnes
Tento příklad načte **využití** data z posledního dne pro názvy počítačů, která obsahuje řetězec _ContosoFile_. Výsledky jsou seřazeny podle **TimeGenerated**.

```Kusto
Usage
| where TimeGenerated > ago(1d)
| where  Computer contains "ContosoFile" 
| sort by TimeGenerated desc nulls last
```

## <a name="updates"></a>Aktualizace

### <a name="computers-still-missing-updates"></a>Počítače stále chybějící aktualizace
Tento příklad zobrazí seznam počítačů, které byly chybí jeden nebo více důležitých aktualizací před pár dny a pořád chybí aktualizace.

```Kusto
let ComputersMissingUpdates3DaysAgo = Update
| where TimeGenerated between (ago(3d)..ago(2d))
| where  Classification == "Critical Updates" and UpdateState != "Not needed" and UpdateState != "NotNeeded"
| summarize makeset(Computer);

Update
| where TimeGenerated > ago(1d)
| where  Classification == "Critical Updates" and UpdateState != "Not needed" and UpdateState != "NotNeeded"
| where Computer in (ComputersMissingUpdates3DaysAgo)
| summarize UniqueUpdatesCount = dcount(Product) by Computer, OSType
```


## <a name="next-steps"></a>Další postup

- Odkazovat [referenční informace k jazyku Kusto](/azure/kusto/query) o na jazyku.
- Projít [lekci o psaní dotazů ve službě Log Analytics](get-started-queries.md).