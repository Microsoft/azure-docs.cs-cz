---
title: Analýza využití dat v Log Analytics | Dokumentace Microsoftu
description: Použijte řídicí panel Využití a odhad nákladů v Log Analytics k vyhodnocení objemu dat odesílaných do Log Analytics a identifikaci možných příčin jejich neočekávaných nárůstů.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 74d0adcb-4dc2-425e-8b62-c65537cef270
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/11/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: c72e1c92815f70838db20ab67c3f70fc5223ac03
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52964743"
---
# <a name="analyze-data-usage-in-log-analytics"></a>Analýza využití dat v Log Analytics

> [!NOTE]
> Tento článek popisuje, jak analyzovat využití dat v Log Analytics.  Přečtěte si následující články související informace.
> - [Spravovat náklady pomocí řízení objemu dat a uchovávání dat v Log Analytics](log-analytics-manage-cost-storage.md) popisuje, jak řídit své náklady tak, že změníte vaše data uchovávají.
> - [Monitorování využití a odhadované náklady](../monitoring-and-diagnostics/monitoring-usage-and-estimated-costs.md) popisuje, jak zobrazit využití a odhadované náklady napříč více funkcí pro různé cenové modely pro monitorování Azure. Také popisuje, jak změnit cenový model.

## <a name="understand-usage"></a>Vysvětlení využití

Použití **využití Log Analytics a odhadované náklady** můžete zkontrolovat a analyzovat data využití. Ukazuje, kolik dat shromažďují jednotlivá řešení, kolik dat je uchovávané a odhad nákladů na základě objemu dat přijatých a jakékoli další uchování nad rámec objemu zahrnutého v ceně.

![Využití a odhadované náklady](media/log-analytics-usage/usage-estimated-cost-dashboard-01.png)<br>

Prozkoumat data podrobněji, klikněte na ikonu v horní části napravo od buď z grafů v **využití a odhadované náklady** stránky. Teď můžete pracovat s Tento dotaz a prozkoumejte podrobnosti o využití.  

![Zobrazení protokolů](media/log-analytics-usage/logs.png)<br>

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>Řešení potíží způsobujících větší využití, než se čekalo
Větší využití je způsobeno jedním nebo obojím z těchto aspektů:
- Do Log Analytics se odesílá více dat, než se čekalo.
- Více uzlů, než očekávané odesílání dat do služby Log Analytics nebo některé uzly posílají více dat než obvykle.

Pojďme se podívat, jak jsme se dozvíte o oba z těchto příčin. 

> [!NOTE]
> Některá pole datového typu využití při pořád ve schématu, jsou zastaralé a jejich hodnoty jsou již nejsou naplněny. Jedná se o **počítače** a také související s příjmem pole (**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**,  **BatchesCapped** a **AverageProcessingTimeMs**.
> Níže jsou nový způsob k dotazování objem přijatých dat na jeden počítač. 

### <a name="data-volume"></a>Objem dat 
Na **využití a odhadované náklady** stránky, *příjem dat podle řešení* graf ukazuje celkový objem dat odesílaných a kolik je odesíláno každé řešení. Díky tomu můžete určit trendy, jako je například, jestli se rozrůstá celkové využití dat (nebo využití podle konkrétního řešení), zbývající konstantní nebo se snižuje. Query sloužící ke generování to je

`Usage| where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| summarize TotalVolumeGB = sum(Quantity) / 1024 by bin(TimeGenerated, 1d), Solution| render barchart`

Všimněte si, že v klauzuli "kde IsBillable = true" filtruje z určité řešení, pro které neplatí žádné poplatky ingestování datových typů. 

Můžete přejít na trendy v datech najdete konkrétní datové typy, například pokud chcete zkoumat data z důvodu protokoly služby IIS:

`Usage| where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| where DataType == "W3CIISLog"
| summarize TotalVolumeGB = sum(Quantity) / 1024 by bin(TimeGenerated, 1d), Solution| render barchart`

### <a name="nodes-sending-data"></a>Uzlů odesílajících data

Chcete-li pochopit počet počítačů (uzlů) vykazujících data za poslední měsíc, použijte

`Heartbeat | where TimeGenerated > startofday(ago(31d))
| summarize dcount(ComputerIP) by bin(TimeGenerated, 1d)    
| render timechart`

Pokud chcete zobrazit **velikost** účtovaných událostí může ingestovat počítače, použijte

`union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  Computer | sort by Bytes nulls last `

Tyto dotazy používejte opatrně, jsou náročné ke spuštění kontrol napříč datové typy. Tento dotaz nahrazuje starý způsob dotazování to s datovým typem využití. 

Pokud chcete zobrazit **počet** událostí může ingestovat počítače, použijte

`union withsource = tt *
| summarize count() by Computer | sort by count_ nulls last`

Pokud chcete zobrazit počet účtovaných událostí může ingestovat počítače, použijte 

`union withsource = tt * 
| where _IsBillable == true 
| summarize count() by Computer  | sort by count_ nulls last`

Pokud chcete vidět, že počet účtovaných datové typy jsou odesílání dat k určitému počítači, použijte:

`union withsource = tt *
| where Computer == "*computer name*"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last `

Pokud chcete dostat hlouběji do zdroje dat pro konkrétní datový typ, tady jsou některé užitečné příklady dotazů:

+ Řešení **zabezpečení**
  - `SecurityEvent | summarize AggregatedValue = count() by EventID`
+ Řešení **pro správu protokolů**
  - `Usage | where Solution == "LogManagement" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | summarize AggregatedValue = count() by DataType`
+ Datový typ **Perf**
  - `Perf | summarize AggregatedValue = count() by CounterPath`
  - `Perf | summarize AggregatedValue = count() by CounterName`
+ Datový typ **Event**
  - `Event | summarize AggregatedValue = count() by EventID`
  - `Event | summarize AggregatedValue = count() by EventLog, EventLevelName`
+ Datový typ **Syslog**
  - `Syslog | summarize AggregatedValue = count() by Facility, SeverityLevel`
  - `Syslog | summarize AggregatedValue = count() by ProcessName`
+ Datový typ **AzureDiagnostics**
  - `AzureDiagnostics | summarize AggregatedValue = count() by ResourceProvider, ResourceId`

### <a name="tips-for-reducing-data-volume"></a>Tipy pro snížení objemu dat

Některé návrhy pro snížení objemu shromažďovaných protokolů zahrnují:

| Zdroj velkého objemu dat | Postup snížení objemu dat |
| -------------------------- | ------------------------- |
| Události zabezpečení            | Vyberte [běžné nebo minimální události zabezpečení](https://blogs.technet.microsoft.com/msoms/2016/11/08/filter-the-security-events-the-oms-security-collects/). <br> Změňte zásady auditu zabezpečení tak, aby se shromažďovaly jenom potřebné události. Zaměřte se hlavně na potřebu shromažďovat události pro <br> - [audit platformy Filtering Platform](https://technet.microsoft.com/library/dd772749(WS.10).aspx) <br> - [audit registru](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10))<br> - [audit systému souborů](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10))<br> - [audit objektu jádra](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10))<br> - [audit manipulace s popisovačem](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10))<br> -audit vyměnitelného úložiště |
| Čítače výkonu       | Změňte [konfiguraci čítačů výkonu](log-analytics-data-sources-performance-counters.md) tak, aby se: <br> – Snížila četnost shromažďování dat <br> – Snížil počet čítačů výkonu |
| Protokoly událostí                 | Změňte [konfiguraci protokolů událostí](log-analytics-data-sources-windows-events.md) tak, aby se: <br> – Snížil počet shromažďovaných protokolů událostí <br> – Shromažďovaly pouze požadované úrovně událostí Například zrušte shromažďování událostí úrovně *Informace*. |
| Syslog                     | Změňte [konfiguraci syslogu](log-analytics-data-sources-syslog.md) tak, aby se: <br> – Snížil počet zařízení, ze kterých se shromažďují data <br> – Shromažďovaly pouze požadované úrovně událostí Například zrušte shromažďování událostí úrovně *Informace* a *Ladění*. |
| AzureDiagnostics           | Změňte shromažďování protokolů prostředků tak, aby se: <br> – Snížil počet prostředků, které odesílají protokoly do Log Analytics <br> – Shromažďovaly pouze požadované protokoly |
| Data řešení z počítačů, které řešení nepotřebují | Použijte [cílení na řešení](../azure-monitor/insights/solution-targeting.md) a shromažďujte data pouze z požadované skupiny počítačů. |

### <a name="getting-node-counts"></a>Získávání počty uzlu 

Pokud jste na "Za uzel (OMS)," cenové úrovně, pak se účtují na základě počtu uzlů a řešení používáte, počtu přehledy a analýzy uzly, u kterých se vám účtují se nezobrazí v tabulce na **využití a odhadované náklady**stránky.  

Pokud chcete zobrazit počet různých uzlů, které zabezpečení, můžete dotaz:

`union
(
    Heartbeat
    | where (Solutions has 'security' or Solutions has 'antimalware' or Solutions has 'securitycenter')
    | project Computer
),
(
    ProtectionStatus
    | where Computer !in~
    (
        (
            Heartbeat
            | project Computer
        )
    )
    | project Computer
)
| distinct Computer
| project lowComputer = tolower(Computer)
| distinct lowComputer
| count`

Pokud chcete zobrazit počet různých uzlů, které služby Automation, použijte dotaz:

` ConfigurationData 
 | where (ConfigDataType == "WindowsServices" or ConfigDataType == "Software" or ConfigDataType =="Daemons") 
 | extend lowComputer = tolower(Computer) | summarize by lowComputer 
 | join (
     Heartbeat 
       | where SCAgentChannel == "Direct"
       | extend lowComputer = tolower(Computer) | summarize by lowComputer, ComputerEnvironment
 ) on lowComputer
 | summarize count() by ComputerEnvironment | sort by ComputerEnvironment asc`

## <a name="create-an-alert-when-data-collection-is-higher-than-expected"></a>Vytvoření upozornění při větším než očekávaném shromažďování dat
Tato část popisuje postup vytvoření upozornění v těchto případech:
- Objem dat překračuje zadanou velikost.
- Očekává se, že objem dat překročí zadanou velikost.

Upozornění Azure podporují [upozornění protokolu](../monitoring-and-diagnostics/monitor-alerts-unified-log.md) využívající vyhledávací dotazy. 

Následující dotaz vrátí výsledek, pokud se za posledních 24 hodin shromáždilo více než 100 GB dat:

`union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type | where DataGB > 100`

Následující dotaz pomocí jednoduchého vzorce předvídá, jestli dojde k odeslání více než 100 GB dat za den: 

`union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type | where EstimatedGB > 100`

Pokud chcete upozornit na jiný objem dat, změňte v dotazech hodnotu 100 na počet GB, na který chcete upozornit.

Pokud chcete být upozorňováni při větším než očekávaném shromažďování dat, postupujte podle kroků popsaných v tématu týkajícím se [vytvoření nového upozornění protokolu](../monitoring-and-diagnostics/alert-metric.md).

Při vytváření upozornění pro první dotaz (více než 100 GB dat během 24 hodin) nastavte:  

- **Definujte podmínku upozornění** – Jako cíl prostředku zadejte svůj pracovní prostor Log Analytics.
- **Kritéria upozornění** – Zadejte následující:
   - **Název signálu** – Vyberte **Vlastní prohledávání protokolu**.
   - **Vyhledávací dotaz** na `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type | where DataGB > 100`.
   - **Logika upozornění** je **Založená na** *počtu výsledků* a **Podmínka** je *Větší než* **Prahová hodnota** *0*.
   - **Časové období** na *1440* minut a **Frekvenci upozornění** na každých *60* minut, protože se data o využití aktualizují pouze jednou za hodinu.
- **Definujte podrobnosti upozornění** – Zadejte následující:
   - **Název** na *Větší objem dat než 100 GB během 24 hodin*.
   - **Závažnost** na *Upozornění*.

Zadejte existující nebo vytvořte novou [Skupinu akcí](../monitoring-and-diagnostics/monitoring-action-groups.md), abyste dostali upozornění, když upozornění protokolu splní kritéria.

Při vytváření upozornění pro druhý dotaz (předpověď, že během 24 hodin bude shromážděno více než 100 GB dat) nastavte:

- **Definujte podmínku upozornění** – Jako cíl prostředku zadejte svůj pracovní prostor Log Analytics.
- **Kritéria upozornění** – Zadejte následující:
   - **Název signálu** – Vyberte **Vlastní prohledávání protokolu**.
   - **Vyhledávací dotaz** na `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type | where EstimatedGB > 100`.
   - **Logika upozornění** je **Založená na** *počtu výsledků* a **Podmínka** je *Větší než* **Prahová hodnota** *0*.
   - **Časové období** na *180* minut a **Frekvenci upozornění** na každých *60* minut, protože se data o využití aktualizují pouze jednou za hodinu.
- **Definujte podrobnosti upozornění** – Zadejte následující:
   - **Název** na *Očekávaný větší objem dat než 100 GB během 24 hodin*.
   - **Závažnost** na *Upozornění*.

Zadejte existující nebo vytvořte novou [Skupinu akcí](../monitoring-and-diagnostics/monitoring-action-groups.md), abyste dostali upozornění, když upozornění protokolu splní kritéria.

Pokud obdržíte upozornění, pomocí kroků v následující části můžete řešit potíže způsobující větší využití, než se čekalo.

## <a name="next-steps"></a>Další postup
* V tématu [Prohledávání protokolů v Log Analytics](log-analytics-queries.md) zjistíte, jak používat jazyk vyhledávání. Pomocí vyhledávacích dotazů můžete na datech o využití provádět další analýzy.
* Pokud chcete být upozorňováni při splnění kritérií vyhledávání, postupujte podle kroků popsaných v tématu týkajícím se [vytvoření nového upozornění protokolu](../monitoring-and-diagnostics/alert-metric.md).
* Použijte [cílení na řešení](../azure-monitor/insights/solution-targeting.md) a shromažďujte data pouze z požadované skupiny počítačů.
* Pokud chcete nakonfigurovat efektivní zásadu shromažďování událostí zabezpečení, přečtěte si téma popisující [zásady filtrování v Azure Security Center](../security-center/security-center-enable-data-collection.md).
* Změňte [konfiguraci čítačů výkonu](log-analytics-data-sources-performance-counters.md).
* Pokud chcete upravit nastavení shromažďování událostí, přečtěte si téma popisující [konfiguraci protokolu událostí](log-analytics-data-sources-windows-events.md).
* Pokud chcete upravit nastavení shromažďování syslogu, přečtěte si téma popisující [konfiguraci syslogu](log-analytics-data-sources-syslog.md).
