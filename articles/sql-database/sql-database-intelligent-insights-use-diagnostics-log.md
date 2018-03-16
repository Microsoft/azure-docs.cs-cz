---
title: "Inteligentní diagnostiky výkonu Statistika-protokol – Azure SQL Database | Microsoft Docs"
description: "Inteligentní statistika poskytuje protokolu diagnostiky problémů s výkonem databáze SQL Azure"
services: sql-database
author: danimir
manager: craigg
ms.reviewer: carlrab
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 09/25/2017
ms.author: v-daljep
ms.openlocfilehash: b380d3a8a35750602a4a0d20d595f71b125fc118
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="use-the-intelligent-insights-azure-sql-database-performance-diagnostics-log"></a>Použití protokolu diagnostiky výkonu inteligentního Statistika Azure SQL Database

Tato stránka obsahuje informace o tom, jak používat protokol diagnostiky výkonu Azure SQL Database generované [inteligentního Statistika](sql-database-intelligent-insights.md), formát a k datům obsahuje pro váš vlastní vývoj potřebuje. Můžete odeslat tento protokol diagnostiky a [Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md), [Azure Event Hubs](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md), [Azure Storage](sql-database-metrics-diag-logging.md#stream-into-storage), nebo řešení třetí strany pro vlastní DevOps výstrahy a vytváření sestav Možnosti.

## <a name="log-header"></a>Záhlaví protokolu

Protokol diagnostiky používá standardní formát JSON k vypsání výsledků zjištění inteligentního Statistika. Vlastnost přesný kategorie pro přístup k protokolu inteligentního Insights je pevná hodnota "SQLInsights".

Hlavička protokolu běžné a skládá se z časové razítko (TimeGenerated), který ukazuje vytvoření položku. Zahrnuje také ID prostředku (ResourceId), který odkazuje na konkrétní položka má vztah k databázi SQL. Kategorie (kategorie), úrovni (úroveň) a název operaci (OperationName) jsou pevně dané vlastnosti, jejichž hodnoty se nezmění. Indikuje, že je záznam protokolu informativní a že pocházejí z inteligentního přehledy (SQLInsights).

```json
"TimeGenerated" : "2017-9-25 11:00:00", // time stamp of the log entry
"ResourceId" : "database identifier", // value points to a database resource
"Category": "SQLInsights", // fixed property
"Level" : "Informational", // fixed property
"OperationName" : "Insight", // fixed property
```

## <a name="issue-id-and-database-affected"></a>ID problému a databáze vliv

Vlastnost Identifikace problém (issueId_d) poskytuje způsob jednoznačně sledování problémy s výkonem, dokud se jejich vyřešení. Inteligentní Statistika dodržuje každý problém životního cyklu jako "Aktivní", "Ověření" nebo "Úplná". Prostřednictvím jednotlivých fází stav můžete zaznamenat inteligentního Statistika více záznamů událostí v protokolu. Pro každou z těchto položek zůstane jedinečné číslo ID problému. Inteligentní Insights sleduje problém prostřednictvím životního cyklu a generuje přehledu v protokolu diagnostiky každých 15 minut.

Jakmile se zjistí problém s výkonem a pro tak dlouho, dokud jeho platnost, problém se hlásí jako "Aktivní" v části vlastnost status (status_s). Po zjištěný problém zmírnit, má ověřit a ohlásit jako "Ověření" v části vlastnost status (status_s). Pokud tento problém již neexistuje, stav (status_s) vlastnost hlásí tento problém jako "Dokončeno".

Společně s ID problému protokolu diagnostiky hlásí počáteční (intervalStartTime_t) a end (intervalEndTme_t) časová razítka konkrétní události související s problém, který se použije v hlášení v protokolu diagnostiky.

Vlastnost elastického fondu (elasticPoolName_s) určuje, které elastického fondu databáze s problémem patří do. Není-li databáze součástí fondu elastické databáze, tato vlastnost nemá žádnou hodnotu. Databáze, ve kterém byl zjištěn problém je uvedeno v databázi vlastnost name (databaseName_s).

```json
"intervalStartTime_t": "2017-9-25 11:00", // start of the issue reported time stamp
"intervalEndTme_t":"2017-9-25 12:00", // end of the issue reported time stamp
"elasticPoolName_s" : "", // resource elastic pool (if applicable) 
"databaseName_s" : "db_name",  // database name
"issueId_d" : 1525, // unique ID of the issue detected
"status_s" : "Active" // status of the issue – possible values: "Active", "Verifying", and "Complete"
```

## <a name="detected-issues"></a>Zjištěné problémy

V další části inteligentního Statistika výkonu protokolu obsahuje problémy s výkonem, které byly zjištěny prostřednictvím vestavěné inteligentní umělé. Detekce jsou uvedena v vlastnosti v rámci protokolu diagnostiky JSON. Tyto detekce skládá z kategorii problému, dopad problém, vliv na dotazy a metriky. Vlastnosti detekce může obsahovat více problémy s výkonem, které byly zjištěny.

Problémy s výkonem zjištěné jsou hlášeny s následující strukturou detekce vlastnost:

```json
"detections_s" : [{
"impact" : 1 to 3, // impact of the issue detected, possible values 1-3 (1 low, 2 moderate, 3 high impact)
"category" : "Detectable performance pattern", // performance issue detected, see the table
"details": <Details outputted> // details of an issue (see the table)
}] 
```

Vzory rozpoznat výkon a podrobnosti, které jsou výstupem do protokolu diagnostiky jsou uvedeny v následující tabulce.

### <a name="detection-category"></a>Detekce kategorie

Vlastnost category (kategorie) popisuje kategorii výkonu rozpoznat vzory. Najdete v následující tabulce pro všechny možné kategorie výkonu rozpoznat vzory. Další informace najdete v tématu [řešení potíží s výkonem databáze s inteligentního Insights](sql-database-intelligent-insights-troubleshoot-performance.md).

V závislosti na výkonu zjištěném problému, podrobnosti výstupem v diagnostiku lišit podle toho souboru protokolu.

| Vzory rozpoznat výkonu | Podrobnosti o výstupem |
| :------------------- | ------------------- |
| Dosažení limitů prostředků | <li>Vliv na prostředky</li><li>Hodnoty hash dotazu</li><li>Procento spotřeby prostředků</li> |
| Zvýšení zatížení | <li>Počet dotazů, jejichž spuštění vyšší</li><li>Hodnoty hash dotazu dotazů s největší příspěvku zvýšení zatížení</li> |
| Přetížení paměti | <li>Fulltextu paměti</li> |
| Zamykání | <li>Vliv na hodnoty hash dotazu</li><li>Blokování hodnoty hash dotazu</li> |
| Zvýšená MAXDOP | <li>Hodnoty hash dotazu</li><li>Dobu čekání CXP</li><li>Čekání</li> |
| Pagelatch kolizí | <li>Dotaz na hodnoty hash dotazů způsobuje kolizí</li> |
| Chybí indexu | <li>Hodnoty hash dotazu</li> |
| Nový dotaz | <li>Hodnota hash dotazu nové dotazy</li> |
| Statistiky neobvyklou čekání | <li>Typy neobvyklou čekání</li><li>Hodnoty hash dotazu</li><li>Dobu čekání dotazu</li> |
| Databáze TempDB kolizí | <li>Dotaz na hodnoty hash dotazů způsobuje kolizí</li><li>Uvedení dotazu na celkový databáze pagelatch kolizí čekací doba [%]</li> |
| Nedostatek DTU elastického fondu | <li>Elastický fond</li><li>Hlavní databáze spotřeba DTU</li><li>Procento fondu používá ji příjemce nejvyšší počet jednotek DTU</li> |
| Plánování regrese | <li>Hodnoty hash dotazu</li><li>Kvalitní plán ID</li><li>Chybný plán ID</li> |
| Změna hodnoty obor databáze konfigurace | <li>Změny konfigurace s rozsahem databáze ve srovnání s výchozí hodnoty</li> |
| Pomalé klienta | <li>Hodnoty hash dotazu</li><li>Čekání</li> |
| Cenová úroveň přechod na starší verzi | <li>Text oznámení</li> |

### <a name="impact"></a>Dopad

Dopad (dopad), že vlastnost popisuje na problém, který má databáze podílí kolik zjištěné chování. Ovlivňuje rozsahu od 1 do 3, 3 jako nejvyšší příspěvku, 2 jako střední a 1 jako nejnižší příspěvku. Hodnota dopad může použít jako vstup pro vlastní výstrahy automatizace, v závislosti na svých konkrétních potřeb. Dotazy na vlastnosti ovlivněné (QueryHashes), poskytovat seznam dotaz hodnoty hash, které byly ovlivněny konkrétní detekce.

### <a name="impacted-queries"></a>Ovlivněné dotazy

Následující část inteligentního Statistika protokolu obsahuje informace o konkrétní dotazy, které situace měla vliv na výkon zjištěné problémy. Tyto informace jsou zveřejňovány jako pole objektů vložených ve vlastnosti impact_s. Vlastnost účinek se skládá z entity a metriky. Entity odkazovat na konkrétní dotaz (typ: dotazu). Hodnota hash jedinečný dotazu je zveřejněn v části vlastnosti value (hodnota). Kromě toho každý dotaz odhalena následuje metriky a hodnotu, které označují problémy zjištěné výkonem.

V následujícím příkladu protokolu mít prodloužena doba provádění dotazu s hash 0x9102EXZ4 zjistilo (metrika: DurationIncreaseSeconds). Hodnota 110 sekund znamená, že tento konkrétní dotaz trvalo déle 110 sekund provést. Protože více dotazů můžete zjistit, v této části konkrétní protokolu může obsahovat několik záznamů dotazu.

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

Jednotky měření pro jednotlivé metriky hlášené jsou poskytovány na základě vlastnost metrika (metrika) s možné hodnoty sekund, počet a procento. Hodnota měřená metrika je uveden ve vlastnosti value (hodnota).

Vlastnost DurationIncreaseSeconds poskytuje jednotky měření v sekundách. Měrné jednotky CriticalErrorCount je číslo, které představuje počtu chyb.

```json
"metric" : "DurationIncreaseSeconds", // issue metric type – possible values: DurationIncreaseSeconds, CriticalErrorCount, WaitingSeconds
"value" : 102 // value of the measured metric (in this case seconds)
```

## <a name="root-cause-analysis-and-improvement-recommendations"></a>Doporučení pro analýzu a zlepšování příčina kořenové

Poslední část protokol inteligentního Statistika výkonu se vztahují na Analýza automatizované hlavní příčiny problému snížení identifikovaných výkonu. Informace se zobrazí v lidských friendly tento problém ve vlastnosti kořenové příčina analysis (rootCauseAnalysis_s). Zlepšování doporučení jsou zahrnuty v protokolu, kde je to možné.

```json
// example of reported root cause analysis of the detected performance issue, in a human-readable format

"rootCauseAnalysis_s" : "High data IO caused performance to degrade. It seems that this database is missing some indexes that could help."
```

Můžete použít protokol inteligentního Statistika výkonu s [Azure Log Analytics]( https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql) nebo řešení třetí strany pro vlastní DevOps výstrahy a vytváření sestav funkce.

## <a name="next-steps"></a>Další postup
- Další informace o [inteligentního Statistika](sql-database-intelligent-insights.md) koncepty.
- Zjistěte, jak [řešení potíží s výkonem databáze SQL Azure s inteligentního Insights](sql-database-intelligent-insights-troubleshoot-performance.md).
- Zjistěte, jak [monitorovat Azure SQL Database pomocí Azure SQL Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql).
- Zjistěte, jak [shromažďovat a využívat data protokolu z vašich prostředků Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).



