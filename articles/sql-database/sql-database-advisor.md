---
title: Doporučení k výkonu
description: Azure SQL Database poskytuje doporučení pro databáze SQL, která mohou zlepšit aktuální výkon dotazů.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik
ms.date: 11/12/2019
ms.openlocfilehash: a113ea3fd4828a498d1f53ea7604df7bc8588eb5
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048414"
---
# <a name="performance-recommendations-for-sql-database"></a>Doporučení k výkonu pro SQL Database

Azure SQL Database se učí a přizpůsobí vaší aplikaci. Nabízí přizpůsobená doporučení, která vám umožní maximalizovat výkon vašich databází SQL. SQL Database průběžně vyhodnocuje a analyzuje historii využití vašich databází SQL. Doporučení, která jsou k dispozici, jsou založená na vzorcích úloh jedinečných pro databázi a pomáhá zvýšit výkon.

> [!TIP]
> [Automatické ladění](sql-database-automatic-tuning.md) je doporučovanou metodou pro automatické ladění některých nejběžnějších problémů s výkonem databáze. [Query Performance Insights](sql-database-query-performance.md) je doporučená metoda pro základní Azure SQL Database sledování výkonu. [Azure SQL Analytics](../azure-monitor/insights/azure-sql.md) je doporučená metoda pro pokročilé sledování výkonu databáze ve velkém měřítku s integrovanými inteligentními funkcemi pro řešení potíží s automatickým výkonem.
>

## <a name="performance-recommendations-options"></a>Možnosti doporučení pro výkon

Dostupné možnosti pro doporučení výkonu Azure SQL Database jsou:

| Doporučení pro výkon | Podpora jedné databáze a databáze ve fondu | Podpora databáze instancí |
| :----------------------------- | ----- | ----- |
| **Vytvoření doporučení indexu** – doporučuje vytváření indexů, které můžou zlepšit výkon vašich úloh. | Ano | Ne | 
| **Vyřazení doporučení indexu** – doporučuje, abyste každý den odebrali redundantní a duplicitní indexy, s výjimkou jedinečných indexů a indexů, které se nepoužívaly dlouhou dobu (> 90 dní). Upozorňujeme, že tato možnost není kompatibilní s aplikacemi, které používají přepínání oddílů a parametry indexu. Vyřazování nepoužívaných indexů se u úrovní služeb Premium a Pro důležité obchodní informace nepodporuje. | Ano | Ne |
| **Parametrizovat dotazy doporučení (Preview)** – doporučuje vynucení Vynutit pro případy, kdy máte jeden nebo více dotazů, které jsou neustále znovu kompilovány, ale končí stejným plánem spuštění dotazu. | Ano | Ne |
| **Opravit doporučení pro problémy se schématy (Preview)** – doporučení pro korekci schématu se zobrazí, když služba SQL Database zaznamená anomálii v počtu chyb SQL souvisejících se schématem, které se vyskytují ve vaší databázi SQL. Microsoft momentálně nepoužívá doporučení "opravit problém schématu". | Ano | Ne |

## <a name="create-index-recommendations"></a>Vytvořit doporučení indexu
SQL Database nepřetržitě monitoruje spuštěné dotazy a identifikuje indexy, které by mohly zvýšit výkon. Po dostatečné jistotě, že chybí určitý index, se vytvoří nové doporučení **Create index** .

 Azure SQL Database se zastavuje tak, že se odhaduje zvýšení výkonu, které by index přinesl v čase. V závislosti na odhadovaném nárůstu výkonu jsou doporučení zařazená do kategorií vysoká, střední nebo nízká. 

Indexy vytvořené pomocí doporučení jsou vždy označeny jako automaticky vytvořené indexy. Automatické vytváření indexů můžete zobrazit v zobrazení sys. indexy. Automaticky vytvořené indexy neblokují příkazy ALTER/Rename. 

Pokud se pokusíte odstranit sloupec, který obsahuje automaticky vytvořený index, příkaz projde. Automaticky vytvořený index se zahodí i s příkazem. Pravidelné indexy zablokují příkaz ALTER/rename pro sloupce, které jsou indexované.

Po použití doporučení Create index Azure SQL Database porovnává výkon dotazů s výkonem směrného plánu. Pokud nový index zvyšuje výkon, doporučení je označeno jako úspěšné a sestava dopadu je k dispozici. Pokud index nezlepšil výkon, je automaticky vrácen. SQL Database tento proces používá k zajištění, že doporučení zlepšují výkon databáze.

Jakékoli doporučení **Create index** obsahuje back-vypnutou zásadu, která neumožňuje použití doporučení, pokud je využití prostředků databáze nebo fondu vysoké. Back-výstupní zásada bere v úvahu procesor, data v/v, protokol a úložiště k dispozici. 

Pokud je procesor, v/v/v/v/v/v za posledních 30 minut vyšší než 80%, doporučení Create index se odloží. Pokud bude dostupné úložiště nižší než 10% po vytvoření indexu, doporučení se dostane do chybového stavu. Pokud po několika dnech se automatické ladění stále domnívá, že by byl index užitečný, proces se znovu spustí. 

Tento proces se opakuje, dokud nebude k dispozici dostatek dostupných úložišť pro vytvoření indexu, nebo dokud se index nezobrazuje jako dostupný.

## <a name="drop-index-recommendations"></a>Vyřadit doporučení indexu
Kromě detekce chybějících indexů SQL Database průběžně analyzují výkon stávajících indexů. Pokud se index nepoužívá, Azure SQL Database ho doporučuje přetažení. Vyřazení indexu se doporučuje ve dvou případech:
* Index je duplikátem jiného indexu (stejný indexovaný a zahrnutý sloupec, schéma oddílu a filtry).
* Index se nepoužil pro prodloužené období (93 dní).

Vyřadit doporučení indexu také po implementaci projít ověřením. Pokud se výkon zlepší, je sestava dopadu k dispozici. Pokud dojde ke snížení výkonu, doporučení se vrátí.

## <a name="parameterize-queries-recommendations-preview"></a>Parametrizovat dotazy na doporučení (Preview)
*Parametrizovat dotazy* doporučení se zobrazí, když máte jeden nebo více dotazů, které jsou neustále znovu kompilovány, ale končí stejným plánem spuštění dotazu. Tato podmínka vytvoří příležitost k použití vynuceného Parametrizace. Vynucené Parametrizace umožňuje, aby se plány dotazů ukládaly do mezipaměti a znovu v budoucnu používaly, což zvyšuje výkon a snižuje využití prostředků. 

Každý dotaz, který je vydaný pro SQL Server počáteční, musí být zkompilován pro generování plánu spuštění. Každý vygenerovaný plán se přidá do mezipaměti plánu. Následná spuštění stejného dotazu může tento plán znovu použít z mezipaměti, což eliminuje nutnost další kompilace. 

Dotazy s neparametrizovanými hodnotami mohou vést k režii na výkon, protože plán spuštění je znovu zkompilován pokaždé, když se neparametrizované hodnoty liší. V mnoha případech se stejné plány spuštění generují stejnými dotazy s různými hodnotami parametrů. Tyto plány jsou však stále samostatně přidány do mezipaměti plánu. 

Proces opětovné kompilace plánů spouštění používá databázové prostředky, zvyšuje dobu trvání dotazu a přetéká mezipaměť plánu. Tyto události zase způsobí vyřazení plánů z mezipaměti. Toto chování SQL Server lze změnit nastavením vynucené možnosti PARAMETRIZACE v databázi. 

Abychom vám pomohli odhadnout dopad tohoto doporučení, máte k dispozici porovnání skutečného využití procesoru a předpokládaného využití procesoru (jako kdyby se doporučení uplatnilo). Toto doporučení vám může pomáhat získat úspory procesoru. Může vám také snížit dobu trvání dotazu a režii pro mezipaměť plánu, což znamená, že další plány mohou zůstat v mezipaměti a znovu použít. Toto doporučení můžete použít rychle tak, že vyberete příkaz **použít** . 

Po použití tohoto doporučení povolíte vynucené Parametrizace v průběhu několika minut v databázi. Spustí monitorovací proces, který trvá přibližně 24 hodin. Po uplynutí této doby uvidíte sestavu ověření. Tato sestava zobrazuje využití procesoru vaší databáze 24 hodin před a po použití doporučení. SQL Database Advisor má bezpečnostní mechanismus, který automaticky vrátí použité doporučení v případě zjištění regrese výkonu.

## <a name="fix-schema-issues-recommendations-preview"></a>Oprava doporučení pro problémy s schématy (Preview)

> [!IMPORTANT]
> Microsoft momentálně nepoužívá doporučení "opravit problém schématu". Doporučujeme použít [Intelligent Insights](sql-database-intelligent-insights.md) k monitorování problémů s výkonem databáze, včetně problémů schématu, které dříve pokryla doporučení "opravit problém schématu".
> 

**Oprava upozornění na problémy s schématem** se zobrazí, když služba SQL Database zaznamená anomálii v počtu chyb SQL souvisejících se schématy, ke kterým dochází ve vaší databázi SQL. Toto doporučení se obvykle zobrazuje, když vaše databáze narazí na více chyb souvisejících se schématem (neplatný název sloupce, neplatný název objektu atd.) během hodiny.

"Problémy se schématem" jsou třídy chyb syntaxe v SQL Server. K nim dochází, když není zarovnaná definice dotazu SQL a definice schématu databáze. Například jeden ze sloupců očekávaných dotazem může chybět v cílové tabulce nebo naopak. 

Doporučení "Oprava potíží se schématem" se zobrazí, když služba Azure SQL Database zaznamená anomálii v počtu chyb SQL souvisejících se schématem, které se probíhají ve vaší databázi SQL. Následující tabulka obsahuje chyby, které se týkají potíží se schématem:

| Kód chyby SQL | Zpráva |
| --- | --- |
| 201 |Procedura nebo funkce*očekává parametr*, který nebyl zadán. |
| 207 |Neplatný název sloupce * |
| 208 |Neplatný název objektu * |
| 213 |Název sloupce nebo počet zadaných hodnot se neshodují s definicí tabulky. |
| 2812 |Uloženou proceduru * se nepovedlo najít. |
| 8144 |Procedura nebo funkce * má příliš mnoho zadaných argumentů. |

## <a name="custom-applications"></a>Vlastní aplikace

Vývojáři mohou zvážit vývoj vlastních aplikací s využitím doporučení o výkonu pro Azure SQL Database. Všechna doporučení uvedená na portálu pro databázi jsou přístupná prostřednictvím rozhraní [Get-AzSqlDatabaseRecommendedAction](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserecommendedaction) API.

## <a name="next-steps"></a>Další kroky
Sledujte vaše doporučení a pokračujte v jejich použití pro zlepšení výkonu. Databázová zatížení jsou dynamická a neustále se mění. SQL Database Advisor nadále sleduje a poskytuje doporučení, která mohou potenciálně zlepšit výkon vaší databáze. 

* Další informace o automatickém ladění databázových indexů a plánů spouštění dotazů najdete v tématu [Azure SQL Database automatické ladění](sql-database-automatic-tuning.md).
* Další informace o automatickém monitorování výkonu databáze pomocí automatizované diagnostiky a analýzy hlavních příčin problémů s výkonem najdete v tématu [Azure SQL Intelligent Insights](sql-database-intelligent-insights.md).
*  Další informace o tom, jak používat doporučení týkající se výkonu v Azure Portal, najdete v tématu [doporučení pro výkon v Azure Portal](sql-database-advisor-portal.md).
* Podívejte se na téma [Přehled výkonu dotazů](sql-database-query-performance.md) , kde se dozvíte, jak a jak zobrazit dopad vašich dotazů na výkon.


