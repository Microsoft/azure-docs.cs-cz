---
title: Správa historických dat v dočasných tabulkách
description: Naučte se používat dočasné zásady uchovávání informací, abyste zachovali historické údaje pod vaším ovládacím prvkem.
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: bonova
ms.author: bonova
ms.reviewer: sstein
ms.date: 09/25/2018
ms.openlocfilehash: 1d68163a9fba3ba3bcd4c0c0f3fb5f442296e781
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91619385"
---
# <a name="manage-historical-data-in-temporal-tables-with-retention-policy"></a>Správa historických dat v dočasnách tabulkách pomocí zásad uchovávání informací
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Dočasné tabulky mohou zvýšit velikost databáze větší než běžné tabulky, zejména pokud uchováváte historická data po delší dobu. Proto jsou zásady uchovávání historických dat důležitým aspektem plánování a správy životního cyklu každé dočasné tabulky. Dočasné tabulky v Azure SQL Database a Azure SQL Managed instance přidávají se snadno použitelným mechanismem uchovávání informací, který vám pomůže tuto úlohu provést.

Dočasné uchovávání historie lze nakonfigurovat na úrovni jednotlivých tabulek, což umožňuje uživatelům vytvářet flexibilní zásady stárnutí. Použití dočasného uchovávání je jednoduché: vyžaduje, aby byl během vytváření tabulky nebo změny schématu nastavený jenom jeden parametr.

Po definování zásad uchovávání informací Azure SQL Database a Azure SQL Managed instance se pravidelně spouští kontrola, pokud jsou k dispozici historické řádky, které mají nárok na automatické vyčištění dat. Identifikace odpovídajících řádků a jejich odebrání z tabulky historie probíhá transparentně v úloze na pozadí, která je naplánována a spuštěna systémem. Podmínka stáří pro řádky tabulky historie se kontroluje na základě sloupce, který představuje konec SYSTEM_TIME období. Pokud je doba uchování například nastavená na šest měsíců, řádky tabulky s nárokem na vyčištění splňují následující podmínky:

```sql
ValidTo < DATEADD (MONTH, -6, SYSUTCDATETIME())
```

V předchozím příkladu předpokládáme, že sloupec **ValidTo** odpovídá konci období SYSTEM_TIME.

## <a name="how-to-configure-retention-policy"></a>Jak nakonfigurovat zásady uchovávání informací

Než nakonfigurujete zásady uchovávání informací pro dočasnou tabulku, nejprve si projděte, jestli je *na úrovni databáze* povolené dočasné historické uchovávání.

```sql
SELECT is_temporal_history_retention_enabled, name
FROM sys.databases
```

Příznak databáze **is_temporal_history_retention_enabled** ve výchozím nastavení zapnutý, ale uživatelé ho můžou změnit pomocí příkazu ALTER DATABASE. Tato funkce se také automaticky nastaví na vypnuto po operaci [Obnovení bodu v čase](recovery-using-backups.md) . Pokud chcete pro vaši databázi povolit vyčištění dočasného uchovávání historie, spusťte následující příkaz:

```sql
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
```

> [!IMPORTANT]
> Uchovávání dat pro dočasné tabulky můžete nakonfigurovat i v případě, že je **IS_TEMPORAL_HISTORY_RETENTION_ENABLED** vypnuté, ale v takovém případě se automatické čištění pro staré řádky neaktivuje.

Zásady uchovávání informací se konfigurují během vytváření tabulky zadáním hodnoty pro parametr HISTORY_RETENTION_PERIOD:

```sql
CREATE TABLE dbo.WebsiteUserInfo
(  
    [UserID] int NOT NULL PRIMARY KEY CLUSTERED
  , [UserName] nvarchar(100) NOT NULL
  , [PagesVisited] int NOT NULL
  , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
  , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
  , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
 )  
 WITH
 (
     SYSTEM_VERSIONING = ON
     (
        HISTORY_TABLE = dbo.WebsiteUserInfoHistory,
        HISTORY_RETENTION_PERIOD = 6 MONTHS
     )
 );
```

Azure SQL Database a Azure SQL Managed instance umožňují určit dobu uchování pomocí různých časových jednotek: dny, týdny, měsíce a roky. Pokud je hodnota HISTORY_RETENTION_PERIOD vynechána, předpokládá se nekonečné uchovávání. Nekonečná klíčová slova můžete také použít explicitně.

V některých scénářích můžete chtít po vytvoření tabulky nakonfigurovat uchování nebo změnit dříve nakonfigurovanou hodnotu. V takovém případě použijte příkaz ALTER TABLE:

```sql
ALTER TABLE dbo.WebsiteUserInfo
SET (SYSTEM_VERSIONING = ON (HISTORY_RETENTION_PERIOD = 9 MONTHS));
```

> [!IMPORTANT]
> Nastavení SYSTEM_VERSIONING vypnuto *nezachovává* hodnotu doby uchování. Nastavení SYSTEM_VERSIONING na ZAPNUTo, aniž by HISTORY_RETENTION_PERIOD explicitně určila výsledky v nekonečné době uchování.

Chcete-li zkontrolovat aktuální stav zásady uchovávání informací, použijte následující dotaz, který spojuje příznak povolení dočasného uchovávání na úrovni databáze s dobami uchování pro jednotlivé tabulky:

```sql
SELECT DB.is_temporal_history_retention_enabled,
SCHEMA_NAME(T1.schema_id) AS TemporalTableSchema,
T1.name as TemporalTableName,  SCHEMA_NAME(T2.schema_id) AS HistoryTableSchema,
T2.name as HistoryTableName,T1.history_retention_period,
T1.history_retention_period_unit_desc
FROM sys.tables T1  
OUTER APPLY (select is_temporal_history_retention_enabled from sys.databases
where name = DB_NAME()) AS DB
LEFT JOIN sys.tables T2
ON T1.history_table_id = T2.object_id WHERE T1.temporal_type = 2
```

## <a name="how-ages-rows-are-deleted"></a>Jak se odstraňují řádky s výpadky

Proces čištění závisí na rozložení indexu v tabulce historie. Je důležité si všimnout, že pro *omezené zásady uchovávání informací můžou být nakonfigurované jenom tabulky historie s clusterovaným indexem (B-stromu nebo columnstore)*. Vytvoří se úkol na pozadí, který provede zastaralé vyčištění dat pro všechny dočasné tabulky s omezenou dobou uchovávání.
Logika vyčištění pro clusterovaný index rowstore (B-Tree) odstraní starý řádek v menších blocích (až 10 000) minimalizuje tlak na protokol databáze a podsystém v/v. I když logika vyčištění používá požadovaný index B-Tree, pořadí odstranění pro řádky, které jsou starší než doba uchování, nelze pevně zaručit. Proto neprovádějte *žádnou závislost na pořadí čištění v aplikacích*.

Úloha čištění pro clusterovaný columnstore odebere celou [skupinu řádků](/sql/relational-databases/indexes/columnstore-indexes-overview) najednou (obvykle obsahuje 1 000 000 řádků každého), což je velmi efektivní, zejména při vygenerování historických dat při vysokém tempu.

![Uchovávání clusterovaného v columnstore](./media/temporal-tables-retention-policy/cciretention.png)

Vynikající komprese dat a efektivní vyčištění v rámci uchovávání informací vytváří clusterový index columnstore ideální volbou pro scénáře, kdy vaše úlohy rychle generují vysoké množství historických dat. Tento vzor je typický pro náročné [úlohy zpracování transakčních procesů, které využívají dočasné tabulky](/sql/relational-databases/tables/temporal-table-usage-scenarios) ke sledování změn a auditování, analýze trendů nebo příjmu dat IoT.

## <a name="index-considerations"></a>Otázky indexu

Úloha čištění pro tabulky s clusterovaným indexem rowstore vyžaduje, aby index začínal sloupcem odpovídajícím konci SYSTEM_TIME období. Pokud tento index neexistuje, nemůžete nakonfigurovat omezenou dobu uchování:

*Pro dočasnou <br> </br> tabulku temporalstagetestdb. dbo. WebsiteUserInfo se systémovou správou verzí, protože tabulka historie temporalstagetestdb. dbo. WebsiteUserInfoHistory neobsahuje požadovaný clusterovaný index, se nezdařilo nastavení zprávy č. 13765, úroveň 16, stav 1. Zvažte vytvoření clusterovaného indexu columnstore nebo B-Tree začínajícího sloupcem, který odpovídá konci SYSTEM_TIME období, v tabulce historie.*

Je důležité si všimnout, že výchozí tabulka historie vytvořená Azure SQL Database a spravovanou instancí Azure SQL již obsahuje clusterovaný index, který je v souladu se zásadami uchovávání informací. Pokud se pokusíte tento index odstranit v tabulce s omezenou dobou uchovávání, operace se nezdařila s následující chybou:

*Msg 13766, Level 16, state 1 <br> </br> nemůže vyřadit clusterovaný index ' WebsiteUserInfoHistory.IX_WebsiteUserInfoHistory ', protože je používán pro automatické vyčištění starých dat. Pokud potřebujete tento index odstranit, zvažte nastavení HISTORY_RETENTION_PERIOD na nekonečné v odpovídající dočasné tabulce se systémovou správou verzí.*

Čištění v clusterovaných indexech columnstore funguje optimálně, pokud jsou historické řádky vloženy ve vzestupném pořadí (seřazené podle sloupce konec období), což je vždy případ, kdy je tabulka historie vyplněna výhradně pomocí mechanismu SYSTEM_VERSIONIOING. Pokud se řádky v tabulce historie neúčtují podle sloupce konec období (což může být případ, že jste migrovali existující historická data), měli byste znovu vytvořit clusterovaný index columnstore na rowstore indexu B-Tree, který je správně uspořádaný, abyste dosáhli optimálního výkonu.

Vyhněte se opětovnému sestavování clusterovaného indexu columnstore v tabulce historie s omezenou dobou uchovávání, protože může změnit řazení skupin řádků přirozeně uložených operací se systémovou správou verzí. Pokud potřebujete znovu sestavit clusterovaný index columnstore v tabulce historie, udělejte to tak, že ho znovu vytvoříte na základě kompatibilního indexu B-stromu, čímž zachováte řazení v rowgroups nezbytném pro normální vyčištění dat. Stejný postup byste měli vzít v případě, že vytváříte dočasnou tabulku se stávající tabulkou historie, která má clusterovaný index sloupce bez zaručené pořadí dat:

```sql
/*Create B-tree ordered by the end of period column*/
CREATE CLUSTERED INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory (ValidTo)
WITH (DROP_EXISTING = ON);
GO
/*Re-create clustered columnstore index*/
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON);
```

Pokud je pro tabulku historie s clusterovaným indexem columnstore nakonfigurované omezené období uchování, nemůžete v této tabulce vytvořit další indexy neclusterovaných B-stromu:

```sql
CREATE NONCLUSTERED INDEX IX_WebHistNCI ON WebsiteUserInfoHistory ([UserName])
```

Pokus o provedení výše uvedeného příkazu se nezdaří s následující chybou:

*Msg 13772, Level 16, state 1 <br> </br> nemůže vytvořit neclusterovaný index pro dočasnou tabulku historie ' WebsiteUserInfoHistory ', protože má omezené období uchování a definovaný clusterovaný index columnstore.*

## <a name="querying-tables-with-retention-policy"></a>Dotazování tabulek pomocí zásad uchovávání informací

Všechny dotazy v dočasné tabulce automaticky vyfiltrují historické řádky, které odpovídají omezeným zásadám uchovávání informací, aby nedocházelo k nepředvídatelným a nekonzistentním výsledkům, protože úloha čištění může odstranit staré řádky, a to v *jakémkoli časovém okamžiku a v libovolném pořadí*.

Následující obrázek znázorňuje plán dotazu pro jednoduchý dotaz:

```sql
SELECT * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME ALL;
```

Plán dotazu obsahuje další filtr aplikovaný do sloupce konec období (ValidTo) v rámci operátoru prohledání clusterovaného indexu v tabulce historie (zvýrazněný). V tomto příkladu se předpokládá, že v tabulce WebsiteUserInfo byl nastavené období uchování na jeden měsíc.

![Filtr dotazů na uchování](./media/temporal-tables-retention-policy/queryexecplanwithretention.png)

Pokud se však tabulka historie dotazuje přímo, můžou se zobrazit řádky, které jsou starší než zadaná doba uchovávání, ale bez jakékoli záruky pro opakující se výsledky dotazu. Následující obrázek znázorňuje plán spuštění dotazu pro dotaz v tabulce historie bez použití dalších filtrů:

![Dotazování na historii bez filtru uchovávání](./media/temporal-tables-retention-policy/queryexecplanhistorytable.png)

Nespoléhat se na obchodní logiku při čtení tabulky historie po dobu uchování, protože můžete získat nekonzistentní nebo neočekávané výsledky. Pro analýzu dat v dočasných tabulkách doporučujeme použít dočasné dotazy s klauzulí FOR SYSTEM_TIME.

## <a name="point-in-time-restore-considerations"></a>Předpoklady pro obnovení bodu v čase

Při vytváření nové databáze [obnovením existující databáze k určitému bodu v čase](recovery-using-backups.md)bude dočasné uchovávání zakázáno na úrovni databáze. (příznak **is_temporal_history_retention_enabled** nastavený na vypnuto). Tato funkce umožňuje kontrolovat všechny historické řádky při obnovení, aniž byste se museli zabývat tím, že se zastaralými řádky odeberou předtím, než se jim zobrazí dotaz. Můžete ji použít ke *kontrole historických dat nad rámec nakonfigurované doby uchovávání*.

Řekněme, že dočasná tabulka má určenou dobu uchování v měsíci. Pokud byla vaše databáze vytvořena na úrovni služby Premium, budete moci vytvořit kopii databáze se stavem databáze až 35 dní zpět v minulosti. To vám umožní analyzovat historické řádky, které jsou až 65 dnů staré, a to tak, že se dotazují přímo na tabulku historie.

Pokud chcete aktivovat dočasné uchovávání informací, spusťte následující příkaz Transact-SQL po obnovení bodu v čase:

```sql
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
```

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět, jak používat dočasné tabulky v aplikacích, podívejte [se na Začínáme s dočasnými tabulkami](../temporal-tables.md).

Navštivte kanál 9, kde se dozvíte, jak se [zákazníkem dočasná implementace](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) doznala a sledujte [živý dočasný názor](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016).

Podrobné informace o dočasných tabulkách najdete v podrobných [tabulkách](/sql/relational-databases/tables/temporal-tables).
