---
title: Monitorování výkonu databáze s Intelligent Insights – Azure SQL Database | Dokumentace Microsoftu
description: Azure SQL Database Intelligent Insights využívá integrované inteligentní funkce neustále monitorovat využití databáze prostřednictvím umělé inteligence a zjišťovat rušivé události, které způsobují nízký výkon.
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
ms.date: 10/05/2018
ms.openlocfilehash: 6969744ff52e9aff9d486d2eab6fffc2c3ac8b74
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53607996"
---
# <a name="intelligent-insights-using-ai-to-monitor-and-troubleshoot-database-performance"></a>Inteligentní přehledy s využitím AI k monitorování a ladění výkonu databáze

Azure SQL Database Intelligent Insights vám umožňuje vědět, co se děje s výkonu vaší databáze SQL Database a Managed Instance.

Intelligent Insights využívá integrované inteligentní funkce neustále monitorovat využití databáze prostřednictvím umělé inteligence a zjišťovat rušivé události, které způsobují nízký výkon. Jakmile detekuje, se provádí podrobné analýzy, která generuje protokol diagnostiky inteligentních hodnocení problému. Toto posouzení se skládá z analýzu původní příčiny potíží s výkonem databáze a kde je to možné, doporučení pro vylepšení výkonu.

## <a name="what-can-intelligent-insights-do-for-you"></a>Co můžete udělat Intelligent Insights pro vás

Intelligent Insights je jedinečné možnosti Azure integrované inteligentní funkce, která poskytuje následující hodnotu:

- Proaktivní monitorování
- Informace o výkonu pro míru
- Včasnou detekci snížení výkonu databáze
- Hlavní příčina analýzy nebyly zjištěny problémy
- Doporučení pro vylepšení výkonu
- Horizontální navýšení kapacity schopností na stovky tisíc databází
- Pozitivní dopad prostředky DevOps a celkové náklady na vlastnictví

## <a name="how-does-intelligent-insights-work"></a>Jak inteligentní přehledy funguji

Intelligent Insights analyzuje porovnáním databázové úlohy od poslední hodiny s úlohou posledních sedm dní standardních hodnot výkonu databáze. Databázové úlohy se skládá z dotazů, jestli se má být nejvýznamnější na výkon databází, jako jsou dotazy nejvíce opakovaných a největší. Protože každá databáze je jedinečný, na základě struktury, data, použití a aplikace, je každý směrný plán úloh, který je generován konkrétní a jedinečné pro jednotlivé instance. Intelligent Insights, nezávisle na základní úlohy také sleduje absolutní provozní prahové hodnoty a detekuje problémy s nadměrným čekací dobu, kritické výjimky a problémy s parameterizations dotazu, které může mít vliv na výkon.

Po snížení problému s výkonem se zjistí z několika zjištěnou metrik pomocí umělé inteligence, provedení analýzy. Diagnostický protokol je generována pomocí inteligentních přehledů na co se děje s vaší databáze. Intelligent Insights umožňuje snadno sledovat potíže s výkonem databáze z jeho první vzhled až do jejich vyřešení. Každé zjištěné potíže jsou sledovány prostřednictvím jejich životního cyklu od počátečního problém rozpoznávání a ověření zlepšení výkonu na jeho dokončení.

![Pracovní postup analýzy výkonu databáze](./media/sql-database-intelligent-insights/intelligent-insights-concept.png)

Metriky, používá k měření a zjištění problémů s výkonem databáze jsou založeny na doba trvání dotazu, časový limit žádosti, nadměrné čekací dobu a chybných žádostí. Další informace týkající se metrik najdete v tématu [detekce metriky](sql-database-intelligent-insights.md#detection-metrics) část tohoto dokumentu.

Identifikuje SQL Database přehledu výkonu jsou zaznamenány v protokolu diagnostiky inteligentních záznamy, které se skládají z následujících vlastností:

| Vlastnost             | Podrobnosti              |
| :------------------- | ------------------- |
| informace o databázi | Metadata o databázi, na kterém byl zjištěn přehledů, jako je identifikátor URI prostředku. |
| Sledovaného časového rozsahu | Počáteční a koncový čas pro období zjištěné insight. |
| Ovlivněné metriky | Metriky, který způsobil vygenerování přehledů: <ul><li>Doba trvání zvýšení [sekund] dotazu.</li><li>Nadměrné čekání [sekund].</li><li>Vypršel časový limit žádosti [procento].</li><li>Požadavky s chybou [procento].</li></ul>|
| Hodnota dopad | Měří hodnota metriky. |
| Ovlivněné dotazy a kódy chyb | Dotaz na hodnotu hash nebo kód chyby. To umožňuje snadno porovnat pro ovlivněné dotazy. Metriky, které se skládají z zvýšení doby trvání dotazu, čekání, vypršení časového limitu počtu nebo kódy chyb jsou k dispozici. |
| Nalezení | Detekce určí v databázi v době události. Existují 15 detekce vzory. Další informace najdete v tématu [databáze odstraňování problémů s výkonem Intelligent insights](sql-database-intelligent-insights-troubleshoot-performance.md). |
| Analýza původní příčiny | Hlavní příčina analýzy problému zjištěného v lidsky čitelném formátu. Některé insights může obsahovat doporučení zlepšení výkonu, kde je to možné. |
|||

Praktické přehled o používání Intelligent Insights s Azure SQL Analytics a typické scénáře využití najdete v části vložené video:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-Intelligent-Insights-for-Improving-Azure-SQL-Database-Performance/player]
>

Intelligent Insights oslní zjišťování a řešení potíží s výkonem SQL Database. Pokud chcete použít k SQL Database a spravované instanci databáze odstraňování problémů s výkonem Intelligent Insights, naleznete v tématu [Poradce při potížích s Azure SQL Database problémy s výkonem Intelligent insights](sql-database-intelligent-insights-troubleshoot-performance.md).

## <a name="configure-intelligent-insights"></a>Konfigurace Intelligent Insights

Výstup Intelligent Insights je protokol diagnostiky inteligentních výkonu. Tento protokol mohou být spotřebovány v několika způsoby – streamování do Azure SQL Analytics, Azure Event Hubs a Azure storage nebo třetí strana produktu.

- Používání produktu s [Azure SQL Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql) zobrazíte insights prostřednictvím uživatelského rozhraní na webu Azure portal. Toto je integrované řešení Azure a obvykle způsob, jak zobrazit přehledy.
- Použití produktu pomocí Azure Event Hubs pro vývoj vlastní monitorování a upozorňování scénáře
- Používání produktu pomocí úložiště Azure pro vývoj vlastních aplikací, jako jsou třeba vlastní data pro generování sestav, dlouhodobé archivace a tak dále.

Integrace inteligentní přehledy s jinými produkty pro Azure SQL Analytics, Azure Event Hubs, Azure storage nebo produkty třetích stran pro použití se provádí prostřednictvím první povolení Intelligent Insights protokolování (protokol "SQLInsights") v diagnostiky okno nastavení databáze a pak nakonfigurujete Intelligent Insights vytvářet protokoly dat Streamovat do jednoho z těchto produktů.

Další informace o povolení protokolování Intelligent Insights a konfigurace dat protokolu Streamovat k používání produktu najdete v tématu [Azure SQL Database metrik a protokolování diagnostiky](sql-database-metrics-diag-logging.md).

### <a name="set-up-with-azure-sql-analytics"></a>S Azure SQL Analytics

Řešení Azure SQL Analytics poskytuje grafické uživatelské rozhraní, vytváření sestav a upozorňování na výkonu databáze, spolu s Intelligent Insights diagnostické protokoly data.

> [!TIP]
> Rychlé zahájení práce: Nejjednodušší způsob, jak získat firmu s využitím inteligentních přehledů je použít společně s Azure SQL Analytics, která bude poskytovat grafické uživatelské rozhraní pro problémy s výkonem databáze. Přidat řešení Azure SQL Analytics z webu marketplace, vytvořit pracovní prostor uvnitř tohoto řešení a potom pro každou databázi, kterého chcete povolit inteligentní přehledy na, nakonfigurujte vysílání datového proudu protokolu "SQLInsights" v okně nastavení diagnostiky nové databáze pracovní prostor služby Azure SQL Analytics.
>

Před požadavkem je mít Azure SQL Analytics přidat na řídicí panel portálu Azure z webu marketplace a chcete vytvořit pracovní prostor, přečtěte si téma [konfigurovat Azure SQL Analytics](../azure-monitor/insights/azure-sql.md#configuration)

Intelligent Insights s Azure SQL Analytics, nakonfigurovat data protokolu Intelligent Insights odesílání do pracovního prostoru Azure SQL Analytics, které jste vytvořili v předchozím kroku, naleznete v tématu [metriky Azure SQL Database a Diagnostika protokolování](sql-database-metrics-diag-logging.md).

Následující příklad ukazuje, že Intelligent Insights zobrazit pomocí Azure SQL Analytics:

![Intelligent Insights sestavy](./media/sql-database-intelligent-insights/intelligent-insights-azure-sql-analytics.png)

### <a name="set-up-with-event-hubs"></a>Nastavení se službou Event Hubs

Intelligent Insights pomocí služby Event Hubs, konfigurace dat protokolu Intelligent Insights Streamovat do služby Event Hubs najdete v tématu [diagnostické protokoly Stream Azure do služby Event Hubs](../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md).

Nastavit vlastní monitorování a upozorňování pomocí služby Event Hubs naleznete v části [co dělat s Diagnostika a metriky protokolů ve službě Event Hubs](sql-database-metrics-diag-logging.md#what-to-do-with-metrics-and-diagnostics-logs-in-event-hubs).

### <a name="set-up-with-azure-storage"></a>S Azure Storage

Intelligent Insights s úložištěm, nakonfigurovat data protokolů Intelligent Insights Streamovat do úložiště, přečtěte si téma [Stream do služby Azure Storage](sql-database-metrics-diag-logging.md#stream-into-storage).

### <a name="custom-integrations-of-intelligent-insights-log"></a>Vlastní integrace protokolu Intelligent Insights

Intelligent Insights používat s nástroji třetích stran nebo vlastních výstrah a monitorování vývoj, naleznete v tématu [použití protokolování diagnostiky výkonu databáze Intelligent Insights](sql-database-intelligent-insights-use-diagnostics-log.md).

## <a name="detection-metrics"></a>Detekce metriky

Metriky pro zjišťování modely, které generují Intelligent Insights jsou založené na monitorování:

- Doba trvání dotazu
- Časový limit žádosti
- Nadměrné čekací doba
- Došlo k chybě žádosti

Doba trvání a časový limit požadavků na dotazy se používají jako primární modely ke zjišťování problémů s výkonem úloh databáze. Vzhledem k tomu, že přímo měří, co se děje s úlohou slouží. Ke zjištění všech možných případů výkon úloh snížení nadměrné čekací dobu a s chybou požadavky se používají jako další modely označuje problémy, které mají vliv na výkon úloh.

Systém automaticky bere v úvahu změny na zatížení a změny počtu požadavků na dotazy dynamicky provedených v databázi k určení prahových hodnot výkonu databáze normální a mimo běžný.

Některé metriky jsou považovány za společně v různých relacích prostřednictvím vědecky odvozené datový model, který slouží ke kategorizaci každý zjistil problémy s výkonem. Informací získaných prostřednictvím inteligentních přehledů zahrnuje:

- Podrobnosti o zjištěném problému výkonu.
- Analýza původní příčiny problému, zjistili.
- Doporučení pro zlepšení výkonu monitorované SQL database, kde je to možné.

## <a name="query-duration"></a>Doba trvání dotazu

Model snížení doby trvání dotazu analyzuje jednotlivé dotazy a detekuje v čas potřebný ke kompilaci a spuštění dotazu porovnané se standardními hodnotami výkonu vyšší.

Pokud integrované inteligentní funkce SQL Database zjistí výrazné zvýšení v kompilaci dotazu nebo doba provádění dotazu, který má vliv na výkon úloh, tyto dotazy se označí jako doba trvání dotazu problémech se snižováním výkonu.

Protokol diagnostiky inteligentních přehledů uloží hodnota hash dotazu dotazu snížení výkonu. Hodnota hash dotazu určuje, zda byl snížení výkonu související s kompilace nebo spuštění dotazu zvýšení, která zvýšil doba trvání dotazu.

## <a name="timeout-requests"></a>Časový limit žádosti

Časový limit žádosti o snížení model analyzuje jednotlivé dotazy a detekuje nějaký nárůst v časové limity na úrovni spuštění dotazu a celkový časový limit požadavku na úrovni databáze, ve srovnání s obdobím standardních hodnot výkonu.

Některé dotazy může být vypršení časového limitu ještě dřív, než dorazí fáze spuštění. Prostředky přerušené pracovních procesů a požadavky SQL Database integrované inteligentní funkce opatření a analyzuje všech dotazů, které bylo dosaženo databáze, jestli máte do fáze spuštění nebo ne.

Po počet vypršení časového limitu pro spuštěné dotazy nebo počet pracovních procesů přerušené požadavku překročí prahovou hodnotu spravovaných system, diagnostický protokol se vyplní inteligentní přehledy.

Přehledy vytvořené obsahovat počet požadavků, vypršel časový limit a počet dotazů vypršel časový limit. Údaj o snížení výkonu má vztah k zvýšení časového limitu ve fázi provádění nebo je k dispozici celkovou úroveň databáze. Při zvýšení časových limitů je považován za důležité pro výkon databáze, tyto dotazy se označí jako problémech se snižováním výkonu vypršení časového limitu.

## <a name="excessive-wait-times"></a>Nadměrné čekací dobu

Model nadměrné čekací čas monitoruje jednotlivých databázových dotazů. Zjistí statistiky čekání neobvykle vysokého dotazu, které překročí prahové spravovaných system absolutní hodnoty. Následující dotaz nadměrné doba čekání metriky jsou dodržovány pomocí nové funkce systému SQL Server, počkejte statistiky Query Store (sys.query_store_wait_stats):

- Dosáhnout omezení prostředků
- Rozsáhlejší limity elastického fondu prostředků
- Nadměrný počet pracovních procesů nebo relace vláken
- Uzamčení nadměrné databáze
- Přetížení paměti
- Další statistiky čekání

Dosáhnout omezení prostředků nebo prostředek limity elastického fondu označují, že spotřeby dostupných prostředků v rámci předplatného nebo do elastického fondu překročen absolutní prahové hodnoty. Na tyto statistiky znamenat snížení výkonu úloh. Nadměrný počet pracovních procesů nebo relace vláken označuje podmínku, ve kterém překročen počet pracovních vláken nebo relace iniciované absolutní prahové hodnoty. Na tyto statistiky znamenat snížení výkonu úloh.

Uzamčení nadměrné databáze označuje podmínku, ve kterém má překročen počet zámků na databázi absolutní prahové hodnoty. Tento stat indikuje snížení výkonu úloh. Paměti, že je tlak podmínku, ve kterém se uděluje počet vláken, požadování paměti překročí absolutní prahovou hodnotu. Tento stat indikuje snížení výkonu úloh.

Další zjišťování statistiky čekání označuje podmínku, ve kterém různé metriky měří prostřednictvím statistiky čekání se Query Store překročen absolutní prahovou hodnotu. Na tyto statistiky znamenat snížení výkonu úloh.

Po zjištění nadměrného čekací dobu, podle toho, aby byla data dostupná, protokolu diagnostiky inteligentních přehledů hodnoty hash výstupy dopad a ovlivněné dotazů snížení výkonu, podrobností, která způsobí dotazy čekat ve spuštění, a měřené čekací doba.

## <a name="errored-requests"></a>Chybných žádostí

Model snížení chybných žádostí v jednotlivých monitorování dotazů a zjistí nárůst počtu dotazů, že došlo k chybě ve srovnání s směrné období. Tento model také sleduje kritické výjimky, které překročí absolutní prahové hodnoty, které spravuje integrované inteligentní funkce SQL Database. Systém automaticky bere v úvahu počet požadavků na dotazy do databáze a účty pro všechny úlohy změny provedené v monitorovaných období.

Když měřené zvýšení chybných žádostí vzhledem k celkový počet zpracovaných požadavků je považován za důležité pro výkon úloh, ovlivněné dotazy se označí jako problémech se snižováním výkonu chybných žádostí.

Intelligent Insights protokolů výstupů počet chybných žádostí. Znamená to, jestli snížení výkonu byl související s prodloužením chybných žádostí nebo překračuje prahovou hodnotu monitorovaných kritické výjimky a měřitelných čas snížení výkonu.

Pokud některý z monitorovaných kritické výjimky pro různé absolutní prahové hodnoty, které jsou spravované systémem, vygeneruje se s podrobnostmi o kritické výjimky inteligentních přehledů.

## <a name="next-steps"></a>Další postup

- Zjistěte, jak [řešení problémů s výkonem SQL Database s Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md).
- Použití [protokolování diagnostiky výkonu Intelligent Insights SQL Database](sql-database-intelligent-insights-use-diagnostics-log.md).
- Zjistěte, jak [monitorování SQL Database s použitím SQL Analytics](../azure-monitor/insights/azure-sql.md).
- Zjistěte, jak [shromažďovat a zpracovávat data protokolu z vašich prostředků Azure](../azure-monitor/platform/diagnostic-logs-overview.md).
