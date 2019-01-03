---
title: Doporučení k výkonu – Azure SQL Database | Dokumentace Microsoftu
description: Azure SQL Database poskytuje doporučení pro vaše databáze SQL, které může zlepšit výkon aktuální dotaz.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: f55d3eb67bb9c494a3d055e39d970c9a440dfc67
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53603427"
---
# <a name="performance-recommendations-for-sql-database"></a>Doporučení k výkonu pro SQL Database

Azure SQL Database se učí a adaptuje spolu s vaší aplikace. Poskytuje přizpůsobená doporučení, které vám umožní maximalizovat výkon vašich databází SQL. SQL Database průběžně vyhodnocuje a analyzuje historie využití vašich databází SQL. Doporučení, které jsou k dispozici jsou založeny na vzorce jedinečné databázové úlohy a Pomozte nám vylepšit výkon.

> [!TIP]
> [Automatické ladění](sql-database-automatic-tuning.md) je doporučená metoda automaticky vyladit některé z nejběžnějších problémů s výkonem databáze. [Informace o výkonu dotazování](sql-database-query-performance.md) je doporučená metoda pro potřeby monitorování základní výkonem Azure SQL Database. [Azure SQL Analytics](../azure-monitor/insights/azure-sql.md) je doporučená metoda pro rozšířené monitorování výkonu databáze ve velkém měřítku, s integrovanými inteligentními funkcemi pro řešení potíží s výkonem automatizované.
>

## <a name="create-index-recommendations"></a>Vytvoření doporučení indexu
SQL Database průběžně monitoruje dotazy, které jsou spuštěny a identifikuje indexy, které může zlepšit výkon. Po dostatek jistotu, že některé index chybí, nový **vytvořit index** , vytvoří se doporučení.

 Azure SQL Database vytvoří spolehlivosti odhadem zvýšení výkonu, které by index přinesl průběhu času. V závislosti na tom, největší odhadované zvýšení výkonu doporučení se zařazených do kategorií vysoká, střední nebo Nízká. 

Indexy, které jsou vytvářeny instalační sadou doporučení jsou vždy příznakem automaticky vytvořený indexy. Můžete zobrazit, které indexy jsou automaticky vytvořený zobrazením sys.indexes zobrazení. Automaticky vytvořený indexy nedošlo k blokování příkazy ALTER/přejmenovat. 

Při pokusu vyřadit sloupec, který se má automaticky vytvořený index nad ním, předá tento příkaz. Automaticky vytvořený index se zrušil a příkazem. Příkaz ALTER/přejmenování sloupců, která jsou indexována bloku obyčejné indexy.

Po použití doporučení vytvořit index Azure SQL Database porovnán výkon dotazů, které se základním výkonem. Pokud nový index vyšší výkon, doporučení je označený jako úspěšně dokončený a dopad sestava je k dispozici. Pokud je index nebyl zvýšit výkon, se automaticky vrátil. SQL Database používá tento proces k zajištění, že doporučení pro vylepšení výkonu databáze.

Žádné **vytvořit index** doporučení obsahuje regresní zásadu, která neumožňuje použití doporučení, pokud je vysoké využití prostředků databáze nebo fondu. Regresní zásady bere v úvahu CPU, datové v/v, v/v protokolu a úložiště k dispozici. 

Pokud procesor, datové v/v nebo v/v protokolu je vyšší než 80 % v předchozích 30 minut, odloží se doporučení vytvořit index. Pokud úložiště k dispozici bude mít míň než 10 %, po vytvoření indexu, doporučení přejde do chybového stavu. Pokud po několika dnech, automatické ladění stále se řídí zásadou, že by byly užitečné index, opakujte spuštění procesu. 

Tento proces se opakuje, dokud není dostatek dostupného úložiště k vytvoření indexu, nebo index není viditelná jako užitečné zobrazovat.

## <a name="drop-index-recommendations"></a>Vyřadit doporučení indexu
Kromě zjišťování chybějících indexů, SQL Database průběžně analyzuje výkonu stávající indexy. Pokud není použit index, doporučuje Azure SQL Database, zastaví. V obou případech se doporučuje odstranit index:
* Index je duplikátem jiného indexu (stejné indexovat barvy a zahrnuté sloupce, schéma oddílu a filtry).
* Index nebyl použit po delší dobu (93 dnů).

Doporučení indexu rozevírací taky projít ověřování po implementaci. Pokud se zlepší výkon dopad sestava je k dispozici. Pokud výkon sníží, přetrénujte, doporučení se vrátí zpět.


## <a name="parameterize-queries-recommendations"></a>Parametrizovat dotazy doporučení
*Parametrizovat dotazy* doporučení se zobrazí, když máte jeden nebo více dotazů, které jsou neustále se rekompilovány ale koncové nahoru stejný plán provádění dotazu. Tato podmínka vytvoří příležitost k použití Parametrizace vynucené. Vynucené Parametrizace zase umožňuje plány dotazů do mezipaměti a opakovaně v budoucnosti, což zvyšuje výkon a snižuje využití prostředků. 

Každý dotaz, který je původně vydaný pro SQL Server musí být zkompilována Generovat plán provádění. Každý generovaný plán je přidán do mezipaměti plánu. Další spuštění stejný dotaz můžete znovu použít tento plán z mezipaměti, která eliminuje potřebu další kompilace. 

Dotazy s parametry bez hodnot může vést k nároky na výkon, protože plán spuštění je znovu zkompilovat pokaždé, když-parametrizované hodnoty se liší. V mnoha případech generovat stejný dotazy s různými hodnotami parametrů stejného plánům spuštění. Tyto plány, ale stále samostatně přidají do mezipaměti plánu. 

Proces opětovné kompilaci plánům spuštění používá databázových prostředků, zvýší dobu trvání dotazu a přetečení mezipaměti plánu. Tyto události zase způsobit plány vyřazení z mezipaměti. Toto chování systému SQL Server můžete změnit nastavením možnosti vynucené Parametrizace v databázi. 

Abyste mohli odhadnout vliv tohoto doporučení, jsou součástí srovnání ze skutečného využití procesoru a předpokládané využití procesoru (jako by byly použity doporučení). Toto doporučení vám může pomoct získat úspora využití procesoru. Pomůže vám také můžete zkrátit dobu trvání dotazu a režie pro ukládání do mezipaměti plánu, což znamená, že více plánů můžete zůstat v mezipaměti a znovu použít. Toto doporučení můžete rychle použít tak, že vyberete **použít** příkazu. 

Po použití tohoto doporučení, povolí vynucené Parametrizace během několika minut ve vaší databázi. Spustí monitorování procesu, který trvá přibližně 24 hodin. Po uplynutí této doby se zobrazí sestavu ověření. Tato sestava uvádí využití CPU databáze 24 hodin před a po použití doporučení. SQL Database Advisor má bezpečnostní mechanismus, který se automaticky vrátí použité doporučení, pokud byl zjištěn regrese výkonu.

## <a name="fix-schema-issues-recommendations-preview"></a>Opravte problémy doporučení schématu (preview)

> [!IMPORTANT]
> Microsoft je aktuálně vyřazení "Opravit problém schématu" doporučení. Doporučujeme, abyste použili [Intelligent Insights](sql-database-intelligent-insights.md) monitorovat vaše databáze problémy s výkonem, včetně problémů schéma, které dříve popsané "Opravit problém schématu" doporučení.
> 

**Opravit potíže se schématem** doporučení se zobrazí, když služba SQL Database oznámení anomálie v čísle schématu chyby související s SQL, které se dějí v SQL database. Toto doporučení se zobrazí obvykle, když databáze zaznamená více chyby (neplatný název sloupce, neplatný název objektu a tak dále), která související s schéma do jedné hodiny.

"Problémy s schématu" jsou třídy chyby syntaxe v systému SQL Server. K nim dojde, když nejsou zarovnány definici příkazu jazyka SQL a definice schématu databáze. Například jeden ze sloupců, u kterých se očekává v dotazu může být chybějící v cílové tabulce nebo naopak. 

"Opravit problém schématu" doporučení se zobrazí, když služba Azure SQL Database oznámení anomálie v čísle schématu chyby související s SQL, které se dějí v SQL database. Následující tabulka obsahuje chyby, které se vztahují na schéma problémy:

| Kód chyby SQL | Zpráva |
| --- | --- |
| 201 |Procedura nebo funkce "*"očekává, že parametr"*", který nebyl uveden. |
| 207 |Neplatný název sloupce ' *'. |
| 208 |Neplatný název objektu "*". |
| 213 |Název sloupce nebo počet zadaných hodnot neodpovídá definici tabulky. |
| 2812 |Nelze nalézt uloženou proceduru ' *'. |
| 8144 |Proceduru nebo funkci * má příliš mnoho zadaných argumentů. |

## <a name="next-steps"></a>Další postup
Monitorujte svá doporučení a pokračuje v používání jejich vylepšit výkon. Databázové úlohy jsou dynamická a neustále. SQL Database Advisor bude nadále monitorovat a poskytovat doporučení, které mohou potenciálně tak vylepšit výkon vaší databáze. 

* Další informace o automatickém ladění indexů databáze a plánům spuštění dotazů najdete v tématu [automatické ladění Azure SQL Database](sql-database-automatic-tuning.md).
* Další informace o automaticky monitorování výkonu databáze pomocí automatizovaných diagnostiky a analýzy hlavních příčin problémů s výkonem najdete v tématu [Azure SQL Intelligent Insights](sql-database-intelligent-insights.md).
*  Další informace o tom, jak používat doporučení k výkonu na webu Azure Portal najdete v tématu [doporučení k výkonu na webu Azure Portal](sql-database-advisor-portal.md).
* V tématu [informace o výkonu dotazů](sql-database-query-performance.md) a další informace o zobrazení dopad na výkon vaše hlavní dotazy.


