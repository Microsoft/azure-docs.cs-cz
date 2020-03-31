---
title: Příklady dotazu na protokol Azure Monitor | Dokumenty společnosti Microsoft
description: Příklady dotazů protokolu v Azure Monitoru pomocí dotazovacího jazyka Kusto.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/16/2020
ms.openlocfilehash: 18cd74ac9298b7dd058de2b224f677ec0d8f2d64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480279"
---
# <a name="azure-monitor-log-query-examples"></a>Příklady dotazu protokolu Azure Monitor
Tento článek obsahuje různé [příklady dotazů](log-query-overview.md) pomocí [dotazovacího jazyka Kusto](/azure/kusto/query/) k načtení různých typů dat protokolu z Azure Monitoru. Ke konsolidaci a analýze dat se používají různé metody, takže tyto ukázky můžete použít k identifikaci různých strategií, které můžete použít pro vlastní požadavky.  

Podrobnosti o různých klíčových slovech použitých v těchto ukázkách naleznete v [referenční příručce v jazyce Kusto.](https://docs.microsoft.com/azure/kusto/query/) Pokud s Azure Monitorem tečte [lekci o vytváření dotazů.](get-started-queries.md)

## <a name="events"></a>Události

### <a name="search-application-level-events-described-as-cryptographic"></a>Hledat události na úrovni aplikace popsané jako "Kryptografické"
Tento příklad vyhledá v tabulce **Události** záznamy, ve kterých je **EventLog** _aplikace_ a **RenderedDescription** obsahuje _kryptografické_. Zahrnuje záznamy za posledních 24 hodin.

```Kusto
Event
| where EventLog == "Application" 
| where TimeGenerated > ago(24h) 
| where RenderedDescription contains "cryptographic"
```

### <a name="search-events-related-to-unmarshaling"></a>Vyhledávací události související s odmarshalcováním
Hledat tabulky **Event** a **SecurityEvents** pro záznamy, které zmiňují _unmarshaling_.

```Kusto
search in (Event, SecurityEvent) "unmarshaling"
```

## <a name="heartbeat"></a>Prezenční signál

### <a name="chart-a-week-over-week-view-of-the-number-of-computers-sending-data"></a>Graf týdenního zobrazení počtu počítačů odesílajících data

Následující příklad mapuje počet různých počítačů, které každý týden odeslaly prezenční signály.

```Kusto
Heartbeat
| where TimeGenerated >= startofweek(ago(21d))
| summarize dcount(Computer) by endofweek(TimeGenerated) | render barchart kind=default
```

### <a name="find-stale-computers"></a>Najít zastaralé počítače

Následující příklad vyhledá počítače, které byly aktivní v poslední den, ale neodeslal prezenční signály za poslední hodinu.

```Kusto
Heartbeat
| where TimeGenerated > ago(1d)
| summarize LastHeartbeat = max(TimeGenerated) by Computer
| where isnotempty(Computer)
| where LastHeartbeat < ago(1h)
```

### <a name="get-the-latest-heartbeat-record-per-computer-ip"></a>Získání nejnovějšího záznamu prezenčního signálu na IP počítač

Tento příklad vrátí poslední záznam prezenčního signálu pro každou ip adresu počítače.
```Kusto
Heartbeat
| summarize arg_max(TimeGenerated, *) by ComputerIP
```

### <a name="match-protected-status-records-with-heartbeat-records"></a>Shoda chráněných záznamů stavu se záznamy prezenčního signálu

Tento příklad vyhledá související záznamy stavu ochrany a záznamy prezenčního signálu, odpovídající v počítači i v čase.
Všimněte si, že časové pole je zaokrouhleno na nejbližší minutu. K tomu jsme použili výpočet `round_time=bin(TimeGenerated, 1m)`přihrádky za běhu: .

```Kusto
let protection_data = ProtectionStatus
    | project Computer, DetectionId, round_time=bin(TimeGenerated, 1m);
let heartbeat_data = Heartbeat
    | project Computer, Category, round_time=bin(TimeGenerated, 1m);
protection_data | join (heartbeat_data) on Computer, round_time
```

### <a name="server-availability-rate"></a>Míra dostupnosti serveru

Výpočet míry dostupnosti serveru na základě záznamů prezenčního signálu. Dostupnost je definována jako "alespoň 1 srdeční tep za hodinu".
Pokud byl tedy server k dispozici 98 ze 100 hodin, je míra dostupnosti 98 %.

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


## <a name="multiple-data-types"></a>Více datových typů

### <a name="chart-the-record-count-per-table"></a>Graf počtu záznamů pro tabulku
Následující příklad shromažďuje všechny záznamy všech tabulek za posledních pět hodin a spočítá, kolik záznamů bylo v každé tabulce. Výsledky jsou zobrazeny v časovém diagramu.

```Kusto
union withsource=sourceTable *
| where TimeGenerated > ago(5h) 
| summarize count() by bin(TimeGenerated,10m), sourceTable
| render timechart
```

### <a name="count-all-logs-collected-over-the-last-hour-by-type"></a>Počítat všechny protokoly shromážděné za poslední hodinu podle typu
Následující příklad vyhledá vše, co bylo hlášeno za poslední hodinu, a spočítá záznamy každé tabulky podle **typu**. Výsledky jsou zobrazeny v pruhovém grafu.

```Kusto
search *
| where TimeGenerated > ago(1h) 
| summarize CountOfRecords = count() by Type
| render barchart
```

## <a name="azurediagnostics"></a>AzureDiagnostics

### <a name="count-azure-diagnostics-records-per-category"></a>Počítat diagnostické záznamy Azure podle kategorií
Tento příklad počítá všechny záznamy diagnostiky Azure pro každou jedinečnou kategorii.

```Kusto
AzureDiagnostics 
| where TimeGenerated > ago(1d)
| summarize count() by Category
```

### <a name="get-a-random-record-for-each-unique-category"></a>Získání náhodného záznamu pro každou jedinečnou kategorii
Tento příklad získá jeden náhodný záznam diagnostiky Azure pro každou jedinečnou kategorii.

```Kusto
AzureDiagnostics
| where TimeGenerated > ago(1d) 
| summarize any(*) by Category
```

### <a name="get-the-latest-record-per-category"></a>Získejte nejnovější záznam podle kategorie
Tento příklad získá nejnovější záznam diagnostiky Azure v každé jedinečné kategorii.

```Kusto
AzureDiagnostics
| where TimeGenerated > ago(1d) 
| summarize arg_max(TimeGenerated, *) by Category
```

## <a name="network-monitoring"></a>Monitorování sítě

### <a name="computers-with-unhealthy-latency"></a>Počítače s latencí v nepořádku
Tento příklad vytvoří seznam různých počítačů s latencí není v pořádku.

```Kusto
NetworkMonitoring 
| where LatencyHealthState <> "Healthy" 
| where Computer != "" 
| distinct Computer
```

## <a name="performance"></a>Výkon

### <a name="join-computer-perf-records-to-correlate-memory-and-cpu"></a>Připojte se k záznamům perf počítače a korelujte paměť a procesor
Tento příklad koreluje záznamy **perf** konkrétního počítače a vytvoří dva časové grafy, průměrný procesor a maximální paměť.

```Kusto
let StartTime = now()-5d;
let EndTime = now()-4d;
Perf
| where CounterName == "% Processor Time"  
| where TimeGenerated > StartTime and TimeGenerated < EndTime
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

### <a name="perf-cpu-utilization-graph-per-computer"></a>Graf využití procesoru Perf na počítač
Tento příklad vypočítá a vymapuje využití procesoru počítačů, které začínají na _Contoso_.

```Kusto
Perf
| where TimeGenerated > ago(4h)
| where Computer startswith "Contoso" 
| where CounterName == @"% Processor Time"
| summarize avg(CounterValue) by Computer, bin(TimeGenerated, 15m) 
| render timechart
```

## <a name="protection-status"></a>Stav ochrany

### <a name="computers-with-non-reporting-protection-status-duration"></a>Počítače s dobou trvání stavu ochrany bez hlášení
V tomto příkladu jsou uvedeny počítače, které měly stav ochrany _Nenahlášení_ a dobu jejich trvání v tomto stavu.

```Kusto
ProtectionStatus
| where ProtectionStatus == "Not Reporting"
| summarize count(), startNotReporting = min(TimeGenerated), endNotReporting = max(TimeGenerated) by Computer, ProtectionStatusDetails
| join ProtectionStatus on Computer
| summarize lastReporting = max(TimeGenerated), startNotReporting = any(startNotReporting), endNotReporting = any(endNotReporting) by Computer
| extend durationNotReporting = endNotReporting - startNotReporting
```

### <a name="match-protected-status-records-with-heartbeat-records"></a>Shoda chráněných záznamů stavu se záznamy prezenčního signálu
Tento příklad vyhledá související záznamy stavu ochrany a prezenčního signálu odpovídající počítači i času.
Časové pole je zaokrouhleno na nejbližší minutu pomocí **přihrádky**.

```Kusto
let protection_data = ProtectionStatus
    | project Computer, DetectionId, round_time=bin(TimeGenerated, 1m);
let heartbeat_data = Heartbeat
    | project Computer, Category, round_time=bin(TimeGenerated, 1m);
protection_data | join (heartbeat_data) on Computer, round_time
```


## <a name="security-records"></a>Bezpečnostní záznamy

### <a name="count-security-events-by-activity-id"></a>Počítat události zabezpečení podle ID aktivity


Tento příklad závisí na pevné struktuře \<sloupce\>-\<\> **Aktivita:** Název ID .
Analyzuje **Activity** hodnotu do dvou nových sloupců a spočítá výskyt každé **activityID**.

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m) 
| project Activity 
| parse Activity with activityID " - " activityDesc
| summarize count() by activityID
```

### <a name="count-security-events-related-to-permissions"></a>Počítat události zabezpečení související s oprávněními
Tento příklad ukazuje počet záznamů **securityEvent,** ve kterých sloupec **Aktivita** obsahuje celý termín _Oprávnění_. Dotaz se vztahuje na záznamy vytvořené za posledních 30 minut.

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m)
| summarize EventCount = countif(Activity has "Permissions")
```

### <a name="find-accounts-that-failed-to-log-in-from-computers-with-a-security-detection"></a>Vyhledání účtů, kterým se nepodařilo přihlásit z počítačů se zjišťováním zabezpečení
Tento příklad vyhledá a spočítá účty, které se nepodařilo přihlásit z počítačů, ve kterých identifikujeme zjišťování zabezpečení.

```Kusto
let detections = toscalar(SecurityDetection
| summarize makeset(Computer));
SecurityEvent
| where Computer in (detections) and EventID == 4624
| summarize count() by Account
```

### <a name="is-my-security-data-available"></a>Jsou k dispozici moje bezpečnostní údaje?
Spuštění zkoumání dat často začíná kontrolou dostupnosti dat. Tento příklad ukazuje počet záznamů **SecurityEvent** za posledních 30 minut.

```Kusto
SecurityEvent 
| where TimeGenerated  > ago(30m) 
| count
```

### <a name="parse-activity-name-and-id"></a>Analyzovat název a ID aktivity
Dva níže uvedené příklady spoléhají na pevnou strukturu\>-\<\>sloupce **Aktivita:** \<Název ID . První příklad používá operátor **parse** k přiřazení hodnot dvěma novým sloupcům: **activityID** a **activityDesc**.

```Kusto
SecurityEvent
| take 100
| project Activity 
| parse Activity with activityID " - " activityDesc
```

Tento příklad používá **operátor rozdělení** k vytvoření pole samostatných hodnot.
```Kusto
SecurityEvent
| take 100
| project Activity 
| extend activityArr=split(Activity, " - ") 
| project Activity , activityArr, activityId=activityArr[0]
```

### <a name="explicit-credentials-processes"></a>Explicitní procesy pověření
Následující příklad ukazuje výsečový graf procesů, které používaly explicitní pověření v posledním týdnu

```Kusto
SecurityEvent
| where TimeGenerated > ago(7d)
// filter by id 4648 ("A logon was attempted using explicit credentials")
| where EventID == 4648
| summarize count() by Process
| render piechart 
```

### <a name="top-running-processes"></a>Nejlepší běžící procesy

Následující příklad ukazuje časovou osu aktivity pro pět nejběžnějších procesů za poslední tři dny.

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


### <a name="find-repeating-failed-login-attempts-by-the-same-account-from-different-ips"></a>Vyhledání opakovaných neúspěšných pokusů o přihlášení stejným účtem z různých IP služeb

Následující příklad vyhledá neúspěšné pokusy o přihlášení stejným účtem z více než pěti různých IP adresy za posledních šest hodin.

```Kusto
SecurityEvent 
| where AccountType == "User" and EventID == 4625 and TimeGenerated > ago(6h) 
| summarize IPCount = dcount(IpAddress), makeset(IpAddress)  by Account
| where IPCount > 5
| sort by IPCount desc
```

### <a name="find-user-accounts-that-failed-to-log-in"></a>Vyhledání uživatelských účtů, kterým se nepodařilo přihlásit 
Následující příklad identifikuje uživatelské účty, které se nepodařilo přihlásit více než pětkrát za poslední den a při posledním pokusu o přihlášení.

```Kusto
let timeframe = 1d;
SecurityEvent
| where TimeGenerated > ago(1d)
| where AccountType == 'User' and EventID == 4625 // 4625 - failed log in
| summarize failed_login_attempts=count(), latest_failed_login=arg_max(TimeGenerated, Account) by Account 
| where failed_login_attempts > 5
| project-away Account1
```

Pomocí **join**a **nechat** příkazy můžeme zkontrolovat, zda stejné podezřelé účty byly později schopni se úspěšně přihlásit.

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

Datový `Usage` typ lze použít ke sledování objemu ingestovaných dat podle řešení nebo datového typu. Existují i jiné techniky pro studium svazků ingdovaných dat podle [počítače](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#data-volume-by-computer) nebo [předplatného Azure, skupiny prostředků nebo prostředku](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#data-volume-by-azure-resource-resource-group-or-subscription).

#### <a name="data-volume-by-solution"></a>Objem dat podle řešení

Dotaz použitý k zobrazení fakturovatelného objemu dat podle řešení za poslední měsíc (s výjimkou posledního částečného dne) je:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), Solution | render barchart
```

Všimněte si, že klauzule `where IsBillable = true` filtruje datové typy z určitých řešení, pro které není účtován poplatek za ingestování.  Také klauzule `TimeGenerated` s je pouze zajistit, že prostředí dotazů na portálu Azure bude hledět zpět za výchozí 24 hodin. Při použití datového `StartTime` typu `EndTime` Využití a představují časové intervaly, pro které jsou prezentovány výsledky. 

#### <a name="data-volume-by-type"></a>Objem dat podle typu

Můžete přejít k podrobnostem a zobrazit trendy dat podle typu dat:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), DataType | render barchart
```

Nebo chcete-li zobrazit tabulku podle řešení a zadejte za poslední měsíc,

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by Solution, DataType
| sort by Solution asc, DataType asc
```

> [!NOTE]
> Některá pole datového typu Využití, zatímco stále ve schématu, byly zastaralé a budou jejich hodnoty již naplněny. Jedná **se o počítač,** stejně jako pole související s požitím (**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**, **BatchesCapped** a **AverageProcessingTimeMs**.

## <a name="updates"></a>Aktualizace

### <a name="computers-still-missing-updates"></a>Počítače stále chybí aktualizace
Tento příklad ukazuje seznam počítačů, kterým před několika dny chyběla jedna nebo více důležitých aktualizací a stále chybí aktualizace.

```Kusto
let ComputersMissingUpdates3DaysAgo = Update
| where TimeGenerated between (ago(30d)..ago(1h))
| where Classification !has "Critical" and UpdateState =~ "Needed"
| summarize makeset(Computer);
Update
| where TimeGenerated > ago(1d)
| where Classification has "Critical" and UpdateState =~ "Needed"
| where Computer in (ComputersMissingUpdates3DaysAgo)
| summarize UniqueUpdatesCount = dcount(Product) by Computer, OSType
```


## <a name="next-steps"></a>Další kroky

- Podrobnosti o jazyce naleznete v referenční příručce k [jazyku Kusto.](/azure/kusto/query)
- Projděte si [lekci o zápisu dotazů protokolu v Azure Monitoru](get-started-queries.md).