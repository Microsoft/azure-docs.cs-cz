---
title: Výkon doporučení – Azure SQL Database | Microsoft Docs
description: Databáze SQL Azure poskytuje doporučení pro vaše databáze SQL, které může zlepšit výkon aktuální dotaz.
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: b88b8a313a03ef2ad72aeb053b89505201c23819
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="performance-recommendations-for-sql-database"></a>Výkon doporučení pro databázi SQL.

Azure SQL Database zjišťuje a přizpůsobuje s vaší aplikací. Poskytuje přizpůsobené doporučení, které vám umožní maximalizovat výkon vaší databáze SQL. Databáze SQL se neustále vyhodnocuje a analyzuje historie využití databází SQL. Doporučení, které jsou k dispozici jsou založené na vzory databáze jedinečný úlohy a zlepšení výkonu.

> [!TIP]
> [Automatické ladění](sql-database-automatic-tuning.md) patří mezi doporučené metody pro optimalizaci výkonu. [Inteligentní Statistika](sql-database-intelligent-insights.md) patří mezi doporučené metody pro sledování výkonu. 
>

## <a name="create-index-recommendations"></a>Vytvoření doporučení indexu
Databáze SQL nepřetržitě monitoruje dotazy, které jsou spuštěné a identifikuje indexy, které může zlepšit výkon. Po dostatek jistota, že je index na určité chybí, nový **vytvořit index** doporučení je vytvořena.

 Databáze SQL Azure vytvoří spolehlivosti odhadem výkonnější, které by index uvést prostřednictvím čas. V závislosti na odhadované výkonnější doporučení jsou klasifikovány jako Vysoká, střední nebo Nízká. 

Indexy, které jsou vytvořené pomocí doporučení jsou vždy označení automaticky vytvořené indexy. Uvidíte, jaké indexy jsou automaticky vytvořené prohlížením sys.indexes zobrazení. Automaticky vytvořené indexy neblokovat příkazy ALTER nebo přejmenovat. 

Pokud se pokusíte vyřadit sloupec, který je vytvořen automaticky index přes něj, předá se příkaz. Automaticky vytvořené index vyřazen pomocí příkazu také. Obyčejné indexy blok příkazu ALTER/přejmenování na sloupce, které jsou uloženy.

Po vytvoření doporučení indexu se použije, Azure SQL Database porovná výkon dotazů s základní výkon. Pokud nový index zvýšení výkonu, doporučení je označený jako úspěšně dokončený a dopad sestava je k dispozici. Pokud index nebylo zvýšit výkon, se automaticky vrátila. Databáze SQL tento proces používá k zajištění, že doporučení zlepšit výkon databáze.

Všechny **vytvořit index** doporučení obsahuje zásadu back vypnout, která neumožňuje použití doporučení, pokud využití prostředků databáze nebo fondu je vysoká. Zásady back vypnout trvá v úvahu procesoru, vstupně-výstupní Data, vstupně-výstupní operace protokolu a úložiště k dispozici. 

Využití procesoru, vstupně-výstupní operace dat nebo vstupně-výstupní operace protokolu je vyšší než 80 % v předchozí 30 minut, odloží se doporučuje vytvořit index. Pokud bude menší než 10 % dostupného úložiště, po vytvoření indexu, doporučení přejde do chybového stavu. Pokud po několik dní, automatické ladění stále dochází k závěru, že index by být výhodné, proces se znovu spustí. 

Tento proces se opakuje, dokud není k dispozici dostatečně velké úložiště k dispozici pro vytvoření indexu, nebo dokud není index považovat za výhodné už.

## <a name="drop-index-recommendations"></a>Doporučení k odstranění indexu
Kromě zjišťování chybějící indexy, databáze SQL průběžně analyzuje výkon stávající indexy. Pokud se nepoužívá index, Azure SQL Database doporučuje vyřadíte. Vyřazení indexu se doporučuje ve dvou případech:
* Index je duplicitní s jiným indexem (stejná indexována a zahrnuté sloupce, schéma oddílu a filtry).
* Index nebyl použit po delší dobu (93 dnů).

Doporučení k odstranění indexu také projít ověření po implementaci. Pokud zvyšuje výkon, dopad sestava je k dispozici. Pokud dojde ke zhoršení výkonu, je vrácen doporučení.


## <a name="parameterize-queries-recommendations"></a>Parametrizace doporučení dotazy
*Parametrizace dotazy* doporučení se zobrazí, když máte jeden nebo více dotazů, které jsou neustále probíhá překompilovat, ale end až s stejný plán spuštění dotazu. Tato podmínka vytvoří možnost použít vynucené parametrizace. Vynucené Parametrizace zase umožňuje plány dotazů do mezipaměti a opakovaně v budoucnosti, což vylepšuje výkon a snižuje využití prostředků. 

Každý dotaz, který je vydaný původně pro SQL Server musí být zkompilovány Generovat plán spuštění. Každý generovaného plán je přidán do mezipaměti plánu. Dalších spuštěních stejný dotaz můžete znovu použít tento plán z mezipaměti, která eliminuje potřebu další kompilace. 

Dotazy s hodnotami parametry může vést k nároky na výkon, protože plán spuštění znovu zkompiluje pokaždé, když hodnoty parametry se liší. V mnoha případech generovat stejný dotazy s jinými hodnotami parametru stejné provádění plány. Tyto plány, ale jsou stále samostatně přidány do mezipaměti plánu. 

Proces nutnosti rekompilace provádění plány používá databáze prostředků, zvyšuje dotaz doba trvání a přetečení mezipaměti plánu. Tyto události se pak způsobit plány k vyloučení z mezipaměti. Toto chování systému SQL Server může být změněna nastavením možnost vynucené Parametrizace v databázi. 

Chcete-li odhadnout dopad toto doporučení, jsou k dispozici srovnání skutečné využití procesoru a předpokládané využití procesoru (jako kdyby byly použity doporučení). Toto doporučení můžete získat úspory využití procesoru. Je také vám může pomoci zkrátit dobu trvání dotazu a zatížení mezipaměti plánu, což znamená, že více plánů může zůstat v mezipaměti a znovu použít. Toto doporučení můžete rychle použít tak, že vyberete **použít** příkaz. 

Po použití tohoto doporučení se umožňuje vynucené Parametrizace minut ve vaší databázi. Spustí monitorování procesu, který trvá přibližně 24 hodin. Po uplynutí této doby se zobrazí sestavu ověření. Tato sestava zobrazuje využití procesoru vaší databáze, 24 hodin před a po použití doporučení. Poradce pro funkci SQL Database má bezpečnostní mechanismus, který automaticky vrátí použité doporučení, pokud byl zjištěn snížení výkonu.

## <a name="fix-schema-issues-recommendations-preview"></a>Opravte problémy doporučení schématu (preview)

> [!IMPORTANT]
> Microsoft je aktuálně místo začne "Opravit problém schématu" doporučení. Doporučujeme vám, že používáte [inteligentního Statistika](sql-database-intelligent-insights.md) k monitorování vaší problémů s výkonem databáze, včetně problémů schéma, které dříve zahrnutých doporučení "Opravit problém schématu".
> 

**Opravte problémy schématu** doporučení se zobrazí, pokud služba SQL Database oznámení anomálií v počtu schématu související chyby SQL, které jsou aktivit ve vaší databázi SQL. Toto doporučení se obvykle zobrazují, když databáze dojde více schématu související chyby (neplatný název sloupce, neplatný název objektu a tak dále) v rámci hodiny.

"Schématu problémy" jsou třídou chyby syntaxe v systému SQL Server. K nim dojde, pokud nejsou zarovnány definici příkazu jazyka SQL a definice schématu databáze. Například jeden ze sloupců, které se očekává v dotazu může být chybějící v cílové tabulce nebo naopak. 

"Opravit problém schématu" doporučení se zobrazí, když služba Azure SQL Database oznámení anomálií v počtu schématu související chyby SQL, které jsou aktivit ve vaší databázi SQL. Následující tabulka obsahuje chyby, které souvisí s problémy schématu:

| Kód chyby SQL | Zpráva |
| --- | --- |
| 201 |Procedura nebo funkce,*'očekává parametr'*', která nebyla zadána. |
| 207 |Neplatný název sloupce ' *'. |
| 208 |Neplatný název objektu ' *'. |
| 213 |Název sloupce nebo počet zadaných hodnot neodpovídá definici tabulky. |
| 2812 |Nelze nalézt uloženou proceduru ' *'. |
| 8144 |Procedura nebo funkce * má příliš mnoho zadaných argumentů. |

## <a name="next-steps"></a>Další postup
Sledovat vaše doporučení a pokračuje v používání jejich Upřesnit výkonu. Databázové úlohy jsou dynamické a průběžně změnu. Poradce pro funkci SQL Database i nadále monitorovat a poskytovat doporučení, které může zlepšit výkon vaší databáze. 

* Další informace o automatické ladění indexy databáze a plány provádění dotazů najdete v tématu [Azure SQL Database automatické ladění](sql-database-automatic-tuning.md).
* Další informace o automaticky monitorování výkonu databáze s automatizované Diagnostika a analýza hlavní příčiny problémů s výkonem, najdete v části [inteligentního Statistika SQL Azure](sql-database-intelligent-insights.md).
*  Další informace o tom, jak používat doporučení výkonu na portálu Azure najdete v tématu [doporučení výkonu na portálu Azure](sql-database-advisor-portal.md).
* V tématu [Query Performance Insight](sql-database-query-performance.md) a další informace o zobrazení dopad na výkon nejčastějších dotazů.


