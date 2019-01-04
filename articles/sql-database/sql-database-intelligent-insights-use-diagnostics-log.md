---
title: Intelligent Insights Diagnostika protokolování výkonu – Azure SQL Database | Dokumentace Microsoftu
description: Intelligent Insights poskytuje protokol diagnostiky problémů s výkonem Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 04/04/2018
ms.openlocfilehash: 1464b2685e0ae084de3679e8a8fea36a3cf19c4b
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53599140"
---
# <a name="use-the-intelligent-insights-azure-sql-database-performance-diagnostics-log"></a>Použití protokolu diagnostiky výkonu Intelligent Insights Azure SQL Database

Tato stránka obsahuje informace o tom, jak používat protokol diagnostiky výkonu Azure SQL Database generovaných [Intelligent Insights](sql-database-intelligent-insights.md), jeho formát a data obsahuje pro potřeby vaší vlastní vývoj. Odešlete tento protokol diagnostiky a [Azure Log Analytics](../azure-monitor/insights/azure-sql.md), [Azure Event Hubs](../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md), [služby Azure Storage](sql-database-metrics-diag-logging.md#stream-into-storage), nebo řešení třetí strany pro vlastní vývoj a provoz upozorňování a generování sestav Možnosti.

## <a name="log-header"></a>Hlavička protokolu

Diagnostický protokol používalo standardní formát JSON do výstupu Intelligent Insights zjištění. Vlastnost přesné kategorie pro přístup k protokolu Intelligent Insights je pevná hodnota "SQLInsights".

Hlavička protokolu je běžné a skládá se z časové razítko (TimeGenerated), který ukazuje, kdy byl vytvořen záznam. Zahrnuje také ID prostředku (ID prostředku), který odkazuje na konkrétní položka má vztah k databázi SQL. Category (kategorie), úroveň (úrovně) a název operaci (OperationName) jsou pevně dané vlastnosti, jejichž hodnoty se nezmění. Indikuje, že je položka protokolu informativní a že pocházejí z Intelligent Insights (SQLInsights).

```json
"TimeGenerated" : "2017-9-25 11:00:00", // time stamp of the log entry
"ResourceId" : "database identifier", // value points to a database resource
"Category": "SQLInsights", // fixed property
"Level" : "Informational", // fixed property
"OperationName" : "Insight", // fixed property
```

## <a name="issue-id-and-database-affected"></a>ID problému a ovlivněné databáze

Vlastnost Identifikace problému (issueId_d) poskytuje způsob jednoznačně sledování problémů s výkonem, dokud se nevyřeší. Více záznamů událostí do protokolu hlásí stav stejný problém bude sdílet stejné ID problému.

Spolu s ID problému diagnostický protokol sestavy (intervalStartTime_t) start a end (intervalEndTme_t) časová razítka konkrétní události týkající se problému, který se použije v hlášení v protokolu diagnostiky.

Vlastnosti elastického fondu (elasticPoolName_s) označuje, které elastického fondu databáze s problémem patří. Pokud databáze, které nejsou součástí elastického fondu, tato vlastnost nemá žádnou hodnotu. Vlastnost name (databaseName_s) databáze zachycuje databáze, ve kterém byl zjištěn problém.

```json
"intervalStartTime_t": "2017-9-25 11:00", // start of the issue reported time stamp
"intervalEndTme_t":"2017-9-25 12:00", // end of the issue reported time stamp
"elasticPoolName_s" : "", // resource elastic pool (if applicable) 
"databaseName_s" : "db_name",  // database name
"issueId_d" : 1525, // unique ID of the issue detected
"status_s" : "Active" // status of the issue – possible values: "Active", "Verifying", and "Complete"
```

## <a name="detected-issues"></a>Zjištěné problémy

Další část protokolu výkonu Intelligent Insights obsahuje problémy s výkonem, které byly zjištěny prostřednictvím integrovanou umělou inteligencí. Detekce jsou uvedeny ve vlastnostech v protokolu diagnostiky JSON. Tyto detekce se skládají z kategorii problému, dopad problému, vliv na dotazy a metriky. Detekce vlastnosti může obsahovat více, které byly zjištěny problémy s výkonem.

S následující strukturou vlastnost detekcí jsou hlášeny problémy s výkonem zjištěné:

```json
"detections_s" : [{
"impact" : 1 to 3, // impact of the issue detected, possible values 1-3 (1 low, 2 moderate, 3 high impact)
"category" : "Detectable performance pattern", // performance issue detected, see the table
"details": <Details outputted> // details of an issue (see the table)
}] 
```

Vzory zjistitelná výkonu a informace, které jsou výstupem v protokolu diagnostiky jsou k dispozici v následující tabulce.

### <a name="detection-category"></a>Detekce kategorie

Vlastnost category (kategorie) popisuje kategorie vzory zjistitelná výkonu. Podívejte se na následující tabulku pro všechny možné kategorie vzorů zjistitelná výkonu. Další informace najdete v tématu [databáze odstraňování problémů s výkonem Intelligent insights](sql-database-intelligent-insights-troubleshoot-performance.md).

Soubor protokolu v závislosti na výkonu zjištěném problému, podrobnosti výstupem v diagnostice liší odpovídajícím způsobem.

| Vzory zjistitelná výkonu | Podrobnosti výstupu |
| :------------------- | ------------------- |
| Dosáhnout omezení prostředků | <li>Ovlivněné prostředky</li><li>Hodnoty hash dotazu</li><li>Procento využití prostředků</li> |
| Zvýšení zatížení | <li>Počet dotazů, jejichž spuštění vyšší</li><li>Hodnoty hash dotazu dotazů s největší příspěvek na zvýšení zatížení</li> |
| Přetížení paměti | <li>Paměť fulltextu</li> |
| Uzamykání | <li>Vliv na hodnoty hash dotazu</li><li>Blokování hodnoty hash dotazu</li> |
| Zvýšená MAXDOP | <li>Hodnoty hash dotazu</li><li>CXP čekací dobu</li><li>Čekání</li> |
| Pagelatch kolizí | <li>Dotazování hodnoty hash způsobí kolizi dotazů</li> |
| Chybějící Index | <li>Hodnoty hash dotazu</li> |
| Nový dotaz | <li>Hodnota hash dotazu nových dotazů</li> |
| Statistiky neobvyklé čekání | <li>Typy neobvyklé čekání</li><li>Hodnoty hash dotazu</li><li>Dobu čekání dotazu</li> |
| Databáze TempDB kolizí | <li>Dotazování hodnoty hash způsobí kolizi dotazů</li><li>Attribution dotazu na celkový databáze pagelatch kolize čekací dobu [%]</li> |
| Nedostatek DTU elastického fondu | <li>Elastický fond</li><li>Hlavní databáze využívání jednotek DTU</li><li>Procento DTU použít hlavní spotřebitel fondu</li> |
| Regrese plán | <li>Hodnoty hash dotazu</li><li>Dobrý plán ID</li><li>Chybný plán ID</li> |
| Změna hodnoty konfigurace s rozsahem databáze | <li>Změny konfigurace s rozsahem databáze ve srovnání s výchozími hodnotami</li> |
| Pomalé klienta | <li>Hodnoty hash dotazu</li><li>Čekání</li> |
| Přechod na starší cenová úroveň | <li>Text oznámení</li> |

### <a name="impact"></a>Dopad

Dopad (dopad), že vlastnost popisuje kolik zjištěném chování přispívají k problému, který má databázi. Dopady rozsahu od 1 do 3, 3 jako největším přispěním, 2 jako střední a 1 jako nejnižší příspěvek. Hodnota dopad může použít jako vstup pro vlastní výstrahy automatizace, v závislosti na vašich konkrétních potřeb. Dotazy na vlastnosti ovlivněné (QueryHashes) zadejte seznam dotazu hodnoty hash, které byly ovlivněny konkrétním zjišťování.

### <a name="impacted-queries"></a>Ovlivněné dotazy

V další části protokolu Intelligent Insights poskytuje informace o určité dotazy, které byly ovlivněny problémy zjištěné výkonu. Tyto informace jsou zveřejňovány jako pole objektů, které jsou součástí impact_s vlastnost. Vlastnost účinek se skládá z entity a metriky. Entity odkazují na speciální dotaz (typ: Dotaz). Hodnota hash jedinečný dotazu je zveřejněn pod vlastnost value (hodnota). Kromě toho každý dotaz zveřejněn postupuje podle metrik a hodnotu, které označují problém zjištěné výkonu.

V následujícím příkladu protokolu, mají vyšší dobu provádění dotazu s hash 0x9102EXZ4 zjistilo (metriky: DurationIncreaseSeconds). Hodnota 110 sekund značí, že tento speciální dotaz trvalo déle 110 sekund provádění. Protože více dotazů můžete zjistit, tento oddíl obsahuje konkrétní protokol může obsahovat několik záznamů dotazu.

```json
"impact" : [{
"entity" : { 
"Type" : "Query", // type of entity - query
"Value" : "query hash value", // for example "0x9102EXZ4" query hash value },
"Metric" : "DurationIncreaseSeconds", // measured metric and the measurement unit (in this case seconds)
"Value" : 110 // value of the measured metric (in this case seconds)
}]
```

### <a name="metrics"></a>Metriky

Jednotka měření pro jednotlivé metriky hlášené není k dispozici vlastnost metrika (metrika) pomocí sady sekund, počet a procento možné hodnoty. Hodnota metriky měřené je uveden ve vlastnosti value (hodnota).

Vlastnost DurationIncreaseSeconds poskytuje měrnou jednotku v řádu sekund. Měrné jednotky CriticalErrorCount je číslo představující počet k chybě.

```json
"metric" : "DurationIncreaseSeconds", // issue metric type – possible values: DurationIncreaseSeconds, CriticalErrorCount, WaitingSeconds
"value" : 102 // value of the measured metric (in this case seconds)
```

## <a name="root-cause-analysis-and-improvement-recommendations"></a>Doporučení pro analýzy a zlepšování příčina kořenové

Poslední část protokolu výkonu Intelligent Insights se vztahují na analýzu automatizované původní příčiny snížení problému identifikovaného výkonu. Informace se zobrazí ve vhodných lidských tento problém ve vlastnosti kořenové příčiny analýzy (rootCauseAnalysis_s). Doporučení pro vylepšení jsou zahrnuty v protokolu, kde je to možné.

```json
// example of reported root cause analysis of the detected performance issue, in a human-readable format

"rootCauseAnalysis_s" : "High data IO caused performance to degrade. It seems that this database is missing some indexes that could help."
```

Můžete použít protokol výkonu inteligentních přehledů s [Azure Log Analytics]( https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql) nebo řešení třetí strany pro vlastní vývoj a provoz výstrahy a vytváření sestav funkce.

## <a name="next-steps"></a>Další postup
- Další informace o [Intelligent Insights](sql-database-intelligent-insights.md) koncepty.
- Zjistěte, jak [řešení problémů s výkonem Azure SQL Database s Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md).
- Zjistěte, jak [monitorování Azure SQL Database s využitím Azure SQL Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql).
- Zjistěte, jak [shromažďovat a zpracovávat data protokolu z vašich prostředků Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).



