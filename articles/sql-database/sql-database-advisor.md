---
title: Doporučení pro výkon poradce pro databázi pro jednu a sdružené databáze
description: Azure SQL Database poskytuje doporučení pro jednu a sdružené databáze, které můžou zlepšit výkon dotazů v databázi Azure SQL.
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
ms.openlocfilehash: bd7473813722fd413947535413b98d493058634a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214139"
---
# <a name="database-advisor-performance-recommendations-for-single-and-pooled-databases"></a>Doporučení pro výkon Poradce pro databázi pro jednu a sdružené databáze

Azure SQL Database se učí a přizpůsobuje se vaší aplikaci. Pro jednu a sdružené databáze SQL Database má řadu databázových poradců, které poskytují přizpůsobená doporučení, která umožňují maximalizovat výkon. Tito databázoví poradci průběžně vyhodnocují a analyzují historii využití a poskytují doporučení na základě vzorů pracovního vytížení, které pomáhají zlepšit výkon.

## <a name="performance-overview"></a>Přehled výkonu

Přehled výkonu poskytuje souhrn výkonu databáze a pomáhá s laděním výkonu a odstraňováním potíží.

![Přehled výkonu pro Azure SQL Database](./media/sql-database-performance/performance-overview-annotated.png)

- Dlaždice **Doporučení** obsahuje rozpis doporučení pro ladění databáze (tři nejlepší doporučení jsou zobrazena, pokud jich je více). Kliknutím na tuto dlaždici přejdete na **[možnosti doporučení výkonu](sql-database-advisor-portal.md#viewing-recommendations)**.
- Dlaždice **Aktivita optimalizace** poskytuje souhrn probíhajících a dokončených akcí ladění pro vaši databázi a poskytuje rychlý přehled o historii aktivity ladění. Kliknutím na tuto dlaždici přejdete do zobrazení historie ladění pro vaši databázi.
- Dlaždice **automatického ladění** zobrazuje **[konfiguraci automatického ladění](sql-database-automatic-tuning-enable.md)** pro vaši databázi (možnosti ladění, které se automaticky použijí na databázi). Kliknutím na tuto dlaždici se otevře dialogové okno konfigurace automatizace.
- Dlaždice **Databázové dotazy** zobrazuje souhrn výkonu dotazů pro vaši databázi (celkové využití DTU a dotazy s nejvyšší mise využívající hlavní prostředky). Kliknutím na tuto dlaždici přejdete na **[přehled výkonu dotazu](sql-database-query-performance.md)**.

## <a name="performance-recommendation-options"></a>Možnosti doporučení výkonu

Možnosti doporučení výkonu, které jsou k dispozici pro jednu a sdruženou databázi v Azure SQL Database, jsou:

| Doporučení výkonu | Podpora jedné databáze a sdružených databází | Podpora databáze instancí |
| :----------------------------- | ----- | ----- |
| **Vytvořit doporučení indexu** – doporučuje vytvoření indexů, které mohou zlepšit výkon úlohy. | Ano | Ne |
| **Drop index doporučení** – doporučuje odebrání redundantní a duplicitní indexy denně, s výjimkou jedinečné indexy a indexy, které nebyly použity po dlouhou dobu (>90 dny). Vezměte prosím na vědomí, že tato možnost není kompatibilní s aplikacemi pomocí přepínání oddílů a rady pro index. Uvolnění nepoužívaných indexů není podporováno pro úrovně služeb Premium a Business Critical. | Ano | Ne |
| **Parametrizovat dotazy doporučení (preview)** – doporučuje vynucené parametrizace v případech, kdy máte jeden nebo více dotazů, které jsou neustále překompilovány, ale skončí se stejným plánem spuštění dotazu. | Ano | Ne |
| **Oprava problémů schématu doporučení (preview)** – doporučení pro opravu schématu se zobrazí, když služba SQL Database oznámení anomálii v počtu chyb SQL souvisejících se schématem, které se dějí v databázi SQL. Společnost Microsoft je v současné době zastaralá "Opravit problém se schématem" doporučení. | Ano | Ne |

![Doporučení pro výkon pro Azure SQL Database](./media/sql-database-performance/performance-recommendations-annotated.png)

Chcete-li použít doporučení pro zvýšení výkonu, [přečtěte si informace o použití doporučení](sql-database-advisor-portal.md#applying-recommendations). Chcete-li zobrazit stav doporučení, naleznete [v tématu Monitorování operací](sql-database-advisor-portal.md#monitoring-operations).

Můžete také najít úplnou historii ladění akcí, které byly použity v minulosti.

## <a name="create-index-recommendations"></a>Vytvořit doporučení rejstříku

SQL Database průběžně sleduje dotazy, které jsou spuštěny a identifikuje indexy, které by mohly zlepšit výkon. Poté, co je dostatek důvěry, že určitý index chybí, je vytvořen nový **vytvořit index** doporučení.

Azure SQL Database vytváří důvěru odhadem zvýšení výkonu index by přinést časem. V závislosti na odhadovaném zvýšení výkonu jsou doporučení klasifikována jako vysoká, střední nebo nízká.

Indexy, které jsou vytvořeny pomocí doporučení jsou vždy označeny jako automaticky vytvořené indexy. Můžete zjistit, které indexy jsou automaticky vytvořeny při pohledu na [zobrazení sys.indexes](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-indexes-transact-sql). Automaticky vytvořené indexy neblokují příkazy ALTER/RENAME.

Pokud se pokusíte přetáhnout sloupec, který má automaticky vytvořený index nad ním, příkaz projde. Automaticky vytvořený index je vynechán s příkazem také. Pravidelné indexy blokují příkaz ALTER/RENAME u sloupců, které jsou indexovány.

Po použití doporučení vytvořit index Azure SQL Database porovná výkon dotazů s výkonem podle směrného plánu. Pokud nový index zlepšil výkon, doporučení je označeno jako úspěšné a zpráva o dopadu je k dispozici. Pokud index nezlepšil výkon, automaticky se vrátí. SQL Database používá tento proces k zajištění, že doporučení zlepšit výkon databáze.

Jakékoli doporučení **vytvořit index** má zásady back-off, které neumožňuje použití doporučení, pokud je vysoké využití prostředků databáze nebo fondu. Zásady back-off bere v úvahu CPU, vi dat, log IO a dostupné úložiště.

Pokud procesor, vi nebo protokolu VI je vyšší než 80 % v předchozích 30 minutách, je odloženo doporučení indexu vytvoření. Pokud dostupné úložiště bude nižší než 10 % po vytvoření indexu, doporučení přejde do chybového stavu. Pokud po několika dnech automatické ladění stále věří, že index by byl přínosný, proces začíná znovu.

Tento proces se opakuje, dokud není k dispozici dostatek úložiště k vytvoření indexu, nebo dokud index není považován za prospěšné už.

## <a name="drop-index-recommendations"></a>Doporučení pro index poklesu

Kromě zjišťování chybějící indexy SQL Database průběžně analyzuje výkon existujících indexů. Pokud se index nepoužívá, Azure SQL Database doporučuje ho vypustit. Uvolnění indexu se doporučuje ve dvou případech:

- Index je duplikát jiného indexu (stejný indexovaný a zahrnutý sloupec, schéma oddílu a filtry).
- Index nebyl použit delší dobu (93 dní).

Drop index doporučení také projít ověření po implementaci. Pokud se výkon zlepší, je k dispozici zpráva o dopadu. Pokud se výkon sníží, doporučení se vrátí.

## <a name="parameterize-queries-recommendations-preview"></a>Parametrizovat doporučení dotazů (náhled)

*Parametrizovat dotazy* doporučení se zobrazí, pokud máte jeden nebo více dotazů, které jsou neustále překompilovány, ale skončí se stejným plánem spuštění dotazu. Tato podmínka vytvoří příležitost použít vynucenou parametrizaci. Vynucená parametrizace zase umožňuje ukládání plánů dotazů do mezipaměti a opakované použití v budoucnu, což zlepšuje výkon a snižuje využití prostředků.

Každý dotaz, který je vydán proti SQL Server zpočátku musí být zkompilován ke generování plánu spuštění. Každý generovaný plán je přidán do mezipaměti plánu. Následné spuštění stejného dotazu můžete znovu použít tento plán z mezipaměti, což eliminuje potřebu další kompilace.

Dotazy s neparametrizovanými hodnotami mohou vést k režii výkonu, protože plán spuštění je znovu kompilován pokaždé, když se neparametrizované hodnoty liší. V mnoha případech stejné dotazy s různými hodnotami parametrů generovat stejné plány spuštění. Tyto plány jsou však stále samostatně přidány do mezipaměti plánu.

Proces rekompilace plánů provádění používá databázové prostředky, prodlužuje dobu trvání dotazu a přeteče mezipaměť plánu. Tyto události zase způsobit plány, které mají být vyřazeny z mezipaměti. Toto chování serveru SQL Server lze změnit nastavením možnosti vynucené parametrizace v databázi.

Chcete-li odhadnout dopad tohoto doporučení, jsou k dispozici porovnání mezi skutečné využití procesoru a předpokládané využití procesoru (jako kdyby doporučení byly použity). Toto doporučení vám může pomoci dosáhnout úspor procesoru. Může také pomoci snížit dobu trvání dotazu a režii pro mezipaměť plánu, což znamená, že více plánů může zůstat v mezipaměti a znovu použít. Toto doporučení můžete použít rychle výběrem příkazu **Použít.**

Po použití tohoto doporučení umožňuje vynucenou parametrizaci během několika minut v databázi. Spustí monitorovací proces, který trvá přibližně 24 hodin. Po uplynutí této doby se zobrazí sestava ověření. Tato sestava zobrazuje využití procesoru databáze 24 hodin před a po použití doporučení. SQL Database Advisor má bezpečnostní mechanismus, který automaticky vrátí použité doporučení, pokud byla zjištěna regrese výkonu.

## <a name="fix-schema-issues-recommendations-preview"></a>Oprava doporučení problémů schématu (preview)

> [!IMPORTANT]
> Společnost Microsoft je v současné době zastaralá "Opravit problém se schématem" doporučení. Doporučujeme používat [inteligentní přehledy](sql-database-intelligent-insights.md) ke sledování problémů s výkonem databáze, včetně problémů se schématem, které dříve pokrývala doporučení o opravě problému se schématem.

**Oprava problémů schématu** doporučení se zobrazí, když služba SQL Database oznámení anomálie v počtu chyb SQL souvisejících se schématem, které se dějí v databázi SQL. Toto doporučení se obvykle zobrazí, když databáze narazí na více chyb souvisejících se schématem (neplatný název sloupce, neplatný název objektu a tak dále) během jedné hodiny.

"Problémy se schématem" jsou třída syntaktických chyb v SQL Serveru. Dochází k nim, když definice dotazu SQL a definice schématu databáze nejsou zarovnány. Například jeden ze sloupců, které dotaz očekává, může chybět v cílové tabulce nebo naopak.

"Opravit problém schématu" doporučení se zobrazí, když služba Azure SQL Database oznámení anomálie v počtu chyb SQL souvisejících se schématem, které se dějí v databázi SQL. V následující tabulce jsou uvedeny chyby související s problémy se schématem:

| Kód chyby SQL | Zpráva |
| --- | --- |
| 201 |Procedura nebo funkce *' ' očekává parametr ' ,* který nebyl dodán. |
| 207 |Neplatný název sloupce *. |
| 208 |Neplatný název objektu *. |
| 213 |Název sloupce nebo počet zadaných hodnot neodpovídá definici tabulky. |
| 2812 |Uložená procedura nebyla uložena. |
| 8144 |Procedura nebo funkce * má příliš mnoho argumentů zadán. |

## <a name="custom-applications"></a>Vlastní aplikace

Vývojáři mohou zvážit vývoj vlastních aplikací pomocí doporučení výkonu pro Azure SQL Database. Všechna doporučení uvedená na portálu pro databázi lze přistupovat prostřednictvím [Get-AzSqlDatabaseRecommendedAction](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserecommendedaction) API.

## <a name="next-steps"></a>Další kroky

- Další informace o automatickém ladění indexů databází a plánech spuštění dotazů naleznete [v tématu automatické ladění databáze Azure SQL Database](sql-database-automatic-tuning.md).
- Další informace o automatickém sledování výkonu databáze pomocí automatické diagnostiky a analýzy hlavních příčin problémů s výkonem naleznete v [tématu Azure SQL Intelligent Insights](sql-database-intelligent-insights.md).
- Informace o tom, jaký dopad mají hlavní dotazy, najdete v tématu [Přehledy výkonu dotazů.](sql-database-query-performance.md)
