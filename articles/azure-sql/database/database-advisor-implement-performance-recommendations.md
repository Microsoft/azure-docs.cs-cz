---
title: Doporučení pro výkon služby Database Advisor pro Azure SQL Database
description: Azure SQL Database poskytuje doporučení pro databáze, které mohou zlepšit výkon dotazů v Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: fasttrack-edit, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 03/10/2020
ms.openlocfilehash: 79ccf0f8aae7e915601081f875cea294de52d787
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96500848"
---
# <a name="database-advisor-performance-recommendations-for-azure-sql-database"></a>Database Advisor doporučení k výkonu pro Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database se učí a přizpůsobí vaší aplikaci. Azure SQL Database má mnoho poradců databází, které poskytují přizpůsobená doporučení, která vám umožní maximalizovat výkon. Tyto informační zpravodaje budou průběžně hodnotit a analyzovat historii využití a poskytovat doporučení na základě vzorců úloh, které pomůžou zvýšit výkon.

## <a name="performance-overview"></a>Přehled výkonu

Přehled výkonu poskytuje souhrn výkonu vaší databáze a pomáhá s optimalizací výkonu a řešením potíží.

![Přehled výkonu pro Azure SQL Database](./media/database-advisor-implement-performance-recommendations/performance-overview-annotated.png)

- Dlaždice **doporučení** obsahuje rozpis doporučení pro ladění pro vaši databázi (pokud existuje více), zobrazí se v nich více hlavních tří doporučení. Kliknutím na tuto dlaždici přejdete na **[Možnosti doporučení pro výkon](database-advisor-find-recommendations-portal.md#viewing-recommendations)**.
- Dlaždice **aktivity ladění** poskytuje shrnutí probíhajících a dokončených akcí optimalizace pro vaši databázi, takže získáte rychlý přehled o historii aktivity ladění. Kliknutím na tuto dlaždici přejdete k úplnému zobrazení Historie ladění pro vaši databázi.
- Dlaždice **automatického ladění** zobrazuje **[konfiguraci automatického ladění](automatic-tuning-enable.md)** pro vaši databázi (možnosti ladění, které se automaticky aplikují na vaši databázi). Kliknutím na tuto dlaždici otevřete dialogové okno Konfigurace automatizace.
- Dlaždice **dotazy databáze** zobrazuje souhrn výkonu dotazů pro vaši databázi (celkové využití DTU a nejčastější dotazy týkající se využívání prostředků). Kliknutím na tuto dlaždici přejdete na **[Query Performance Insight](query-performance-insight-use.md)**.

## <a name="performance-recommendation-options"></a>Možnosti doporučení výkonu

Možnosti pro doporučení výkonu dostupné v Azure SQL Database jsou:

| Doporučení pro výkon | Podpora jedné databáze a databáze ve fondu | Podpora databáze instancí |
| :----------------------------- | ----- | ----- |
| **Vytvoření doporučení indexu** – doporučuje vytváření indexů, které můžou zlepšit výkon vašich úloh. | Yes | No |
| **Vyřazení doporučení indexu** – doporučuje, abyste každý den odebrali redundantní a duplicitní indexy, s výjimkou jedinečných indexů a indexů, které se nepoužívaly dlouhou dobu (>90 dní). Upozorňujeme, že tato možnost není kompatibilní s aplikacemi, které používají přepínání oddílů a parametry indexu. Vyřazování nepoužívaných indexů se u úrovní služeb Premium a Pro důležité obchodní informace nepodporuje. | Yes | No |
| **Parametrizovat dotazy doporučení (Preview)** – doporučuje vynucené Parametrizace v případech, kdy máte jeden nebo více dotazů, které jsou neustále znovu kompilovány, ale končí stejným plánem spuštění dotazu. | Yes | No |
| **Opravit doporučení pro problémy se schématy (Preview)** – doporučení pro korekci schématu se zobrazí, když Azure SQL Database vydává upozornění na anomálii v počtu chyb SQL souvisejících se schématy, které se vyskytují ve vaší databázi. Microsoft momentálně nepoužívá doporučení "opravit problém schématu". | Yes | No |

![Doporučení k výkonu pro Azure SQL Database](./media/database-advisor-implement-performance-recommendations/performance-recommendations-annotated.png)

Pokud chcete použít doporučení pro výkon, přečtěte si téma [použití doporučení](database-advisor-find-recommendations-portal.md#applying-recommendations). Stav doporučení zobrazíte v tématu [monitorování operací](database-advisor-find-recommendations-portal.md#monitoring-operations).

Můžete také najít úplnou historii akcí optimalizace, které byly v minulosti aplikovány.

## <a name="create-index-recommendations"></a>Vytvořit doporučení indexu

Azure SQL Database nepřetržitě monitoruje spuštěné dotazy a identifikuje indexy, které by mohly zvýšit výkon. Po dostatečné jistotě, že chybí určitý index, se vytvoří nové doporučení **Create index** .

Azure SQL Database se zastavuje tak, že se odhaduje zvýšení výkonu, které by index přinesl v čase. Doporučení se řadí do tří kategorií podle odhadovaného nárůstu výkonu: vysoký, střední a nízký.

Indexy vytvořené pomocí doporučení jsou vždy označeny jako automaticky vytvořené indexy. Automatické vytváření indexů můžete zobrazit v [zobrazení sys. indexy](/sql/relational-databases/system-catalog-views/sys-indexes-transact-sql). Automaticky vytvořené indexy neblokují příkazy ALTER/Rename.

Pokud se pokusíte odstranit sloupec, který obsahuje automaticky vytvořený index, příkaz projde. Automaticky vytvořený index se zahodí i s příkazem. Pravidelné indexy zablokují příkaz ALTER/rename pro sloupce, které jsou indexované.

Po použití doporučení Create index Azure SQL Database porovnává výkon dotazů s výkonem směrného plánu. Pokud nový index zvyšuje výkon, doporučení je označeno jako úspěšné a sestava dopadu je k dispozici. Pokud index nezlepšil výkon, je automaticky vrácen. Azure SQL Database tento proces používá k zajištění, že doporučení zlepšují výkon databáze.

Jakékoli doporučení **Create index** obsahuje back-vypnutou zásadu, která neumožňuje použití doporučení, pokud je využití prostředků databáze nebo fondu vysoké. Back-výstupní zásada bere v úvahu procesor, data v/v, protokol a úložiště k dispozici.

Pokud je procesor, v/v/v/v/v/v za posledních 30 minut vyšší než 80%, doporučení Create index se odloží. Pokud bude dostupné úložiště nižší než 10% po vytvoření indexu, doporučení se dostane do chybového stavu. Pokud po několika dnech se automatické ladění stále domnívá, že by byl index užitečný, proces se znovu spustí.

Tento proces se opakuje, dokud nebude k dispozici dostatek dostupných úložišť pro vytvoření indexu, nebo dokud se index nezobrazuje jako dostupný.

## <a name="drop-index-recommendations"></a>Vyřadit doporučení indexu

Kromě detekce chybějících indexů Azure SQL Database průběžně analyzují výkon stávajících indexů. Pokud se index nepoužívá, Azure SQL Database ho doporučuje přetažení. Vyřazení indexu se doporučuje ve dvou případech:

- Index je duplikátem jiného indexu (stejný indexovaný a zahrnutý sloupec, schéma oddílu a filtry).
- Index se nepoužil pro prodloužené období (93 dní).

Vyřadit doporučení indexu také po implementaci projít ověřením. Pokud se výkon zlepší, je sestava dopadu k dispozici. Pokud dojde ke snížení výkonu, doporučení se vrátí.

## <a name="parameterize-queries-recommendations-preview"></a>Parametrizovat dotazy na doporučení (Preview)

*Parametrizovat dotazy* doporučení se zobrazí, když máte jeden nebo více dotazů, které jsou neustále znovu kompilovány, ale končí stejným plánem spuštění dotazu. Tato podmínka vytvoří příležitost k použití vynuceného Parametrizace. Vynucené Parametrizace umožňuje, aby se plány dotazů ukládaly do mezipaměti a znovu v budoucnu používaly, což zvyšuje výkon a snižuje využití prostředků.

Každý první dotaz musí být zkompilován, aby vygeneroval plán spuštění. Každý vygenerovaný plán se přidá do mezipaměti plánu. Následná spuštění stejného dotazu může tento plán znovu použít z mezipaměti, což eliminuje nutnost další kompilace.

Dotazy s neparametrizovanými hodnotami mohou vést k režii výkonu, protože plán spuštění se překompiluje vždy, když se neparametrizované hodnoty liší. V mnoha případech stejné dotazy s různými hodnotami parametrů generují stejné plány spuštění. Tyto plány se ale stále přidávají do mezipaměti plánu samostatně.

Proces překompilování plánů spuštění používá databázové prostředky, prodlužuje dobu trvání dotazu a přetéká mezipaměť plánu. Tyto události zase způsobují vyřazení plánů z mezipaměti. Toto chování lze změnit nastavením vynucené možnosti PARAMETRIZACE v databázi.

Abychom vám pomohli odhadnout dopad tohoto doporučení, máte k dispozici porovnání skutečného využití procesoru a předpokládaného využití procesoru (jako kdyby se doporučení uplatnilo). Toto doporučení vám může pomáhat získat úspory procesoru. Může vám také snížit dobu trvání dotazu a režii pro mezipaměť plánu, což znamená, že další plány mohou zůstat v mezipaměti a znovu použít. Toto doporučení můžete použít rychle tak, že vyberete příkaz **použít** .

Po použití tohoto doporučení povolíte vynucené Parametrizace v průběhu několika minut v databázi. Spustí monitorovací proces, který trvá přibližně 24 hodin. Po uplynutí této doby uvidíte sestavu ověření. Tato sestava zobrazuje využití procesoru vaší databáze 24 hodin před a po použití doporučení. Azure SQL Database Advisor má bezpečnostní mechanismus, který automaticky vrátí použité doporučení v případě zjištění regrese výkonu.

## <a name="fix-schema-issues-recommendations-preview"></a>Oprava doporučení pro problémy s schématy (Preview)

> [!IMPORTANT]
> Microsoft momentálně nepoužívá doporučení "opravit problém schématu". Doporučujeme použít [Intelligent Insights](intelligent-insights-overview.md) k monitorování problémů s výkonem databáze, včetně problémů schématu, které dříve pokryla doporučení "opravit problém schématu".

**Oprava upozornění na problémy s schématem** se zobrazí, když Azure SQL Database zaznamená anomálii v počtu chyb SQL souvisejících se schématem, které se vyskytují ve vaší databázi. Toto doporučení se obvykle zobrazuje, když vaše databáze narazí na více chyb souvisejících se schématem (neplatný název sloupce, neplatný název objektu atd.) během hodiny.

"Problémy se schématem" jsou třídy chyb syntaxe. K nim dochází, když není zarovnaná definice dotazu SQL a definice schématu databáze. Například jeden ze sloupců očekávaných dotazem může chybět v cílové tabulce nebo naopak.

Doporučení "Oprava potíží se schématem" se zobrazí, když Azure SQL Database vydává upozornění na anomálii v počtu chyb SQL souvisejících se schématy, které se ve vaší databázi probíhají. Následující tabulka obsahuje chyby, které se týkají potíží se schématem:

| Kód chyby SQL | Zpráva |
| --- | --- |
| 201 |Procedura nebo funkce *očekává parametr*, který nebyl zadán. |
| 207 |Neplatný název sloupce * |
| 208 |Neplatný název objektu * |
| 213 |Název sloupce nebo počet zadaných hodnot se neshodují s definicí tabulky. |
| 2812 |Uloženou proceduru * se nepovedlo najít. |
| 8144 |Procedura nebo funkce * má příliš mnoho zadaných argumentů. |

## <a name="custom-applications"></a>Vlastní aplikace

Vývojáři mohou zvážit vývoj vlastních aplikací s využitím doporučení o výkonu pro Azure SQL Database. Všechna doporučení uvedená na portálu pro databázi jsou přístupná prostřednictvím rozhraní [Get-AzSqlDatabaseRecommendedAction](/powershell/module/az.sql/get-azsqldatabaserecommendedaction) API.

## <a name="next-steps"></a>Další kroky

- Další informace o automatickém ladění databázových indexů a plánů spouštění dotazů najdete v tématu [Azure SQL Database automatické ladění](automatic-tuning-overview.md).
- Další informace o automatickém monitorování výkonu databáze pomocí automatizované diagnostiky a analýzy hlavních příčin problémů s výkonem najdete v tématu [Azure SQL Intelligent Insights](intelligent-insights-overview.md).
- Podívejte se na téma [Přehled výkonu dotazů](query-performance-insight-use.md) , kde se dozvíte, jak a jak zobrazit dopad vašich dotazů na výkon.