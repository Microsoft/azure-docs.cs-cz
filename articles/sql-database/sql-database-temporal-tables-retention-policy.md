---
title: Správa historických dat v dočasných tabulek se zásady uchovávání informací | Microsoft Docs
description: Další informace o použití zásad dočasné uchovávání informací pro historických dat pod kontrolou.
services: sql-database
author: bonova
manager: craigg
ms.service: sql-database
ms.custom: develop databases
ms.topic: article
ms.date: 04/01/2018
ms.author: bonova
ms.openlocfilehash: 3175236306f05831a78ae8ca01911d0c5d19f893
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="manage-historical-data-in-temporal-tables-with-retention-policy"></a>Správa historických dat v dočasných tabulek se zásady uchovávání informací
Dočasné tabulky může zvýšit velikost databáze více než regulérních tabulkách, zejména v případě, že zachováte historických dat pro delší časové období. Proto zásady uchovávání historických dat je důležitým aspektem plánování a správu životního cyklu každých dočasnou tabulku. Dočasné tabulky v databázi SQL Azure se dodávají s snadno použitelné uchování mechanismus, který umožňuje provedení této úlohy.

Uchování dočasné historie může být nakonfigurována na úrovni jednotlivých tabulce, což umožňuje uživatelům vytvářet flexibilní stárnutí zásady. Použití dočasné uchování je jednoduchý: vyžaduje jenom jeden parametr nastavit během změny schématu nebo vytvoření tabulky.

Po definování zásad uchovávání informací, Azure SQL Database spustí pravidelně kontroluje, zda existují historických řádků, které jsou způsobilé pro automatické čištění. Identifikace odpovídající řádky a jejich odebrání z tabulky historie dojít transparentně, v pozadí úloha, která je naplánována a spustit v systému. Stáří podmínku pro řádky tabulky historie je zaškrtnuta možnost podle sloupce představující konec období SYSTEM_TIME. Pokud doba uchování, například je nastavené na šest měsíců, řádky tabulky, které jsou vhodné pro čištění splňovat následující podmínky:

````
ValidTo < DATEADD (MONTH, -6, SYSUTCDATETIME())
````

V předchozím příkladu jsme předpokládá **ValidTo** sloupec odpovídá na konci období SYSTEM_TIME.

## <a name="how-to-configure-retention-policy"></a>Postup konfigurace zásad uchovávání informací?
Než začnete konfigurovat zásady uchovávání informací pro dočasnou tabulku, proveďte nejprve kontrolu zda je povoleno dočasné uchovávání historických *na úrovni databáze*.

````
SELECT is_temporal_history_retention_enabled, name
FROM sys.databases
````

Databáze příznak **is_temporal_history_retention_enabled** je nastaven na hodnotu ON ve výchozím nastavení, ale uživatelé mohou změnit pomocí příkazu ALTER DATABASE. Je také automaticky nastaví na hodnotu OFF po [obnovení bodu v čase](sql-database-recovery-using-backups.md) operaci. Pokud chcete povolit dočasné uchování vyčištění pro vaši databázi, spusťte následující příkaz:

````
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
````

> [!IMPORTANT]
> Uchování pro dočasné tabulky, i když můžete nakonfigurovat **is_temporal_history_retention_enabled** je VYPNUTÝ, ale není v takovém případě aktivuje automatické čištění zastaralá řádků.
> 
> 

Zásady uchovávání informací je nakonfigurované během vytváření tabulky zadáním hodnoty pro parametr HISTORY_RETENTION_PERIOD:

````
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
````

Databáze SQL Azure umožňuje určit dobu uchování pomocí různých časových jednotek: počet dnů, týdnů, měsíců a let. Pokud je vynechán HISTORY_RETENTION_PERIOD, se předpokládá NEKONEČNÉ uchování. Můžete taky NEKONEČNÉ – klíčové slovo explicitně.

V některých případech můžete chtít nakonfigurovat uchování po vytvoření tabulky nebo ke změně nakonfigurovaná hodnota. V takovém případě použijte příkaz ALTER TABLE:

````
ALTER TABLE dbo.WebsiteUserInfo
SET (SYSTEM_VERSIONING = ON (HISTORY_RETENTION_PERIOD = 9 MONTHS));
````

> [!IMPORTANT]
> Nastavení možnosti SYSTEM_VERSIONING na hodnotu OFF *nezachovává* hodnotu doby uchování. Nastavení možnosti SYSTEM_VERSIONING na ON bez HISTORY_RETENTION_PERIOD zadaného explicitně výsledky v NEKONEČNOU dobu uchování.
> 
> 

Pokud chcete zkontrolovat aktuální stav zásady uchovávání informací, použijte následující dotaz, který spojuje příznak povolení dočasné uchovávání dat na úrovni databáze s dob uchování u jednotlivých tabulek:

````
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
````


## <a name="how-sql-database-deletes-aged-rows"></a>Jak SQL Database odstraní stará řádky?
Proces vyčištění závisí na rozložení indexu tabulky historie. Je důležité si všimněte si, že *pouze tabulky historie clusterovaný index (B-stromu nebo columnstore) může mít omezený uchování zásady nakonfigurované*. Úlohy na pozadí se vytvoří pro Vyčistit stará data pro všechny dočasné tabulky s dobou uchování omezené.
Vyčištění logiku pro clusterovaný index rowstore (B-stromu) odstraňuje zastaralá řádek v menší bloky dat (až 10 kB) minimalizovat tlak na protokol databáze a subsystému vstupně-výstupní operace. I když čištění logiku využívá požadovaným indexem B-stromu, pořadí odstranění starší než doba uchování dat se nedá zaručit pevně řádků. Proto *nepřebírají žádné závislostí v pořadí čištění ve svých aplikacích*.

Úloha vyčištění pro Clusterované columnstore odebere celý [řádek skupiny](https://msdn.microsoft.com/library/gg492088.aspx) najednou (obvykle obsahují 1 milionu řádků každý), což je velmi efektivní, zejména v případě, že historická data se generuje vysoký tempem.

![Clusterované columnstore uchování](./media/sql-database-temporal-tables-retention-policy/cciretention.png)

Komprese dat vynikající a efektivní uchování čištění díky clusterovaný columnstore index ideální volbou pro scénáře, když vaše úlohy generují rychle vysoký objem historická data. Tento vzor je typické pro náročné na prostředky [úlohy zpracování transakcí, které používají dočasné tabulky](https://msdn.microsoft.com/library/mt631669.aspx) pro sledování změn a auditování, analýza trendu nebo přijímání dat IoT.

## <a name="index-considerations"></a>Aspekty indexu
Úloha vyčištění tabulky s clusterovaný index rowstore vyžaduje index začínat sloupec odpovídající konci období SYSTEM_TIME. Pokud takový indexu neexistuje, nelze nakonfigurovat doby uchování konečné:

*Msg 13765, Level 16, State 1 <br> </br> nastavení konečné uchovávají se na dočasné tabulce se systémovou správou verzí temporalstagetestdb.dbo.WebsiteUserInfo, protože tabulka historie. temporalstagetestdb.dbo.WebsiteUserInfoHistory' neobsahuje požadované clusterovaný index. Zvažte vytvoření clusteru columnstore nebo B-stromu index začínající sloupec, který odpovídá konec SYSTEM_TIME period v tabulce historie.*

Je důležité si všimněte si, že v tabulce historie výchozí vytvořené databáze SQL Azure již má clusterovaný index, který je kompatibilní s pro zásady uchovávání informací. Pokud se pokusíte odebrat tohoto indexu v tabulce s dobou uchování omezené, operace selže s následující chybou:

*Msg 13766, Level 16, State 1 <br> </br> nelze vyřadit clusterovaný index 'WebsiteUserInfoHistory.IX_WebsiteUserInfoHistory', protože je používán pro automatické čištění zastaralá data. Zvažte nastavení HISTORY_RETENTION_PERIOD k NEKONEČNÉ na odpovídající systémovou správou verzí dočasnou tabulku, pokud je třeba vyřadit tohoto indexu.*

Čištění na clusterovaný index columnstore funguje optimálně vložit historických řádků ve vzestupném pořadí (na konci období sloupec), tomu je vždy, když v tabulce historie je naplněn výhradně SYSTEM_VERSIONIOING mechanismus. Pokud nejsou řádky v tabulce historie seřazené podle konec sloupec období (který může být případ, pokud jste migrovali existující historická data), je třeba znovu vytvořit clusterovaný index columnstore nad B-stromu vytvořit index, který je správně seřadit, abyste dosáhli optimálního výkon.

Vyhněte se znovu sestavit clusterovaný index columnstore v tabulce historie s dobou uchování omezené, protože může změnit, řazení do skupin řádků přirozeně vyvolané operace systémové správy verzí. Pokud potřebujete znovu sestavit clusterovaný index columnstore v tabulce historie, učiňte tak, že ho znovu vytvoříte nad kompatibilní index B-stromu, zachování pořadí v rowgroups potřebné pro čištění regulární dat. Pokud vytvoříte dočasnou tabulku s stávající tabulka historie, který obsahuje clusterovaný index sloupce bez pořadí zaručenou dat by měla být provedena ve stejný přístup:

````
/*Create B-tree ordered by the end of period column*/
CREATE CLUSTERED INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory (ValidTo)
WITH (DROP_EXISTING = ON);
GO
/*Re-create clustered columnstore index*/
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON);
````

Pokud dobu uchování konečné je nakonfigurována pro tabulku historie s clusterovaný index columnstore, nelze vytvořit další neclusterovaných indexů B-stromu v této tabulce:

````
CREATE NONCLUSTERED INDEX IX_WebHistNCI ON WebsiteUserInfoHistory ([UserName])
````

Pokus o provedení výše příkaz selže s následující chybou:

*Msg 13772, Level 16, State 1 <br> </br> nelze vytvořit neclusterovaný index na dočasnou tabulku historie 'WebsiteUserInfoHistory, protože má doba uchovávání omezené a clusterovaný index columnstore definované.*

## <a name="querying-tables-with-retention-policy"></a>Dotazy na tabulky s zásady uchovávání informací
Všechny dotazy na dočasnou tabulku automaticky vyfiltrovat historických řádky odpovídající zásady uchovávání informací omezené, aby se zabránilo nepředvídatelným a konzistentní výsledky, protože zastaralá řádky může odstranit úlohu čištění *v libovolném bodě v čase a v libovolné pořadí*.

Následující obrázek znázorňuje plán dotazu pro jednoduchý dotaz:

````
SELECT * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME ALL;
````

Plán dotazu obsahuje další filtr použít na konci období sloupce (ValidTo) v operátoru kontrolovat clusterovaný Index pro tabulku historie (zvýrazněné). Tento příklad předpokládá u tabulky WebsiteUserInfo se nastavil této doby uchování jeden měsíc.

![Filtr dotazu uchování](./media/sql-database-temporal-tables-retention-policy/queryexecplanwithretention.png)

Pokud dotaz Tabulka historie přímo, však může zobrazit řádky, které jsou starší než uchovávání období, ale bez jakékoli záruky na výsledky dotazu opakovatelných. Následující obrázek znázorňuje provedení plán dotazu pro dotaz v tabulce historie bez další filtry použít:

![Dotazování historie bez uchování filtru](./media/sql-database-temporal-tables-retention-policy/queryexecplanhistorytable.png)

Nespoléhejte obchodní logiky na čtení tabulky historie mimo dobu uchování, jak můžete obdržet nekonzistentní nebo neočekávané výsledky. Doporučujeme použít dočasné dotazy s klauzulí FOR SYSTEM_TIME pro analýzu dat v dočasných tabulek.

## <a name="point-in-time-restore-considerations"></a>Přejděte v aspekty čas obnovení
Když vytvoříte novou databázi pomocí [obnovení existující databázi k určitému bodu v čase](sql-database-recovery-using-backups.md), má dočasné uchování zakázáno na úrovni databáze. (**is_temporal_history_retention_enabled** příznak nastaven na hodnotu OFF). Tato funkce umožňuje zkontrolovat všechny řádky historických při obnovení, aniž byste museli, že jsou zastaralá řádky odebrat předtím, než dojde k dotazování je. Můžete jej do *zkontrolovat historická data i po uplynutí doby uchování nakonfigurované*.

Řekněme, že dočasná tabulka má období uchování jeden měsíc. Pokud vaše databáze byla vytvořena v rámci úrovně služby Premium, bude moci vytvořit kopii databáze se stav databáze až 35 dnů zpět v minulosti. Které efektivně by umožňují analýza historických řádků, které jsou až 65 dny v tabulce historie přímým dotazováním.

Pokud chcete aktivovat čištění dočasné uchování, spusťte následující příkaz Transact-SQL za bod v době obnovení:

````
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
````

## <a name="next-steps"></a>Další postup
Další informace o použití dočasných tabulek v aplikacích, projděte si [Začínáme s dočasné tabulky v databázi SQL Azure](sql-database-temporal-tables.md).

Navštivte Channel 9 a poslechnout [scénáře úspěchu dočasné implementace skutečné zákazníka](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) a sledujte [live dočasné ukázkový](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016).

Podrobné informace o dočasné tabulky, zkontrolujte [dokumentace MSDN](https://msdn.microsoft.com/library/dn935015.aspx).

