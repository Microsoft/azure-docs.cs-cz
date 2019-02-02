---
title: Správa historických dat v dočasných tabulek se zásady uchovávání informací | Dokumentace Microsoftu
description: Další informace o použití zásad pro dočasné uchovávání informací pro historických dat ve vaší kontrole.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab
manager: craigg
ms.date: 09/25/2018
ms.openlocfilehash: 62e88d912c55015f87cc00f21527010ad01ee00c
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2019
ms.locfileid: "55560851"
---
# <a name="manage-historical-data-in-temporal-tables-with-retention-policy"></a>Správa historických dat v dočasných tabulek se zásady uchovávání informací

Dočasné tabulky může zvýšit velikost databáze více než regulární tabulek, zejména v případě, že uchovávání historických dat po dobu delší časové období. Zásady uchovávání informací pro historických dat, proto je důležitou součástí plánování a správu životního cyklu každý dočasnou tabulku. Dočasné tabulky ve službě Azure SQL Database jsou dostupné snadným ovládáním uchování mechanismus, který umožňuje provést tuto úlohu.

Uchování dočasné historie může být nakonfigurována na úrovni jednotlivé tabulky, který umožňuje uživatelům vytvářet flexibilní stárnoucích zásady. Použití dočasné uchování je jednoduchý: vyžaduje jenom jeden parametr, chcete-li nastavit během vytváření nebo schématu změny tabulky.

Po definování zásady uchovávání informací Azure SQL Database začne pravidelně kontroluje, jestli nejsou historických řádky, které jsou vhodné pro automatické čištění. Identifikace odpovídajících řádků a jejich odebrání z tabulky historie probíhá transparentně, úlohy na pozadí, který je naplánován a spustit v systému. Stáří podmínku pro řádky tabulky historie je zaškrtnuté políčko podle sloupce představující konec období SYSTEM_TIME. Pokud doba uchování, je třeba nastavit na šest měsíců, tabulka řádků způsobilých k vyčištění splňovat následující podmínky:

```
ValidTo < DATEADD (MONTH, -6, SYSUTCDATETIME())
```

V předchozím příkladu jsme předpokládá se, že **ValidTo** sloupec odpovídá konci období SYSTEM_TIME.

## <a name="how-to-configure-retention-policy"></a>Jak nakonfigurovat zásady uchovávání informací

Než začnete konfigurovat zásady uchovávání informací pro dočasnou tabulku, proveďte nejprve kontrolu, jestli je povolené dočasné uchovávání historických *na úrovni databáze*.

```
SELECT is_temporal_history_retention_enabled, name
FROM sys.databases
```

Databáze příznak **is_temporal_history_retention_enabled** nastavená na ON ve výchozím nastavení, ale uživatelé mohou změnit pomocí příkazu ALTER DATABASE. Je také automaticky nastaven na hodnotu OFF po [obnovení bodu v čase](sql-database-recovery-using-backups.md) operace. Pokud chcete povolit vyčištění uchování dočasné historie pro vaši databázi, spusťte následující příkaz:

```sql
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
```

> [!IMPORTANT]
> Nakonfigurujete uchovávání dat pro dočasné tabulky i v případě **is_temporal_history_retention_enabled** je VYPNUTÝ, ale není v takovém případě aktivuje automatické vyčištění starých řádků.

Při vytváření tabulky tak, že zadáte hodnotu pro parametr HISTORY_RETENTION_PERIOD jsou nakonfigurované zásady uchovávání informací:

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

Azure SQL Database umožňuje zadat období uchovávání dat s použitím různých časových jednotek: DNŮ, týdnů, měsíců a ROKŮ. HISTORY_RETENTION_PERIOD je vynechána, předpokládá se NEKONEČNÉ uchování. NEKONEČNÉ – klíčové slovo můžete použít také explicitně.

V některých případech můžete chtít konfigurovat uchovávání po vytvoření tabulky nebo chcete-li změnit dříve konfigurovaná hodnota. V takovém případě použijte příkaz ALTER TABLE:

```sql
ALTER TABLE dbo.WebsiteUserInfo
SET (SYSTEM_VERSIONING = ON (HISTORY_RETENTION_PERIOD = 9 MONTHS));
```

> [!IMPORTANT]
> Nastavení možnosti SYSTEM_VERSIONING na hodnotu OFF *nezachová* hodnota období uchování. Nastavení možnosti SYSTEM_VERSIONING na ON bez HISTORY_RETENTION_PERIOD explicitně výsledkem zadané NEKONEČNOU dobu uchování.

Pokud chcete zkontrolovat aktuální stav zásad uchovávání informací, použijte následující dotaz, který spojuje příznak povolení dočasné uchovávání informací na úrovni databáze se doby uchování pro jednotlivé tabulky:

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


## <a name="how-sql-database-deletes-aged-rows"></a>Jak SQL Database odstraňuje zastaralá řádků

Proces vyčištění závisí na index rozložení tabulky historie. Je důležité si všimněte si, že *tabulky historie clusterovaný index, (B-stromu nebo columnstore) můžou mít omezené období uchování zásady nakonfigurované pouze*. Úlohy na pozadí se vytvoří, vyčistěte zastaralá data pro všechny dočasné tabulky s omezeného období uchování.
Vyčištění logiku pro clusterovaný index rowstore (B-stromu) odstraní zastaralá řádek menších částech (až 10 tisíc) minimalizovat tlak na protokol databáze a vstupně-výstupního subsystému. Přestože vyčištění logiky využívá nezbytné indexu B-stromu, pořadí odstranění starší než doba uchování nelze zaručit pevně řádků. Proto *nepřebírají žádné závislosti na vyčištění pořadí, ve svých aplikacích*.

Odebere celou úlohu čištění pro Clusterované columnstore [řádek skupiny](https://msdn.microsoft.com/library/gg492088.aspx) najednou (obvykle obsahovat 1 milion řádků každý), což je velmi efektivní, zejména v případě, že se vygeneruje historická data při vysoké tempo.

![Uchování Clusterované columnstore](./media/sql-database-temporal-tables-retention-policy/cciretention.png)

Komprese vynikající dat a provede vyčištění efektivní uchovávání clusterovaný columnstore index ideální volbou pro scénáře, když vaše úlohy generují rychle vysoké množství historických dat. Tento vzor je typické pro náročné na prostředky [úloh zpracování transakcí, které používají dočasné tabulky](https://msdn.microsoft.com/library/mt631669.aspx) pro řešení change tracking a auditování, analýze trendů nebo zpracování dat IoT.

## <a name="index-considerations"></a>Důležité informace o index

Úloha vyčištění pro tabulky pomocí clusterovaného indexu rowstore vyžaduje index začít s sloupce odpovídající konci období SYSTEM_TIME. Pokud takové indexu neexistuje, nelze nakonfigurovat omezené období uchování:

*Msg – 13765, Level 16, State 1 <br> </br> nastavení omezeného období uchování pro dočasnou tabulku systémovou temporalstagetestdb.dbo.WebsiteUserInfo selhala, protože tabulka historie. temporalstagetestdb.dbo.WebsiteUserInfoHistory' neobsahuje požadovaný clusterovaný index. Zvažte vytvoření clusterovaného indexu columnstore nebo indexu B-stromu počínaje sloupcem, který odpovídá konci SYSTEM_TIME period, tabulku historie.*

Je důležité si všimněte, že výchozí tabulku historie již vytvořené službou Azure SQL Database má clusterovaný index, který je kompatibilní pro zásady uchovávání informací. Pokud se pokusíte odebrat indexu u tabulky se omezeného období uchování, operace se nezdaří s následující chybou:

*Msg – 13766, Level 16, State 1 <br> </br> clusterovaný index 'WebsiteUserInfoHistory.IX_WebsiteUserInfoHistory' nelze vyřadit, protože se používá k automatickému čištění zastaralá data. Vezměte v úvahu history_retention_period na INFINITE odpovídající-systémovou dočasnou tabulku. Pokud potřebujete tento index.*

Čištění na clusterovaný index columnstore funguje optimálně vložili historických řádků ve vzestupném pořadí podle (řazení na konci období sloupec), je vždy případ, kdy v tabulce historie je vyplněn výhradně SYSTEM_VERSIONIOING mechanismus. Pokud je řádků v tabulce historie nejsou seřazené podle konec období sloupce (která může dojít, pokud jste migrovali stávající historická data), je třeba znovu vytvořit clusterovaný index columnstore nad B-stromu můžete vytvořit index rowstore, že je správně uspořádány, abyste dosáhli optimálního výkon.

Vyhněte se znovu sestavit clusterovaný index columnstore v tabulce historie s dobou omezené období uchování, protože mohou změnit pořadí ve skupinách řádků přirozeně stanovené operace systému správy verzí. Pokud je potřeba znovu sestavit clusterovaný index columnstore v tabulce historie, to udělejte tak, že znovu vytvoříte nad kompatibilní indexu B-stromu, zachování pořadí v rowgroups nezbytné pro pravidelné čištění. Stejným způsobem se má vzít-li vytvořit dočasnou tabulku s existující tabulku historie, která má clusterovaný index sloupce bez data zaručené pořadí:

```sql
/*Create B-tree ordered by the end of period column*/
CREATE CLUSTERED INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory (ValidTo)
WITH (DROP_EXISTING = ON);
GO
/*Re-create clustered columnstore index*/
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON);
```

Pokud omezeného období uchování je nakonfigurovaná pro tabulku historie pomocí clusterovaného indexu columnstore, nelze vytvořit další neclusterovaných indexů B-stromu pro danou tabulku:

```sql
CREATE NONCLUSTERED INDEX IX_WebHistNCI ON WebsiteUserInfoHistory ([UserName])
```

Pokus o spuštění výše příkaz selže s následující chybou:

*Msg – 13772, Level 16, State 1 <br> </br> nelze vytvořit neclusterovaný index pro dočasnou tabulku historie 'WebsiteUserInfoHistory' protože má omezené období uchování a definovaný clusterovaný index columnstore.*

## <a name="querying-tables-with-retention-policy"></a>Dotazy na tabulky s zásady uchovávání informací

Všechny dotazy na dočasnou tabulku automaticky vyfiltrovat historických řádky odpovídající zásady omezené období uchování, aby se zabránilo neočekávaným a konzistentní výsledky, protože stará řádků může odstranit úlohu čištění *v libovolném bodě v čase a v libovolné pořadí*.

Následující obrázek znázorňuje plán dotazu pro jednoduchý dotaz:

```sql
SELECT * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME ALL;
```

Plán dotazu obsahuje další filtr použít na konci období ve sloupci (ValidTo) v operátoru kontrola Clusterovaného indexu pro tabulku historie (zvýrazněno). Tento příklad předpokládá tohoto MĚSÍČNÍHO období uchovávání dat byla nastavena na WebsiteUserInfo tabulky.

![Filtr dotazu pro uchovávání dat](./media/sql-database-temporal-tables-retention-policy/queryexecplanwithretention.png)

Ale když odešlete dotaz přímo tabulku historie, může se zobrazit řádky, které jsou starší než zadaná doba uchovávání období, ale bez jakékoli záruky pro výsledky dotazu opakovatelné. Následující obrázek znázorňuje plán provádění dotazu pro dotaz na tabulku historie bez další použité filtry:

![Dotazování na historii bez filtru uchovávání informací](./media/sql-database-temporal-tables-retention-policy/queryexecplanhistorytable.png)

Nespoléhejte obchodní logiky na čtení tabulky historie mimo dobu uchování až získáte nekonzistentní nebo neočekávané výsledky. Doporučujeme používat dočasné dotazy s klauzulí FOR SYSTEM_TIME pro analýzu dat v dočasných tabulek.

## <a name="point-in-time-restore-considerations"></a>Důležité informace o čase obnovení k časovému okamžiku

Když vytvoříte novou databázi pomocí [existující databázi obnovení k určitému bodu v čase](sql-database-recovery-using-backups.md), má dočasné uchování zakázány na úrovni databáze. (**is_temporal_history_retention_enabled** příznak nastaven na hodnotu OFF). Tato funkce umožňuje prozkoumat všechny historické řádků při obnovení, ale nemusíme se starat, zastaralá řádky jsou odebrány, než se ponoříte k jejich dotazování. Můžete použít k *kontrolovat historická data nad rámec nastavené doby uchování*.

Řekněme, že dočasná tabulka má období uchování jeden měsíc. Pokud vaše databáze byla vytvořena v úrovni Premium služby, by mohli vytvořit kopii databáze s daným stavem databáze až 35 dnů zpět v minulosti. Který efektivně umožní k analýze historických řádky, které jsou až 65 dny dotazováním tabulku historie.

Pokud chcete aktivovat čištění dočasné uchovávání, spusťte následující příkaz jazyka Transact-SQL po bodu v čase:

```sql
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
```

## <a name="next-steps"></a>Další postup

Další informace o použití dočasných tabulek se ve svých aplikacích, projděte si [Začínáme s dočasnými tabulkami ve službě Azure SQL Database](sql-database-temporal-tables.md).

Navštivte web Channel 9 a poslechněte si [skutečné dočasné implementace úspěch příběh a zjistěte](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) a sledujte [live dočasné ukázku](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016).

Podrobné informace o dočasných tabulek, najdete v tématu [dokumentaci MSDN](https://msdn.microsoft.com/library/dn935015.aspx).
