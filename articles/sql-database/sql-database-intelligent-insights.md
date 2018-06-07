---
title: Sledování využití databáze se statistickými inteligentního – Azure SQL Database | Microsoft Docs
description: Azure SQL Database inteligentního Insights používá vestavěné inteligentní neustále monitorovat využití databáze prostřednictvím umělé intelligence a zjišťování rušivý událostí, které způsobí snížení výkonu.
services: sql-database
author: danimir
manager: craigg
ms.reviewer: carlrab
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: v-daljep
ms.openlocfilehash: a911ed9777e1a63e1fc1776fb6a8c1ab98bc6820
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34647499"
---
# <a name="intelligent-insights"></a>Užitečné přehledy

Azure SQL Database inteligentního Insights umožňuje vědět, co se děje s výkon databáze.

Inteligentní Insights používá vestavěné inteligentní neustále monitorovat využití databáze prostřednictvím umělé intelligence a zjišťování rušivý událostí, které způsobí snížení výkonu. Jakmile detekuje, se provádí podrobné analýzy, který generuje protokolu diagnostiky s inteligentního posouzení typu problému. Tato hodnocení se skládá z analýza hlavní příčiny problémy s výkonem databáze a, kde je to možné, doporučení pro zlepšení výkonu. 

## <a name="what-can-intelligent-insights-do-for-you"></a>Co dokáže inteligentního statistiky pro vás?

Inteligentní Insights je jedinečné funkce Azure vestavěné inteligentní, která poskytuje následující hodnotu:

- Proaktivní monitorování
- Přehled o výkonu šité na míru
- Časná zjišťování snížení výkonu databáze
- Analyzovat problémy zjištěna příčina
- Doporučení pro zlepšení výkonu
- Horizontální navýšení kapacity možnost na stovky tisíc databáze
- Kladný dopad na prostředky DevOps a celkové náklady na vlastnictví

## <a name="how-does-intelligent-insights-work"></a>Jak funguje inteligentního Insights?

Inteligentní Statistika analyzuje výkonu SQL databáze tak, že porovnáte zatížení databáze z za poslední hodinu se zatížením, posledních sedmi dnů směrného plánu. Zatížení databáze se skládá z dotazy vyhodnocen jako nejvýznamnější výkon databáze, jako je například co opakovaný a největší dotazy. Protože každá databáze je jedinečný na základě jeho struktura, data, použití a aplikace, je každý směrný plán úlohy, aby se vygenerovala konkrétní a jedinečné pro jednotlivé instance. Inteligentní statistiky, nezávislé na standardních hodnot zatížení, také sleduje absolutní provozní prahové hodnoty a zjistí problémy s nadměrné čekací doby, kritické výjimky a problémy s parameterizations dotazu, které mohou ovlivnit výkon.

Po problémem snížení výkonu je zjišťován z více zjištěnou metriky pomocí umělé inteligence, provedení analýzy. Diagnostika protokolu je vytvořen s inteligentního přehledu na co se děje s vaší databáze. Inteligentní Statistika lze snadno sledovat problémy s výkonem databáze z její první vzhled až řešení. Každý zjistil, že problém je sledovat prostřednictvím životního cyklu od počáteční problém detekce a ověření výkonu v přírůstcích na její dokončení. Aktualizace jsou uvedeny v protokolu diagnostiky každých 15 minut. 

![Pracovní postup analýzy výkonu databáze](./media/sql-database-intelligent-insights/intelligent-insights-concept.png)

Metriky lze měřit a zjistit problémy s výkonem databáze jsou založené na dotazu trvání, časový limit žádosti, dobu čekání nadměrné a chybných požadavků. Další informace o metrikách najdete v tématu [detekce metriky](sql-database-intelligent-insights.md#detection-metrics) část tohoto dokumentu.

Identifikovat SQL Database degradations výkonu se zaznamenávají v protokolu diagnostiky s inteligentního položky, které se skládají z následujících vlastností:

| Vlastnost             | Podrobnosti              |
| :------------------- | ------------------- |
| Informace o databázi | Metadata o databázi, na kterém byl zjištěn přehledu, jako je identifikátor URI prostředku. |
| Sledovaného časového rozsahu | Počáteční a koncový čas dobu zjištěné přehledy. |
| Ovlivněné metriky | Metriky, které způsobily vygenerování přehledu: <ul><li>Doba trvání zvýšit [sekund] dotaz.</li><li>Nadměrné čekání [sekund].</li><li>Vypršení časového limitu požadavků [procento].</li><li>Počet požadavků s chybou [procento].</li></ul>|
| Hodnota dopad | Měří hodnota metriky. |
| Ovlivněné dotazy a kódy chyb | Dotaz na hodnotu hash nebo chybový kód. Ty lze snadno korelovat ovlivněných dotazy. Metriky, které se skládají z dotazu zvyšte dobu trvání, doba čekání, počty vypršení časového limitu nebo kódy chyb jsou k dispozici. |
| Zjištění | Detekce identifikovat v databázi v době události. Existují 15 detekce vzory. Další informace najdete v tématu [řešení potíží s výkonem databáze s inteligentního Insights](sql-database-intelligent-insights-troubleshoot-performance.md). |
| Analýza hlavní příčiny | Příčina analýzu problém uvedený v čitelném formátu. Některé insights může obsahovat doporučení zlepšování výkonu, kde je to možné. |
|||

Problémy s výkonem, které se zaznamenávají v protokolu diagnostiky jsou označeny s jedním ze tří stavů životního cyklu k problému: "Aktivní", "Ověření" a "Dokončeno". Po výkonu se zjistí problém, a jak dlouho má považovala přítomný pomocí vestavěné inteligentní SQL Database, problém je označené jako "Aktivní". Pokud problém považuje za zmírnit, je ověřen a problém stav se změní na "Ověření". Po vestavěné inteligentní SQL Database za tento problém vyřešit, je stav problém označení "Dokončeno".

## <a name="use-intelligent-insights"></a>Použití inteligentního statistiky

Inteligentní Insights je inteligentní výkonu protokolu diagnostiky. Lze ji integrovat s dalšími produkty pro používání a konkrétní aplikace takové jsou analýzy protokolů Azure, Azure Event Hubs a Azure storage nebo třetích stran produkty. 

Inteligentní Statistika společně s Azure Log Analytics se obvykle používá k zobrazení statistiky prostřednictvím webového prohlížeče a možná jedním z nejjednodušších způsobů, jak získat vypnout základů s použitím produktu. Inteligentní Statistika společně s Azure Event Hubs se obvykle používá ke konfiguraci vlastního monitorování a výstrah scénáře. Inteligentní Statistika společně s Azure storage se obvykle používá pro vývoj vlastních aplikací, například je například vytváření vlastních sestav, nebo možná data archivace a načtení.

Integrace inteligentního Statistika s jinými produkty Azure Log Analytics centra událostí Azure, Azure storage nebo produkty třetích stran pro používání se provádí prostřednictvím první povolení inteligentního Statistika protokolu (SQLInsights) a potom nakonfigurovat Inteligentní Statistika protokolovat data tok dat do jednoho z těchto produktů. Další informace o povolení protokolování inteligentního přehledy a konfigurace dat protokolu odesílání náročné produktu najdete v tématu [Azure SQL Database metrik a protokolování diagnostiky](sql-database-metrics-diag-logging.md). 

Praktické informace o použití inteligentního Statistika s Azure Log Analytics a příklady typických scénářů použití naleznete v tématu vložené video:


> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-Intelligent-Insights-for-Improving-Azure-SQL-Database-Performance/player]
>

Inteligentní Statistika září v zjišťování a řešení potíží s výkonem databáze SQL. Abyste mohli používat inteligentní Insights řešení potíží s výkonem databáze SQL, najdete v části [problémy s výkonem řešení potíží s Azure SQL Database pomocí služby inteligentního Insights](sql-database-intelligent-insights-troubleshoot-performance.md).

## <a name="set-up-intelligent-insights-with-log-analytics"></a>Nastavení inteligentního Statistika s analýzy protokolů 

Přihlaste se analýza řešení poskytuje sestavy a výstrahy možnosti nad inteligentního Statistika protokolu diagnostiky data.

Pro použití inteligentního Statistika s analýzy protokolů, konfigurace dat protokolu inteligentního Statistika Streamovat k analýze protokolů, najdete v článku [Azure SQL Database metrik a protokolování diagnostiky](sql-database-metrics-diag-logging.md). 

Následující příklad ukazuje zprávu o inteligentního statistiky v Azure SQL Analytics:

![Inteligentní Statistika sestavy](./media/sql-database-intelligent-insights/intelligent-insights-azure-sql-analytics.png)

Protokol diagnostiky inteligentního Statistika konfigurován tak, aby datový proud data analýzy SQL, můžete [monitorovat SQL database pomocí SQL Analytics](../log-analytics/log-analytics-azure-sql.md).

## <a name="set-up-intelligent-insights-with-event-hubs"></a>Nastavení inteligentního Statistika službou Event Hubs

Použití inteligentního Statistika službou Event Hubs, konfigurace dat protokolu inteligentního Statistika tok dat do centra událostí, najdete v článku [protokolů diagnostiky Azure datový proud do centra událostí](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md).

Nastavit vlastní monitorování a generování výstrah pomocí služby Event Hubs naleznete v části [co dělat s metriky a Diagnostika přihlásí Event Hubs](sql-database-metrics-diag-logging.md#what-to-do-with-metrics-and-diagnostics-logs-in-event-hubs). 

## <a name="set-up-intelligent-insights-with-storage"></a>Nastavení inteligentního Statistika s úložištěm

Použití inteligentního Statistika s Storage, konfigurace dat protokolu inteligentního Statistika tok dat do úložiště, najdete v článku [datový proud do Azure Storage](sql-database-metrics-diag-logging.md#stream-into-storage).

## <a name="custom-integrations-of-intelligent-insights-log"></a>Vlastní integrace inteligentního Statistika protokolu

Použití inteligentního Statistika s nástroji třetích stran nebo pro vlastní zobrazení výstrah a monitorování vývoj naleznete v části [použití protokolu diagnostiky výkonu databáze inteligentního Insights](sql-database-intelligent-insights-use-diagnostics-log.md).

## <a name="detection-metrics"></a>Detekce metriky

Metriky použít pro zjištění modely, které generovat inteligentního statistiky jsou založené na monitorování:

- Doba trvání dotazu
- Časový limit žádosti
- Nadměrné čekací doba
- Chyba se požadavky

Doba trvání a vypršení časového limitu požadavků na dotazy se používají jako primární modely v zjišťování problémů s výkonem zatížení databáze. Jejich použití protože přímo měření co se děje se zatížením. Ke zjištění všech možných případech zatížení výkonu snížení nadměrné čekací dobu a požadavky s chybou slouží jako další modely k příznakem potíží, které mají vliv na výkon úloh.

Je automaticky za změny zatížení a změny v počtu požadavků na dotazy dynamicky provedených v databázi k určení prahových hodnot výkonu databáze normální a na více systémů v běžných.

Metriky jsou společně zvažovány všechny v různých relacích prostřednictvím vědecky odvozené datového modelu, který rozděluje každém zjištěném problému výkonu. Obsahuje informace, které jsou poskytované prostřednictvím inteligentního přehledu:

* Podrobnosti o zjištěném problému výkonu. 
* Analýza hlavní příčiny problému zjištěna. 
* Doporučení pro zlepšení výkonu monitorované databáze SQL, kde je to možné.

## <a name="query-duration"></a>Doba trvání dotazu

Model snížení doby trvání dotazu analyzuje jednotlivé dotazy a zjistí zvýšení v době potřebné ke kompilaci a provedení dotazu ve srovnání s základní úrovně výkonu.

Pokud databáze SQL vestavěné inteligentní zjistí významné zvýšení v kompilaci dotazu nebo doba provádění dotazu, který má vliv na výkon zatížení, tyto dotazy jsou označeny jako doba trvání dotazu snížení problémy s výkonem. 

Protokol diagnostiky inteligentního Statistika výstupy hash dotazu snížení výkonnosti dotazu. Hash dotazu určuje, zda byl snížení výkonu související s kompilaci nebo doba provádění dotazu zvýšení, které zvýšily dotazu doba trvání.

## <a name="timeout-requests"></a>Časový limit žádosti

Časový limit žádosti o snížení modelu analyzuje jednotlivé dotazy a zjistí nějaké zvýšení časových limitů na úrovni spuštění dotazu a celkové limitů požadavek na úrovni databáze ve srovnání s obdobím standardních hodnot výkonu.

Některé dotazy, ještě před nedostanou fázi provádění může vypršení časového limitu. Způsoby přerušených pracovníků oproti požadavkům vestavěné inteligentní SQL Database měří a analyzuje všechny dotazy, které dorazí databázi, jestli tu fázi provádění nebo ne. 

Po počet časových limitů pro spuštění dotazy nebo Počet přerušených požadavek zaměstnanců, kteří protne prahovou hodnotu určí systém, se zobrazí v protokolu diagnostiky inteligentního statistiky.

Statistika generované obsahovat počet požadavků, vypršení časového limitu a počet dotazů, vypršení časového limitu. Znamenat snížení výkonu související s zvýšení časového limitu ve fázi provádění nebo je k dispozici celkové úroveň databáze. Při zvýšení časových limitů považuje za důležité pro výkon databáze, tyto dotazy jsou označeny jako snížení problémy s výkonem časový limit. 

## <a name="excessive-wait-times"></a>Nadměrné čekací doby

Model časové nadměrné čekání monitoruje jednotlivé databázové dotazy. Zjistí neobvykle vysokého dotazu čekání statistiky, které překročí absolutní prahovou hodnotu spravované systému. Následující dotaz nadměrné doba čekání metriky jsou dodržovány pomocí nové funkce systému SQL Server, počkejte statistiky dotaz úložiště (sys.query_store_wait_stats):

- Rozsáhlejší limitů prostředků
- Rozsáhlejší limitů elastického fondu prostředků
- Nadměrný počet vláken worker nebo relace
- Zamykání nadměrné databáze
- Přetížení paměti
- Další statistiky čekání

Rozsáhlejší limitů prostředků nebo limitů elastického fondu prostředků označují, že spotřeba dostupné prostředky na předplatném nebo ve fondu elastické překročí absolutní prahové hodnoty. Tyto statistiky znamenat snížení výkonu zatížení. Vysoký počet vláken worker nebo relace označuje podmínku, ve kterém překročen počet pracovních vláken nebo relace iniciované absolutní prahové hodnoty. Tyto statistiky znamenat snížení výkonu zatížení.

Zamykání nadměrné databáze označuje podmínku, ve kterém překročila počet zámky v databázi absolutní prahové hodnoty. Tato stat označuje snížení zatížení výkonu. Paměti, že zatížení je situace, ve kterém se uděluje počet vláken, které žádají o paměti překročí absolutní prahovou hodnotu. Tato stat označuje snížení zatížení výkonu.

Další statistiky detekce čekání označuje podmínku, ve kterém různé metrika měří prostřednictvím dotazu úložiště počkejte statistiky překročí absolutní prahovou hodnotu. Tyto statistiky znamenat snížení výkonu zatížení.

Po zjištění nadměrného čekací doby, v závislosti na dostupná data, protokolu diagnostiky inteligentního Statistika hodnoty hash výstupy dotazů, které ovlivňují a ovlivněných snížení výkonu, podrobnosti o metriky, které způsobí dotazy čekání při provádění, a Doba čekání měřená.

## <a name="errored-requests"></a>Chyba požadavků

Chyba požadavky snížení model monitorování jednotlivé dotazy a zjistí o zvýšení v počet dotazů, že chyba se porovná s obdobím směrného plánu. Tento model také monitoruje kritické výjimky, které překročí absolutní prahové hodnoty, které spravuje vestavěné inteligentní databáze SQL. Je automaticky za počet požadavků na dotazy provést s databází a účty pro všechny změny úlohy v monitorovaných období.

Když měřená nárůst chyba požadavky relativně k celkový počet zpracovaných požadavků považuje za důležité pro výkon zatížení, ovlivněných dotazy jsou označeny jako snížení problémy s výkonem chybných požadavků.

Protokol inteligentního Statistika výstupy počet chybných požadavků. Označuje, zda byl snížení výkonu související zvýšení chyba požadavky nebo při překročení prahové hodnoty monitorovaných kritické výjimky a měřené čas snížení výkonu. 

Pokud monitorované kritické výjimky křížové absolutní prahové hodnoty spravuje systém, vygeneruje se inteligentní přehledu s podrobnostmi kritické výjimky.

## <a name="next-steps"></a>Další postup
* Zjistěte, jak [řešení potíží s výkonem databáze SQL pomocí služby inteligentního Insights](sql-database-intelligent-insights-troubleshoot-performance.md).
* Použití [inteligentního SQL Database Statistika výkonu diagnostiky protokolu](sql-database-intelligent-insights-use-diagnostics-log.md).
* Zjistěte, jak [monitorovat SQL Database pomocí SQL Analytics](../log-analytics/log-analytics-azure-sql.md).
* Zjistěte, jak [shromažďovat a využívat data protokolu z vašich prostředků Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).


