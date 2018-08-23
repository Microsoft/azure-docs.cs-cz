---
title: Elastický dotaz – přístup k datům ve službě Azure SQL Data Warehouse z Azure SQL Database | Dokumentace Microsoftu
description: Přečtěte si doporučené postupy pro používání elastického dotazu pro přístup k datům ve službě Azure SQL Data Warehouse z Azure SQL Database.
services: sql-data-warehouse
author: hirokib
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/11/2018
ms.author: elbutter
ms.reviewer: igorstan
ms.openlocfilehash: 344cb1bed56b0b6af7bd3704f8674ae30695f885
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2018
ms.locfileid: "42054018"
---
# <a name="best-practices-for-using-elastic-query-in-azure-sql-database-to-access-data-in-azure-sql-data-warehouse"></a>Doporučené postupy pro používání elastického dotazu ve službě Azure SQL Database pro přístup k datům ve službě Azure SQL Data Warehouse
Přečtěte si doporučené postupy pro používání elastického dotazu pro přístup k datům ve službě Azure SQL Data Warehouse z Azure SQL Database. 

## <a name="what-is-an-elastic-query"></a>Co je elastický dotaz?
Elastický dotaz můžete napsat dotaz ve službě Azure SQL database, které je odesláno vzdálené služby Azure SQL data warehouse pomocí T-SQL a externí tabulky. Tato funkce poskytuje úspory nákladů a další architektury výkonné, v závislosti na scénáři.

Tato funkce umožňuje dva primární scénáře:

1. Izolace domény
2. Spuštění vzdáleného dotazu

### <a name="domain-isolation"></a>Izolace domény

Izolace domény odkazuje na scénář classic datového tržiště. V některých scénářích jedna může být vhodné k poskytování logické domény dat uživatele, které jsou izolované od zbývající části datového skladu pro z různých důvodů, včetně, ale mimo jiné:

1. Izolace prostředků – SQL database je optimalizovaná pro obsluhu velký základ souběžných uživatelů slouží trochu jiné úlohy než velké analytických dotazů, které datový sklad je vyhrazený pro. Izolace zajistí, že ty správné sady funkcí obsluhuje ty správné nástroje.
2. Izolace zabezpečení - k oddělení podsadě autorizované data selektivně prostřednictvím určitá schémata.
3. Sandboxing - zadejte sadu ukázkových dat jako "playground" zkoumat produkční dotazy atd.

Elastický dotaz může poskytnout možnost jednoduše vyberte podmnožiny dat datového skladu SQL a přesuňte ho do instance databáze SQL. Kromě toho tato izolace nebrání možnost také povolit spouštění vzdálených dotazů umožňující zajímavější scénáře "mezipaměti".

### <a name="remote-query-execution"></a>Spuštění vzdáleného dotazu

Elastický dotaz umožňuje provádění vzdáleného dotazu na instanci SQL data warehouse. Oddělením horká a studená data mezi dvěma databázemi jeden můžete využít to nejlepší z SQL database a SQL data warehouse. Uživatelům můžete ponechat novější data v SQL database, která může sloužit sestavy a velký počet průměrné podnikoví uživatelé. Ale je potřeba víc dat nebo výpočetní, uživatel může převzít část dotazu pro instanci datového skladu SQL, kde agregace ve velkém měřítku může být zpracována mnohem rychleji a efektivněji.

## <a name="elastic-query-process"></a>Proces elastického dotazu
Elastický dotaz lze použít pro zpřístupnění dat umístěných v rámci SQL data warehouse pro instance databází SQL. Elastický dotaz umožňuje dotazů z databáze serveru SQL, přečtěte si k tabulkám ve vzdálené instanci SQL data warehouse. 

Prvním krokem je vytvoření definici externích zdrojů dat, který odkazuje na instanci SQL datového skladu, který používá stávajících přihlašovacích údajů uživatele v datovém skladu SQL. Žádné změny nejsou nezbytné ve vzdálené instanci SQL data warehouse. 

> [!IMPORTANT] 
> 
> Musí mít oprávnění ALTER ANY EXTERNAL DATA SOURCE. Toto oprávnění je součástí oprávnění ALTER DATABASE. K odkazování na vzdálené zdroje dat. jsou potřeba oprávnění ALTER ANY EXTERNAL DATA SOURCE.

Dále vytvořte definici externí tabulky vzdálené instanci SQL database, která odkazuje na vzdálenou tabulku ve službě SQL data warehouse. Pokud dotaz používá externí tabulky, část dotazu odkazuje na externí tabulky přijde na instanci SQL data warehouse ke zpracování. Po dokončení dotazu sadu výsledků dotazu budou odeslána zpět do volání instance databáze SQL. Stručný kurz nastavení elastický dotaz mezi SQL database a SQL data warehouse, najdete v tématu [konfigurace elastické dotazy s využitím SQL Data Warehouse][Configure Elastic Query with SQL Data Warehouse].

Další informace o elastický dotaz SQL Database, najdete v článku [přehled Azure SQL Database elastic query][Azure SQL Database elastic query overview].

## <a name="best-practices"></a>Osvědčené postupy
Pomocí těchto osvědčených postupů efektivně používat elastický dotaz.

### <a name="general"></a>Obecné

- Při použití provádění vzdáleného dotazu, ověřte jste pouze výběr nezbytné sloupců a použití správné filtrů. Nejen nepodporuje toto zvýšení nezbytné výpočetní prostředky, ale také zvyšuje velikost sady výsledků dotazu, a proto množství dat, které je potřeba přesunout mezi dvěma instancemi.
- Spravovat data v Clusterované columnstore analytiIcal výkon pro analytické účely v SQL Database i SQL Data Warehouse.
- Ujistěte se, že zdrojové tabulky dělí pro přesun dotazy a data.
- Zkontrolujte použití jako mezipaměť instance databáze SQL jsou podrobněji, pokud chcete povolit podrobnější aktualizace a snadnější správu. 
- V ideálním případě použijte premiumrs: databáze, protože poskytují analýzu výhod Clusterované columnstore indexování se zaměřením na úlohy náročné na vstupně-výstupní operace z databází Premium se slevou.
- Po načtení využijte zatížení nebo identifikace sloupce Datum účinnosti pro upsertuje v instancích SQL Database k udržení integrity zdroj sdílené mezipaměti prostředí. 
- Vytvořte samostatný účet a uživatele ve vaší instanci SQL data warehouse pro své SQL database vzdálené přihlašovací údaje definované v externí zdroj dat. 

### <a name="elastic-querying"></a>Elastické dotazy

- V mnoha případech jeden chtít spravovat typu roztaženou tabulku, kde je část tabulky v SQL Database jako data uložená v mezipaměti pro výkon se zbytkem data uložená ve službě SQL Data Warehouse. Budete potřebovat dva objekty ve službě SQL Database: externí tabulky v SQL Database, která odkazuje na základní tabulky v SQL Data Warehouse a "v mezipaměti" část tabulky v databázi SQL. Zvažte vytvoření zobrazení v horní části v mezipaměti v tabulce a externí tabulky, které sjednocení tabulky i použity filtry, které oddělení dat vyhodnocena v SQL Database a SQL Data Warehouse data vystavená prostřednictvím externí tabulky.

  Představte si, že chcete zachovat nejnovější ročního objemu dat v instanci databáze SQL. **Externí Objednávky** odkazy tabulky datového skladu orders tabulky. **Dbo. Objednávky** představuje poslední roky za data v instanci databáze SQL. Místo žádá uživatelům v rozhodnutí, jestli se má dotaz jednu tabulku nebo druhé, vytvořte zobrazení v horní části obou tabulek v bodě oddílu posledního roku.

  ```sql
  CREATE VIEW dbo.Orders_Elastic AS
  SELECT 
    [col_a]     
  , [col_b]         
  , ...
  , [col_n]
  FROM
    [dbo].[Orders]
  WHERE 
    YEAR([o_orderdate]) >= '<Most Recent Year>'
  UNION
  SELECT 
    [col_a]     
  , [col_b]         
  , ...
  , [col_n]         
  FROM
    [ext].[Orders]
  WHERE
    YEAR([o_orderdate]) < '<Most Recent Year>'
  ```

  Zobrazení vytvořené tak umožňuje kompilátoru dotazu určit, jestli je potřeba použít instanci datového skladu k zodpovězení dotazu uživatelů. 

  Je režie odesílání, kompilace, spouštění a přesun dat spojené s každou elastický dotaz proti instanci datového skladu. Být cognizant, že každý elastický dotaz vaše slotů souběžnosti se počítá a používá prostředky.  


- Jeden plánů a přejít k podrobnostem další do sady výsledků z instance datového skladu, vezměte v úvahu materializaci v dočasné tabulky v databázi SQL pro výkon a aby se zabránilo zbytečným prostředků využití.

### <a name="moving-data"></a>Přesun dat 

- Pokud je to možné udržujte správy dat jednodušší díky jen pro připojení zdrojové tabulky tak, aby se aktualizace snadno udržovatelný mezi instance datového skladu a databáze.
- Přesunutí dat na úrovni oddílu s vyprázdnění a úroveň a množství dat přesunout zachovat aktuální instance databáze skladu výplně sémantiku minimalizovat náklady na dotaz na data. 

### <a name="when-to-choose-azure-analysis-services-vs-sql-database"></a>Kdy zvolit Azure Analysis Services a SQL Database

Použití Azure Analysis Services, když:

- Máte v úmyslu používat mezipaměť s nástroje BI, který odešle velké množství malých dotazů
- Potřebujete subsecond latence dotazu
- Máte ve správě/vývoj modelů pro Analysis Services 

Použití Azure SQL Database při:

- Chcete-li k dotazování dat pomocí jazyka SQL
- Potřebujete pro určité dotazy vzdálené spuštění
- Máte větší požadavky mezipaměti

## <a name="faq"></a>Nejčastější dotazy

Otázka: Mohu použít databází v Elastickém fondu přitom s elastický dotaz?

Odpověď: Ano. Databáze SQL v Elastickém fondu přitom můžete použít nástroj Elastic Query. 

Otázka: Existuje limit pro kolik databází můžu můžete použít pro elastický dotaz?

Odpověď: neexistuje žádná pevný limit na tom, kolik databází je možné pro elastický dotaz. Nicméně každý elastický dotaz (dotazů, které přístupů do SQL Data Warehouse) připočítají se limity normální souběžnosti.

Dotaz: existují omezení jednotek DTU spojené s elastický dotaz?

A: omezení jednotek DTU nejsou uložené žádné jinak než pomocí elastického dotazu. Standardní zásady se tak, aby logické servery mají omezení jednotek DTU na místě uživatelům zabránit náhodnému nadměrných výdajů. Pokud chcete povolit několik databází pro elastický dotaz společně s instanci SQL Data Warehouse, můžete narazit na zakončení neočekávaně. Pokud k tomu dojde, odešlete žádost o zvýšení limitu jednotek DTU na logickém serveru. Můžete zvýšit kvótu podle [vytvoření lístku podpory](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) a vyberete *kvóty* jako typ požadavku

Otázka: Mohu použít řádek úrovně zabezpečení/dynamických dat maskování s elastický dotaz?

Odpověď: Zákazníci, kteří chtěli používat rozšířené funkce zabezpečení SQL Database to tak, že první přesun a ukládání dat ve službě SQL Database. Nelze použít aktuálně zabezpečení na úrovni řádků nebo DDM na datech zasílat dotazy prostřednictvím externí tabulky. 

Dotaz: lze píše ze své instanci SQL database pro instanci datového skladu?

Odpověď: aktuálně tato funkce není podporována. Navštivte naše [zpětnou vazbu stránky] [ Feedback page] k vytvoření/Hlasujte pro tuto funkci Pokud to je funkce, které byste rádi viděli v budoucích. 

Otázka: Mohu použít prostorové typy například geometrie nebo Geografie?

Odpověď: můžete ukládat prostorové typy ve službě SQL Data Warehouse jako hodnoty varbinary(max). Při dotazování těchto sloupců použitím elastického dotazu, můžete je převést na odpovídající typy za běhu.

![prostorové typy](./media/sql-data-warehouse-elastic-query-with-sql-database/geometry-types.png)

<!--Article references-->

[SQL Data Warehouse development overview]: sql-data-warehouse-overview-develop.md
[Configure Elastic Query with SQL Data Warehouse]: tutorial-elastic-query-with-sql-datababase-and-sql-data-warehouse.md
[Feedback Page]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Azure SQL Database elastic query overview]: ../sql-database/sql-database-elastic-query-overview.md


