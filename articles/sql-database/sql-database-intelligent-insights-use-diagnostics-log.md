---
title: Protokol diagnostiky výkonu inteligentních přehledů
description: Inteligentní přehledy poskytují diagnostický protokol problémů s výkonem databáze Azure SQL
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: bb62b087451140261aee7aaa2fab0de14ea36283
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79209448"
---
# <a name="use-the-intelligent-insights-azure-sql-database-performance-diagnostics-log"></a>Použití protokolu diagnostiky diagnostiky výkonu azure sql databáze Inteligentní přehledy

Tato stránka obsahuje informace o tom, jak používat protokol diagnostiky výkonu databáze Azure SQL database generovaný [inteligentními přehledy](sql-database-intelligent-insights.md), jeho formát a data, která obsahuje pro vaše vlastní potřeby vývoje. Tento protokol diagnostiky můžete odeslat [protokolům Azure Monitor ,](../azure-monitor/insights/azure-sql.md) [Azure Event Hubs](../azure-monitor/platform/resource-logs-stream-event-hubs.md), [Azure Storage](sql-database-metrics-diag-logging.md#stream-into-azure-storage)nebo řešení jiného výrobce pro vlastní funkce upozornění a vytváření sestav DevOps.

## <a name="log-header"></a>Hlavička protokolu

Protokol diagnostiky používá standardní formát JSON k výstupu zjištění inteligentních přehledů. Vlastnost přesné kategorie pro přístup k protokolu Intelligent Insights je pevná hodnota "SQLInsights".

Záhlaví protokolu je běžné a skládá se z časového razítka (TimeGenerated), které ukazuje, kdy byla položka vytvořena. Obsahuje také ID prostředku (ResourceId), který odkazuje na konkrétní databázi SQL položka se vztahuje. Kategorie (Kategorie), úroveň (Úroveň) a název operace (OperationName) jsou pevné vlastnosti, jejichž hodnoty se nemění. Označují, že položka protokolu je informační a že pochází z inteligentních přehledů (SQLInsights).

```json
"TimeGenerated" : "2017-9-25 11:00:00", // time stamp of the log entry
"ResourceId" : "database identifier", // value points to a database resource
"Category": "SQLInsights", // fixed property
"Level" : "Informational", // fixed property
"OperationName" : "Insight", // fixed property
```

## <a name="issue-id-and-database-affected"></a>ID problému a databáze ovlivněny

Vlastnost identifikace problému (issueId_d) poskytuje způsob, jak jednoznačně sledovat problémy s výkonem, dokud nebude vyřešen. Více záznamů událostí ve stavu hlášení protokolu stejného problému bude sdílet stejné ID problému.

Spolu s ID problému protokol diagnostiky hlásí počáteční (intervalStartTime_t) a koncová (intervalEndTme_t) časová razítka konkrétní události související s problémem, který je uveden v protokolu diagnostiky.

Vlastnost elastického fondu (elasticPoolName_s) označuje, do kterého elastického fondu databáze s problémem patří. Pokud databáze není součástí elastického fondu, tato vlastnost nemá žádnou hodnotu. Databáze, ve které byl zjištěn problém, je zveřejněna ve vlastnosti název databáze (databaseName_s).

```json
"intervalStartTime_t": "2017-9-25 11:00", // start of the issue reported time stamp
"intervalEndTme_t":"2017-9-25 12:00", // end of the issue reported time stamp
"elasticPoolName_s" : "", // resource elastic pool (if applicable)
"databaseName_s" : "db_name", // database name
"issueId_d" : 1525, // unique ID of the issue detected
"status_s" : "Active" // status of the issue – possible values: "Active", "Verifying", and "Complete"
```

## <a name="detected-issues"></a>Zjištěné problémy

Další část protokolu výkonu inteligentních přehledů obsahuje problémy s výkonem, které byly zjištěny prostřednictvím integrované umělé inteligence. Detekce jsou uvedeny ve vlastnostech v protokolu diagnostiky JSON. Tato zjišťování se skládají z kategorie problému, dopad problému, ovlivněné dotazy a metriky. Vlastnosti zjišťování mohou obsahovat více problémů s výkonem, které byly zjištěny.

Zjištěné problémy s výkonem jsou hlášeny s následující strukturou vlastností zjišťování:

```json
"detections_s" : [{
"impact" : 1 to 3, // impact of the issue detected, possible values 1-3 (1 low, 2 moderate, 3 high impact)
"category" : "Detectable performance pattern", // performance issue detected, see the table
"details": <Details outputted> // details of an issue (see the table)
}]
```

Zjistitelné vzory výkonu a podrobnosti, které jsou výstupem do protokolu diagnostiky jsou uvedeny v následující tabulce.

### <a name="detection-category"></a>Kategorie detekce

Vlastnost kategorie (kategorie) popisuje kategorii zjistitelných vzorů výkonu. Všechny možné kategorie zjistitelných vzorců výkonu naleznete v následující tabulce. Další informace naleznete [v tématu Poradce při potížích s výkonem databáze pomocí inteligentních přehledů](sql-database-intelligent-insights-troubleshoot-performance.md).

V závislosti na zjištěném problému s výkonem se podrobnosti vyproné v souboru protokolu diagnostiky odpovídajícím způsobem liší.

| Rozpoznatelné vzory výkonu | Výstup podrobností |
| :------------------- | ------------------- |
| Dosažení limitů prostředků | <li>Ovlivněné zdroje</li><li>Dotaz u hashe</li><li>Procento spotřeby zdrojů</li> |
| Zvýšení pracovního vytížení | <li>Počet dotazů, jejichž spuštění bylo zvýšeno</li><li>Dotazovat se na dotazy dotazů na dotazy s největším příspěvkem ke zvýšení pracovního vytížení</li> |
| Zatížení paměti | <li>Úředník paměti</li> |
| Uzamčení | <li>Ovlivněné dotazy hashes</li><li>Blokování hodnot hashe dotazu</li> |
| Zvýšená MAXDOP | <li>Dotaz u hashe</li><li>Čekací doby CXP</li><li>Čekací doby</li> |
| Tvrzení pagelatchu | <li>Dotaz na hashe dotazů na dotazy způsobující konflikty</li> |
| Chybějící index | <li>Dotaz u hashe</li> |
| Nový dotaz | <li>Dotaz na hodnotu hash nových dotazů</li> |
| Neobvyklá statistika čekání | <li>Neobvyklé typy čekání</li><li>Dotaz u hashe</li><li>Čekací doby dotazu</li> |
| Tvrzení TempDB | <li>Dotaz na hashe dotazů na dotazy způsobující konflikty</li><li>Přiřazení přiřazení dotazu na celkovou dobu čekání na stránce databáze pagelatch [%]</li> |
| Elastický bazén Nedostatek DTU | <li>Elastický fond</li><li>Top DTU-náročné databáze</li><li>Procento poolu DTU používaného nejlepším spotřebitelem</li> |
| Regrese plánu | <li>Dotaz u hashe</li><li>Dobrá ID plánu</li><li>Chybná ID plánu</li> |
| Změna hodnoty konfigurace s rozsahem databáze | <li>Změny konfigurace s rozsahem databáze ve srovnání s výchozími hodnotami</li> |
| Pomalý klient | <li>Dotaz u hashe</li><li>Čekací doby</li> |
| Snížení úrovně ocenění | <li>Textové oznámení</li> |

### <a name="impact"></a>Dopad

Vlastnost impact (impact) popisuje, jak moc zjištěné chování přispělo k problému, který má databáze. Dopady se pohybují od 1 do 3, přičemž 3 jsou nejvyšší, 2 jako mírné a 1 jako nejnižší příspěvek. Hodnota dopadu může být použita jako vstup pro vlastní automatizaci výstrah, v závislosti na vašich konkrétních potřebách. Ovlivněné dotazy vlastností (QueryHashes) poskytují seznam hodnot hash dotazů, které byly ovlivněny konkrétní zjišťování.

### <a name="impacted-queries"></a>Ovlivněné dotazy

Další část protokolu Inteligentní přehledy obsahuje informace o konkrétní dotazy, které byly ovlivněny zjištěné problémy s výkonem. Tyto informace jsou zveřejněny jako pole objektů vložených do impact_s vlastnost. Vlastnost impact se skládá z entit a metrik. Entity odkazují na konkrétní dotaz (Typ: Dotaz). Jedinečná hodnota hash dotazu je zveřejněna pod vlastností value (Value). Kromě toho každý z zveřejněných dotazů následuje metrika a hodnota, které označují zjištěný problém s výkonem.

V následujícím příkladu protokolu byl zjištěn dotaz s hodnotou hash 0x9102EXZ4, který má prodlouženou dobu provádění (Metrika: DurationIncreaseSeconds). Hodnota 110 sekund označuje, že tento konkrétní dotaz trvalo 110 sekund déle ke spuštění. Vzhledem k tomu, že lze zjistit více dotazů, může tato konkrétní část protokolu obsahovat více položek dotazu.

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

Měrná jednotka pro každou vykázanou metriku je uvedena v rámci vlastnosti metriky (metriky) s možnými hodnotami sekund, počtu a procentuálníhodnoty. Hodnota naměřené metriky je uvedena ve vlastnosti value (value).

DurationIncreaseSeconds Vlastnost poskytuje měrnou jednotku v sekundách. Měrná jednotka CriticalErrorCount je číslo, které představuje počet chyb.

```json
"metric" : "DurationIncreaseSeconds", // issue metric type – possible values: DurationIncreaseSeconds, CriticalErrorCount, WaitingSeconds
"value" : 102 // value of the measured metric (in this case seconds)
```

## <a name="root-cause-analysis-and-improvement-recommendations"></a>Doporučení pro analýzu a zlepšení hlavních příčin

Poslední část protokolu výkonu Inteligentní přehledy se vymýšlela v automatické analýze hlavní příčiny identifikovaného problému snížení výkonu. Informace se zobrazí v lidské přátelské verbiage v hlavní příčiny analýzy (rootCauseAnalysis_s) vlastnost. Doporučení pro zlepšení jsou zahrnuty v protokolu, kde je to možné.

```json
// example of reported root cause analysis of the detected performance issue, in a human-readable format

"rootCauseAnalysis_s" : "High data IO caused performance to degrade. It seems that this database is missing some indexes that could help."
```

Protokol výkonu Inteligentní přehledy můžete použít s [protokoly Azure Monitor]( https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql) nebo řešení jiného výrobce pro vlastní funkce upozornění a vytváření sestav DevOps.

## <a name="next-steps"></a>Další kroky

- Seznamte se s koncepty [inteligentních přehledů.](sql-database-intelligent-insights.md)
- Zjistěte, jak [řešit problémy s výkonem Azure SQL Database pomocí inteligentních přehledů](sql-database-intelligent-insights-troubleshoot-performance.md).
- Zjistěte, jak [monitorovat Azure SQL Database pomocí Azure SQL Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql).
- Zjistěte, jak [shromažďovat a využívat data protokolu z prostředků Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).
