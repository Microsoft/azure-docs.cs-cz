---
title: Příklady dotazů Azure Monitor protokolu | Microsoft Docs
description: Příklady dotazů protokolu v Azure Monitor pomocí dotazovacího jazyka Kusto
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/16/2020
ms.openlocfilehash: 213437e68efb27c483c8013d209261408ab9d4ea
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100612791"
---
# <a name="azure-monitor-log-query-examples"></a>Příklady dotazů Azure Monitor protokolu
Tento článek obsahuje různé příklady [dotazů](../log-query/log-query-overview.md) pomocí [dotazovacího jazyka Kusto](/azure/kusto/query/) k načtení různých typů dat protokolu z Azure monitor. Pro konsolidaci a analýzu dat se používají různé metody, takže tyto ukázky můžete použít k identifikaci různých strategií, které můžete použít pro vlastní požadavky.  

Podrobnosti o různých klíčových slovech použitých v těchto ukázkách najdete v referenčních informacích k [jazyku Kusto](/azure/kusto/query/) . Pokud jste Azure Monitor, Projděte si [lekci o vytváření dotazů](../log-query/get-started-queries.md) .

## <a name="events"></a>Události

### <a name="search-application-level-events-described-as-cryptographic"></a>Prohledat události na úrovni aplikace popsané jako "kryptografické"
Tento příklad vyhledá v tabulce **událostí** záznamy, ve kterých je protokol **událostí** _aplikace_ a **RenderedDescription** obsahuje _kryptografii_. Obsahuje záznamy za posledních 24 hodin.

```Kusto
Event
| where EventLog == "Application" 
| where TimeGenerated > ago(24h) 
| where RenderedDescription contains "cryptographic"
```

### <a name="search-events-related-to-unmarshaling"></a>Hledat události související s zařazováním
**Události** a **SecurityEvents** vyhledávacích tabulek pro záznamy, které zmiňují _zařazování_.

```Kusto
search in (Event, SecurityEvent) "unmarshaling"
```

## <a name="heartbeat"></a>Tep

### <a name="chart-a-week-over-week-view-of-the-number-of-computers-sending-data"></a>Vytvoření grafu zobrazení počtu počítačů, které odesílají data z týdenního týdne

Následující příklad vyjedná počet různých počítačů, které odeslaly prezenční signály, každý týden.

```Kusto
Heartbeat
| where TimeGenerated >= startofweek(ago(21d))
| summarize dcount(Computer) by endofweek(TimeGenerated) | render barchart kind=default
```

### <a name="find-stale-computers"></a>Najít zastaralé počítače

Následující příklad vyhledá počítače, které byly aktivní během posledního dne, ale během poslední hodiny neodeslaly prezenční signály.

```Kusto
Heartbeat
| where TimeGenerated > ago(1d)
| summarize LastHeartbeat = max(TimeGenerated) by Computer
| where isnotempty(Computer)
| where LastHeartbeat < ago(1h)
```

### <a name="get-the-latest-heartbeat-record-per-computer-ip"></a>Získat nejnovější záznam prezenčního signálu na IP adresu počítače

Tento příklad vrátí poslední záznam prezenčního signálu pro každou IP adresu počítače.
```Kusto
Heartbeat
| summarize arg_max(TimeGenerated, *) by ComputerIP
```

### <a name="match-protected-status-records-with-heartbeat-records"></a>Vyhledání chráněných záznamů o stavu pomocí záznamů prezenčního signálu

Tento příklad najde související záznamy stavu ochrany a záznamy prezenčního signálu, které odpovídají v počítači i v čase.
Všimněte si, že pole čas se zaokrouhluje na nejbližší minutu. Použili jsme výpočet běhového přihrádky k tomu, aby: `round_time=bin(TimeGenerated, 1m)` .

```Kusto
let protection_data = ProtectionStatus
    | project Computer, DetectionId, round_time=bin(TimeGenerated, 1m);
let heartbeat_data = Heartbeat
    | project Computer, Category, round_time=bin(TimeGenerated, 1m);
protection_data | join (heartbeat_data) on Computer, round_time
```

### <a name="server-availability-rate"></a>Rychlost dostupnosti serveru

Vypočítá sazbu dostupnosti serveru na základě záznamů prezenčního signálu. Dostupnost je definována jako "minimálně 1 prezenční signál za hodinu".
Takže pokud byl server dostupný 98 až 100 hodin, sazba dostupnosti je 98%.

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

### <a name="chart-the-record-count-per-table"></a>Vytvoření grafu počtu záznamů na tabulku
Následující příklad shromáždí všechny záznamy všech tabulek za posledních pět hodin a spočítá počet záznamů v každé tabulce. Výsledky se zobrazují v timechart.

```Kusto
union withsource=sourceTable *
| where TimeGenerated > ago(5h) 
| summarize count() by bin(TimeGenerated,10m), sourceTable
| render timechart
```

### <a name="count-all-logs-collected-over-the-last-hour-by-type"></a>Spočítat všechny protokoly shromážděné za poslední hodinu podle typu
Následující příklad vyhledá vše hlášené za poslední hodinu a spočítá záznamy jednotlivých tabulek podle **typu**. Výsledky se zobrazí v pruhovém grafu.

```Kusto
search *
| where TimeGenerated > ago(1h) 
| summarize CountOfRecords = count() by Type
| render barchart
```

## <a name="azurediagnostics"></a>AzureDiagnostics

### <a name="count-azure-diagnostics-records-per-category"></a>Počet záznamů diagnostiky Azure na kategorii
Tento příklad počítá všechny záznamy diagnostiky Azure pro každou jedinečnou kategorii.

```Kusto
AzureDiagnostics 
| where TimeGenerated > ago(1d)
| summarize count() by Category
```

### <a name="get-a-random-record-for-each-unique-category"></a>Získat náhodný záznam pro každou jedinečnou kategorii
Tento příklad načte jeden náhodný záznam Azure Diagnostics pro každou jedinečnou kategorii.

```Kusto
AzureDiagnostics
| where TimeGenerated > ago(1d) 
| summarize any(*) by Category
```

### <a name="get-the-latest-record-per-category"></a>Získá nejnovější záznam na kategorii.
Tento příklad načte nejnovější záznam Azure Diagnostics v každé jedinečné kategorii.

```Kusto
AzureDiagnostics
| where TimeGenerated > ago(1d) 
| summarize arg_max(TimeGenerated, *) by Category
```

## <a name="network-monitoring"></a>Monitorování sítě

### <a name="computers-with-unhealthy-latency"></a>Počítače s nestavovou latencí
Tento příklad vytvoří seznam různých počítačů s nestavovou latencí.

```Kusto
NetworkMonitoring 
| where LatencyHealthState <> "Healthy" 
| where Computer != "" 
| distinct Computer
```

## <a name="performance"></a>Výkon

### <a name="join-computer-perf-records-to-correlate-memory-and-cpu"></a>Připojit záznamy o výkonu počítače ke korelaci paměti a procesoru
Tento příklad koreluje záznamy **výkonu** konkrétního počítače a vytvoří dva časové grafy, průměrný procesor a maximální velikost paměti.

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

### <a name="perf-cpu-utilization-graph-per-computer"></a>Graf využití výkonu procesoru na počítač
Tento příklad vypočítá a segrafuje využití procesoru u počítačů, které začínají na _společnosti Contoso_.

```Kusto
Perf
| where TimeGenerated > ago(4h)
| where Computer startswith "Contoso" 
| where CounterName == @"% Processor Time"
| summarize avg(CounterValue) by Computer, bin(TimeGenerated, 15m) 
| render timechart
```

## <a name="protection-status"></a>Stav ochrany

### <a name="computers-with-non-reporting-protection-status-duration"></a>Počítače s dobou trvání stavu ochrany bez sestav
Tento příklad obsahuje seznam počítačů, které mají stav ochrany _bez sestav_  , a dobu, po kterou byly v tomto stavu.

```Kusto
ProtectionStatus
| where ProtectionStatus == "Not Reporting"
| summarize count(), startNotReporting = min(TimeGenerated), endNotReporting = max(TimeGenerated) by Computer, ProtectionStatusDetails
| join ProtectionStatus on Computer
| summarize lastReporting = max(TimeGenerated), startNotReporting = any(startNotReporting), endNotReporting = any(endNotReporting) by Computer
| extend durationNotReporting = endNotReporting - startNotReporting
```

### <a name="match-protected-status-records-with-heartbeat-records"></a>Vyhledání chráněných záznamů o stavu pomocí záznamů prezenčního signálu
Tento příklad najde související záznamy stavu ochrany a záznamy prezenčního signálu se shodují na počítači i v čase.
Pole čas se zaokrouhluje na nejbližší minutu pomocí **přihrádky**.

```Kusto
let protection_data = ProtectionStatus
    | project Computer, DetectionId, round_time=bin(TimeGenerated, 1m);
let heartbeat_data = Heartbeat
    | project Computer, Category, round_time=bin(TimeGenerated, 1m);
protection_data | join (heartbeat_data) on Computer, round_time
```


## <a name="security-records"></a>Záznamy zabezpečení

### <a name="count-security-events-by-activity-id"></a>Počet událostí zabezpečení podle ID aktivity


Tento příklad spoléhá na pevnou strukturu sloupce **aktivita** : \<ID\> - \<Name\> .
Analyzuje hodnotu **aktivity** na dva nové sloupce a spočítá výskyt jednotlivých **ActivityId**.

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m) 
| project Activity 
| parse Activity with activityID " - " activityDesc
| summarize count() by activityID
```

### <a name="count-security-events-related-to-permissions"></a>Spočítat události zabezpečení související s oprávněními
Tento příklad ukazuje počet záznamů **securityEvent** , ve kterých sloupec **Activity** obsahuje veškerá _oprávnění_. Dotaz se vztahuje na záznamy vytvořené za posledních 30 minut.

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m)
| summarize EventCount = countif(Activity has "Permissions")
```

### <a name="find-accounts-that-failed-to-log-in-from-computers-with-a-security-detection"></a>Vyhledání účtů, kterým se nepodařilo přihlásit z počítačů s detekcí zabezpečení
Tento příklad najde a spočítá účty, které selhaly při přihlášení z počítačů, ve kterých identifikujeme detekci zabezpečení.

```Kusto
let detections = toscalar(SecurityDetection
| summarize makeset(Computer));
SecurityEvent
| where Computer in (detections) and EventID == 4624
| summarize count() by Account
```

### <a name="is-my-security-data-available"></a>Jsou k dispozici moje data zabezpečení?
Spuštění průzkumu dat často začíná kontrolou dostupnosti dat. Tento příklad zobrazuje počet záznamů **SecurityEvent** za posledních 30 minut.

```Kusto
SecurityEvent 
| where TimeGenerated  > ago(30m) 
| count
```

### <a name="parse-activity-name-and-id"></a>Název a ID aktivity analýzy
Následující dva příklady jsou závislé na pevné struktuře sloupce **aktivita** : \<ID\> - \<Name\> . První příklad používá operátor **Parse** k přiřazení hodnot dvěma novým sloupcům: **ActivityId** a **activityDesc**.

```Kusto
SecurityEvent
| take 100
| project Activity 
| parse Activity with activityID " - " activityDesc
```

V tomto příkladu se používá operátor **Split** k vytvoření pole samostatných hodnot.
```Kusto
SecurityEvent
| take 100
| project Activity 
| extend activityArr=split(Activity, " - ") 
| project Activity , activityArr, activityId=activityArr[0]
```

### <a name="explicit-credentials-processes"></a>Explicitní procesy přihlašovacích údajů
Následující příklad ukazuje výsečový graf procesů, které používaly explicitní přihlašovací údaje za poslední týden.

```Kusto
SecurityEvent
| where TimeGenerated > ago(7d)
// filter by id 4648 ("A logon was attempted using explicit credentials")
| where EventID == 4648
| summarize count() by Process
| render piechart 
```

### <a name="top-running-processes"></a>Nejvyšší spuštěné procesy

Následující příklad znázorňuje časovou aktivitu pro pět nejběžnějších procesů za poslední tři dny.

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


### <a name="find-repeating-failed-login-attempts-by-the-same-account-from-different-ips"></a>Hledání opakujících se neúspěšných pokusů o přihlášení ze stejného účtu z různých IP adres

Následující příklad vyhledá neúspěšné pokusy o přihlášení stejného účtu z více než pěti různých IP adres za posledních šest hodin.

```Kusto
SecurityEvent 
| where AccountType == "User" and EventID == 4625 and TimeGenerated > ago(6h) 
| summarize IPCount = dcount(IpAddress), makeset(IpAddress)  by Account
| where IPCount > 5
| sort by IPCount desc
```

### <a name="find-user-accounts-that-failed-to-log-in"></a>Vyhledání uživatelských účtů, u kterých se nezdařilo přihlášení 
Následující příklad identifikuje uživatelské účty, u kterých se během posledního dne nepodařilo přihlásit více než pět časů a kdy se poslední pokus o přihlášení.

```Kusto
let timeframe = 1d;
SecurityEvent
| where TimeGenerated > ago(1d)
| where AccountType == 'User' and EventID == 4625 // 4625 - failed log in
| summarize failed_login_attempts=count(), latest_failed_login=arg_max(TimeGenerated, Account) by Account 
| where failed_login_attempts > 5
| project-away Account1
```

Pomocí příkazu **Join** a **nechejte** příkazy, které můžeme ověřit, zda byly stejné podezřelé účty později schopny úspěšně přihlásit.

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

`Usage`Datový typ lze použít ke sledování přijímaných objemů dat podle řešení nebo datového typu. Existují další techniky, jak prozkoumat ingestované datové svazky podle [počítače](../platform/manage-cost-storage.md#data-volume-by-computer) nebo [předplatného Azure, skupiny prostředků nebo prostředku](../platform/manage-cost-storage.md#data-volume-by-azure-resource-resource-group-or-subscription).

#### <a name="data-volume-by-solution"></a>Objem dat podle řešení

Dotaz použitý k zobrazení objemu fakturovatelných dat podle řešení za poslední měsíc (s výjimkou posledního částečného dne) je:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), Solution | render barchart
```

Všimněte si, že klauzule `where IsBillable = true` filtruje datové typy z určitých řešení, pro které se neúčtují žádné poplatky za ingestování.  Také klauzule WITH `TimeGenerated` je určena pouze k zajištění toho, aby se možnosti dotazování v Azure Portal prohledaly až po dobu 24 hodin. Při použití datového typu použití `StartTime` a `EndTime` představují časové intervaly, pro které jsou zobrazeny výsledky. 

#### <a name="data-volume-by-type"></a>Objem dat podle typu

Další podrobnosti můžete prozkoumat a zobrazit tak trendy dat pro datový typ:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), DataType | render barchart
```

Nebo pokud chcete zobrazit tabulku podle řešení a typu za poslední měsíc,

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by Solution, DataType
| sort by Solution asc, DataType asc
```

> [!NOTE]
> Některá pole datového typu použití, ale stále ve schématu, jsou zastaralá a jejich hodnoty se už neplní. Jedná se o **počítač** a pole související s ingestování (**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**, **BatchesCapped** a **AverageProcessingTimeMs**).

## <a name="updates"></a>Aktualizace

### <a name="computers-still-missing-updates"></a>Počítače pořád scházejí aktualizace
V tomto příkladu se zobrazí seznam počítačů, ve kterých chybí jedna nebo několik důležitých aktualizací před několika dny a stále chybí aktualizace.

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

- Podrobnosti o jazyku najdete v referenčních informacích k [jazyku Kusto](/azure/kusto/query) .
- Projděte si [lekci o zápisu dotazů protokolu v Azure monitor](../log-query/get-started-queries.md).
