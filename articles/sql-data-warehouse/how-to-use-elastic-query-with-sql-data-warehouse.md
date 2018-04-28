---
title: Elastické dotaz – přístup k datům v Azure SQL Data Warehouse z databáze Azure SQL Database | Microsoft Docs
description: Přečtěte si doporučené postupy pro používání pomocí elastické dotazu pro přístup k datům v Azure SQL Data Warehouse z databáze SQL Azure.
services: sql-data-warehouse
author: hirokib
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/11/2018
ms.author: elbutter
ms.reviewer: igorstan
ms.openlocfilehash: ceda0399ae98e2a36fd41b954a741e0379c77fe7
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="best-practices-for-using-elastic-query-in-azure-sql-database-to-access-data-in-azure-sql-data-warehouse"></a>Doporučené postupy pro používání elastické dotazu v databázi SQL Azure pro přístup k datům v Azure SQL Data Warehouse
Přečtěte si doporučené postupy pro používání elastické dotazu pro přístup k datům v Azure SQL Data Warehouse z databáze SQL Azure. 

## <a name="what-is-an-elastic-query"></a>Co je elastické dotazu?
Dotaz elastické umožňuje napsat dotaz v databázi Azure SQL, který je vzdáleně odesílaných do služby Azure SQL data warehouse pomocí T-SQL a externí tabulky. Pomocí této funkce poskytuje úsporu nákladů a další původce architektury, v závislosti na scénáři.

Tato funkce umožňuje dva základní scénáře:

1. Izolace domény
2. Spuštění vzdáleného dotazu

### <a name="domain-isolation"></a>Izolace domény

Izolace domény odkazuje na scénář classic datového tržiště. V některých scénářích jeden chtít poskytují logické domény dat pro příjem dat uživatele, kteří jsou izolované od zbytku datového skladu, pro z mnoha důvodů včetně, ale mimo jiné:

1. Izolaci prostředků – databáze SQL je optimalizovaná tak, aby poskytovat velké základní souběžných uživatelů obsluhující mírně odlišné úloh než velké analytické dotazy, které datového skladu je vyhrazený pro. Izolace zajistí, že správné zatížení zpracovává příslušné nástroje.
2. Izolace zabezpečení – jednotlivé autorizovaným data podmnožina selektivně prostřednictvím určité schémat.
3. Sandboxing - poskytují sadu ukázkových dat jako "playground" a prozkoumejte produkční dotazy atd.

Elastické dotaz může poskytovat umožňuje snadno vyberte podmnožiny dat serveru SQL datového skladu a přesuňte jej do instance databáze SQL. Kromě toho tato izolace nebrání možnost taky povolit spouštění vzdálený dotaz povolení pro scénáře zajímavějšího "mezipaměť".

### <a name="remote-query-execution"></a>Spuštění vzdáleného dotazu

Elastické dotazu umožňuje spuštění vzdáleného dotazu na instanci SQL data warehouse. Tím, že oddělíte horká a studená data mezi těmito dvěma databázemi jeden můžete využít nejlepší z databáze SQL a SQL data warehouse. Uživatele můžete ponechat novější data v databázi SQL, která může obsluhovat sestavy a velkého počtu průměrná podnikoví uživatelé. Ale je potřeba víc dat nebo výpočetní, uživatel může přenést část dotazu do instance datového skladu SQL, kde lze zpracovat rozsáhlé agregace mnohem rychlejší a efektivnější.

## <a name="elastic-query-process"></a>Proces elastické dotazů
Elastické dotazu je použít k datům umístěným v rámci SQL datového skladu k dispozici instance databáze SQL. Elastické dotazu umožňuje dotazy z databáze SQL odkazují na tabulky vzdálenou instanci SQL data warehouse. 

Prvním krokem je vytvoření definice zdroj externích dat, který odkazuje na instance SQL datového skladu, který používá existující přihlašovací údaje uživatele v SQL data warehouse. Žádné změny jsou nezbytné ve vzdálené instanci SQL data warehouse. 

> [!IMPORTANT] 
> 
> Musíte mít oprávnění ALTER ANY EXTERNAL DATA SOURCE. Toto oprávnění je součástí oprávnění ALTER DATABASE. Jsou potřeba oprávnění ALTER ANY externí zdroj dat k odkazování na vzdálené zdroje dat.

Dále vytvořte definici vzdálené externí tabulky instance databáze SQL, který odkazuje na vzdálenou tabulku v SQL data warehouse. Když dotaz používá externí tabulku, část dotaz odkazující na externí tabulky je odeslat instance SQL datového skladu na zpracování. Po dokončení dotazu výsledné sady budou odeslána zpět do volání instance databáze SQL. Stručný kurzu nastavení dotaz elastické mezi SQL database a SQL data warehouse, najdete v článku [konfigurace elastické dotaz s SQL Data Warehouse][Configure Elastic Query with SQL Data Warehouse].

Další informace o elastické dotazu s databází SQL, najdete v článku [Azure SQL Database elastické dotazu přehled][Azure SQL Database elastic query overview].

## <a name="best-practices"></a>Osvědčené postupy
Použijte tyto doporučené postupy pro používání elastické dotazu efektivně.

### <a name="general"></a>Obecné

- Při použití spuštění vzdáleného dotazu, ujistěte se, můžete pouze výběr nezbytné sloupce a použití správné filtrů. Nejen nemá toto zvýšení nezbytné výpočetní, ale taky zvyšuje velikost sady výsledků dotazu, a proto množství dat, který potřebujete přesunout mezi dvěma instancemi.
- Zachování dat pro analytické účely v SQL Data Warehouse a SQL Database v clusteru columnstore analytiIcal výkonu.
- Ujistěte se, že jsou zdrojové tabulky do několika oddílů pro přesun dotazu a data.
- Ujistěte se, použít jako mezipaměť instance databáze SQL jsou rozděleny do oddílů podrobnější aktualizace a snadnější správu povolit. 
- V ideálním případě použijte PremiumRS databáze, protože poskytují analytical výhod Clusterové columnstore indexování se zaměřením na úlohy náročné na vstupně-výstupní operace se slevou z databází Premium.
- Po zatížením využívat zatížení nebo datum účinnosti Identifikace sloupce pro upserts v instance databáze SQL k udržení integrity zdroj mezipaměti. 
- V instanci SQL data warehouse pro své SQL databáze vzdálené přihlašovací údaje definované ve zdroji dat. externí, vytvořte samostatné přihlášení a uživatele. 

### <a name="elastic-querying"></a>Elastické dotazování

- V mnoha případech jeden chtít spravovat typu roztažené tabulky, kde je část tabulku v databázi SQL jako data výkonu se zbytkem data uložená v SQL Data Warehouse uložená v mezipaměti. Budete potřebovat dva objekty v databázi SQL: externí tabulku v databázi SQL, který odkazuje na základní tabulky v SQL Data Warehouse a "v mezipaměti" část tabulky v databázi SQL. Zvažte vytvoření zobrazení v horní části uložené v mezipaměti v tabulce a externí tabulky, které sjednocení tabulky i použity filtry, které oddělte materializována v rámci SQL Database a SQL Data Warehouse dat, které jsou k dispozici prostřednictvím externí tabulky data.

  Představte si, že chcete zachovat poslední rok data v instanci databáze SQL. **Ext. Objednávky** odkazy na tabulku datového skladu řadí tabulky. **Dbo. Objednávky** představuje poslední za roky dat v rámci instance databáze SQL. Místo požádat uživatele, můžete rozhodnout, jestli dotaz na jednu tabulku nebo dalších, vytvořte zobrazení v horní části obě tabulky v bodě oddílu posledního roku.

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

  Zobrazení vytváří tak kompilátoru dotazu umožňuje určit, zda je nutné použít instance datového skladu k odpovědi na dotaz uživatele. 

  Je také odesílá, kompilace, spuštění a přesouvání dat spojené s každou elastické dotaz instance datového skladu. Být cognizant, že každý elastické dotaz započítává vaší souběžnosti sloty a používá prostředky.  


- Pokud jeden plánuje k podrobnostem další do sadu výsledků dotazu z instance datového skladu, zvažte vyhodnocování v dočasné tabulky v databázi SQL, výkonu a využití prostředků zbytečné.

### <a name="moving-data"></a>Přesun dat 

- Pokud je to možné zachovat data správy snadněji vyřeší pomocí připojovacího zdrojové tabulky tak, aby byly aktualizace snadno udržovatelný mezi instancemi datového skladu a databáze.
- Přesunutí dat na úrovni oddílu s vyprázdnit a výplně sémantiku minimalizovat náklady na dotaz na data skladu úroveň a množství dat přesunout k zachování aktualizovaného stavu instanci databáze. 

### <a name="when-to-choose-azure-analysis-services-vs-sql-database"></a>Kdy použít Azure Analysis Services vs databáze SQL

Použití Azure Analysis Services, když:

- Máte v úmyslu používat vaše mezipaměť s BI nástroj, který odešle velké množství malých dotazy
- Třeba subsecond latence dotazu
- Máte zkušenosti ve správě nebo vývoj modely pro službu Analysis Services 

Použití Azure SQL databáze, když:

- Chcete dotazování na data mezipaměti s SQL
- Potřebujete vzdálené spuštění některých dotazů
- Máte větší požadavky mezipaměti

## <a name="faq"></a>Nejčastější dotazy

Otázka: je možné použít databází v Elastickém fondu se elastické dotazu?

Odpověď: Ano. Databáze SQL v Elastickém fondu pomocí elastické dotazu. 

Otázka: je limitu pro počet databází, které lze použít pro elastické dotaz?

Odpověď: neexistuje žádné pevné zakončení na tom, kolik databází lze použít pro elastické dotazu. Každý elastické dotaz (dotazy, které dosáhl SQL Data Warehouse) však bude počítat směrem k omezení normální souběžnosti.

Otázka: existují omezení jednotek dtu úrovně spojené s elastické dotazu?

Odpověď: DTU omezení nejsou uložené žádné jinak s elastické dotazu. Standardní zásady je tak, aby logické servery mají omezení jednotek DTU zavedené zákazníkům zabránit náhodnému nákladů nad plán. Pokud chcete povolit několik databází pro elastický dotaz spolu s instanci SQL Data Warehouse, můžete narazit krytky neočekávaně. Pokud k tomu dojde, odešlete žádost o zvýšení limitu DTU na logickém serveru. Můžete zvýšit kvótu podle [vytvoření lístku podpory](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) a výběrem *kvóty* jako typ požadavku

Otázka: je možné použít řádek úrovně zabezpečení nebo dynamické Data maskování s elastické dotazu?

Odpověď: Zákazníci, kteří jej nepřejete použít pokročilých funkcí zabezpečení SQL Database lze provést první přesunutím a ukládání dat v databázi SQL. Nemůžete použít aktuálně zabezpečení na úrovni řádků nebo DDM na dat získaných prostřednictvím externí tabulky. 

Otázka: je možné napsaný z instance Moje SQL databáze na instance datového skladu?

A: Tato funkce aktuálně není podporována. Navštivte naše [zpětné vazby stránky] [ Feedback page] k vytvoření nebo hlasování pro tuto funkci Pokud toto je funkce vám rádi viděli v budoucnu. 

Otázka: je možné používat prostorové typy jako geometrie nebo Geografie?

Odpověď: můžete uložit prostorové typy v SQL Data Warehouse jako hodnoty varbinary(max). Při dotazu tyto sloupce pomocí elastické dotazu, můžete je převést na odpovídající typy za běhu.

![prostorové typy](./media/sql-data-warehouse-elastic-query-with-sql-database/geometry-types.png)

<!--Article references-->

[SQL Data Warehouse development overview]: sql-data-warehouse-overview-develop.md
[Configure Elastic Query with SQL Data Warehouse]: tutorial-elastic-query-with-sql-datababase-and-sql-data-warehouse.md
[Feedback Page]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Azure SQL Database elastic query overview]: ../sql-database/sql-database-elastic-query-overview.md


