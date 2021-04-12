---
title: Protokol diagnostiky výkonu Intelligent Insights
description: Intelligent Insights poskytuje diagnostický protokol pro Azure SQL Database a problémy s výkonem spravované instance Azure SQL.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 06/12/2020
ms.openlocfilehash: b03c21eea18c966616154b5cfc5df5d8924fd335
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100589313"
---
# <a name="use-the-intelligent-insights-performance-diagnostics-log-of-azure-sql-database-and-azure-sql-managed-instance-performance-issues"></a>Použití Intelligent Insights protokolu diagnostiky výkonu Azure SQL Database a problémů s výkonem spravované instance Azure SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Tato stránka obsahuje informace o tom, jak používat protokol diagnostiky výkonu generovaný [Intelligent Insights](intelligent-insights-overview.md) Azure SQL Database a problémy s výkonem spravované instance Azure SQL, jejich formát a data, která obsahuje pro vlastní potřeby vývoje. Tento diagnostický protokol můžete odeslat do [protokolů Azure monitor](../../azure-monitor/insights/azure-sql.md), [Azure Event Hubs](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs), [Azure Storage](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#stream-into-azure-storage)nebo řešení třetí strany pro vlastní funkce upozorňování a vytváření sestav DevOps.

> [!NOTE]
> Inteligentní přehledy jsou funkce ve verzi Preview, která není dostupná v následujících oblastech: Západní Evropa, Severní Evropa, Západní USA 1 a Východní USA 1.

## <a name="log-header"></a>Hlavička protokolu

Diagnostický protokol pro výstup Intelligent Insightsch výsledků používá standardní formát JSON. Přesná vlastnost kategorie pro přístup k protokolu Intelligent Insights je pevná hodnota "SQLInsights".

Záhlaví protokolu je běžné a skládá se z časového razítka (TimeGenerated), které ukazuje, kdy byla položka vytvořena. Zahrnuje také ID prostředku (ResourceId), které odkazuje na konkrétní databázi, ke které se vztahuje položka. Kategorie (kategorie), úroveň (úroveň) a název operace (OperationName) jsou pevné vlastnosti, jejichž hodnoty se nemění. Označují, že položka protokolu je informační a pochází z Intelligent Insights (SQLInsights).

```json
"TimeGenerated" : "2017-9-25 11:00:00", // time stamp of the log entry
"ResourceId" : "database identifier", // value points to a database resource
"Category": "SQLInsights", // fixed property
"Level" : "Informational", // fixed property
"OperationName" : "Insight", // fixed property
```

## <a name="issue-id-and-database-affected"></a>Ovlivněné ID problému a databáze

Vlastnost Identifikace problému (issueId_d) poskytuje způsob, jak jednoznačně sledovat problémy s výkonem až do vyřešení. Víc záznamů událostí ve stavu vytváření sestav protokolu u stejného problému bude sdílet stejné ID problému.

Společně s ID problému nástroj pro diagnostiku hlásí časová razítka počátečních (intervalStartTime_t) a koncových (intervalEndTme_tch) časových razítek konkrétní události týkající se problému, který je uveden v diagnostickém protokolu.

Vlastnost elastický fond (elasticPoolName_s) určuje, do kterého elastického fondu databáze s problémem patří. Pokud databáze není součástí elastického fondu, nemá tato vlastnost žádnou hodnotu. Databáze, ve které byl problém zjištěn, se zveřejní ve vlastnosti název databáze (databaseName_s).

```json
"intervalStartTime_t": "2017-9-25 11:00", // start of the issue reported time stamp
"intervalEndTme_t":"2017-9-25 12:00", // end of the issue reported time stamp
"elasticPoolName_s" : "", // resource elastic pool (if applicable)
"databaseName_s" : "db_name", // database name
"issueId_d" : 1525, // unique ID of the issue detected
"status_s" : "Active" // status of the issue – possible values: "Active", "Verifying", and "Complete"
```

## <a name="detected-issues"></a>Zjištěné problémy

Další část protokolu o výkonu Intelligent Insights obsahuje problémy s výkonem, které byly zjištěny prostřednictvím integrovaných umělých inteligentních funkcí. Zjišťování se zveřejňuje ve vlastnostech v protokolu JSON Diagnostics. Tyto detekce se skládají z kategorie problému, dopad problému, ovlivněné dotazy a metriky. Vlastnosti detekce mohou obsahovat více zjištěných problémů s výkonem.

Zjištěné problémy s výkonem jsou hlášeny s následující strukturou vlastností detekce:

```json
"detections_s" : [{
"impact" : 1 to 3, // impact of the issue detected, possible values 1-3 (1 low, 2 moderate, 3 high impact)
"category" : "Detectable performance pattern", // performance issue detected, see the table
"details": <Details outputted> // details of an issue (see the table)
}]
```

V následující tabulce jsou uvedeny informace o zjistitelných vzorech výkonu a podrobnostech, které jsou výstupem do diagnostického protokolu.

### <a name="detection-category"></a>Kategorie detekce

Vlastnost Category (kategorie) popisuje kategorii zjistitelných vzorců výkonu. V následující tabulce najdete všechny možné kategorie zjistitelných vzorců výkonu. Další informace najdete v tématu [řešení potíží s výkonem databáze pomocí Intelligent Insights](intelligent-insights-troubleshoot-performance.md).

V závislosti na zjištěném potížích s výkonem se odpovídajícím způsobem liší podrobnosti v souboru diagnostického protokolu.

| Rozpoznatelné vzory výkonu | Podrobnosti výstupu |
| :------------------- | ------------------- |
| Dosažení limitů prostředků | <li>Ovlivněné prostředky</li><li>Hodnoty hash dotazů</li><li>Procento využití prostředků</li> |
| Zvýšení zatížení | <li>Počet dotazů, jejichž spuštění bylo zvýšeno</li><li>Dotazování na hodnoty hash dotazů s největším přínosem zvýšení zatížení</li> |
| Zatížení paměti | <li>Úředník paměti</li> |
| Uzamčení | <li>Ovlivněné hodnoty hash dotazů</li><li>Blokování hodnot hash dotazů</li> |
| Zvýšená MAXDOP | <li>Hodnoty hash dotazů</li><li>CXP čekací doba</li><li>Čekací časy</li> |
| PAGELATCH spory | <li>Dotazování hodnot hash dotazů způsobujících spory</li> |
| Chybějící index | <li>Hodnoty hash dotazů</li> |
| Nový dotaz | <li>Hodnota hash dotazu pro nové dotazy</li> |
| Neobvyklá Statistika čekání | <li>Neobvyklé typy čekání</li><li>Hodnoty hash dotazů</li><li>Časy čekání na dotaz</li> |
| Obsah databáze TempDB | <li>Dotazování hodnot hash dotazů způsobujících spory</li><li>Přidaný dotaz k celkové době čekání na pagelatchování obsahu v databázi [%]</li> |
| Nedostatek DTU elastického fondu | <li>Elastický fond</li><li>Databáze nejvyšší úrovně DTU</li><li>Procento DTU fondu, které používá nejvyšší spotřebitel</li> |
| Plánování regrese | <li>Hodnoty hash dotazů</li><li>ID dobrých plánů</li><li>ID špatných plánů</li> |
| Změna hodnoty konfigurace Database-Scoped | <li>Změny konfigurace v rozsahu databáze v porovnání s výchozími hodnotami</li> |
| Pomalý klient | <li>Hodnoty hash dotazů</li><li>Čekací časy</li> |
| Downgrade cenové úrovně | <li>Textové oznámení</li> |

### <a name="impact"></a>Dopad

Vlastnost dopad (dopad) popisuje, kolik zjištěného chování přispělo k problému, který databáze má. Ovlivňuje rozsah od 1 do 3, přičemž 3 jako nejvyšší příspěvek, 2 jako střední a 1 jako nejnižší podíl. Hodnota dopadu se dá použít jako vstup pro automatizaci vlastního upozornění, a to v závislosti na vašich konkrétních potřebách. Ovlivněné dotazy vlastností (QueryHashes) poskytují seznam hodnot hash dotazů, které byly ovlivněny konkrétní detekcí.

### <a name="impacted-queries"></a>Ovlivněné dotazy

V další části protokolu Intelligent Insights najdete informace o konkrétních dotazech, které byly ovlivněny zjištěnými problémy s výkonem. Tyto informace jsou zveřejněné jako pole objektů vložených do vlastnosti impact_s. Vlastnost dopad se skládá z entit a metrik. Entity odkazují na konkrétní dotaz (typ: dotaz). Hodnota hash jedinečného dotazu je uzavřená pod vlastností hodnota (Value). Každý z těchto dotazů je navíc následován metrikou a hodnotou, která indikuje zjištěný problém s výkonem.

V následujícím příkladu protokolu bylo zjištěno, že dotaz s algoritmem hash 0x9102EXZ4 má vyšší dobu trvání spuštění (metrika: DurationIncreaseSeconds). Hodnota 110 sekund znamená, že spuštění tohoto konkrétního dotazu trvalo 110 sekund déle. Vzhledem k tomu, že je možné zjistit více dotazů, může tento konkrétní oddíl protokolu zahrnovat více položek dotazu.

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

Měrná jednotka každé hlášené metriky je k dispozici pod vlastností metrika (metrika), která má možné hodnoty sekund, Number a PERCENTAGE. Hodnota měřené metriky je uvedena v hodnotě vlastnosti hodnota (Value).

Vlastnost DurationIncreaseSeconds poskytuje jednotku měření v sekundách. Měrnou jednotkou CriticalErrorCount je číslo, které představuje počet chyb.

```json
"metric" : "DurationIncreaseSeconds", // issue metric type – possible values: DurationIncreaseSeconds, CriticalErrorCount, WaitingSeconds
"value" : 102 // value of the measured metric (in this case seconds)
```

## <a name="root-cause-analysis-and-improvement-recommendations"></a>Doporučení k analýze a vylepšení hlavní příčiny

Poslední část Intelligent Insightsho protokolu výkonu se týká analýzy automatických hlavních příčin zjištěného problému s snížením výkonu. Tyto informace se zobrazí v uživatelsky přívětivém verbiage ve vlastnosti "původní Analýza příčiny" (rootCauseAnalysis_s). Doporučení pro zlepšení jsou obsažena v protokolu, kde je to možné.

```json
// example of reported root cause analysis of the detected performance issue, in a human-readable format

"rootCauseAnalysis_s" : "High data IO caused performance to degrade. It seems that this database is missing some indexes that could help."
```

Můžete použít protokol výkonu Intelligent Insights s [protokoly Azure monitor]( https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql) nebo řešení třetí strany pro vlastní funkce upozorňování a vytváření sestav DevOps.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [Intelligent Insights](intelligent-insights-overview.md) konceptech.
- Naučte [se řešit potíže s výkonem pomocí Intelligent Insights](intelligent-insights-troubleshoot-performance.md).
- Naučte se [monitorovat problémy s výkonem pomocí Azure SQL Analytics](../../azure-monitor/insights/azure-sql.md).
- Naučte se [shromažďovat a využívat data protokolu z vašich prostředků Azure](../../azure-monitor/essentials/platform-logs-overview.md).