---
title: Správa historických dat v časových tabulkách
description: Přečtěte si, jak pomocí zásad časového uchovávání dat udržet historická data pod kontrolou.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab
ms.date: 09/25/2018
ms.openlocfilehash: 3c2460c6f5e0905f45106148ecc3e8a949cf221f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73820685"
---
# <a name="manage-historical-data-in-temporal-tables-with-retention-policy"></a>Správa historických dat v časových tabulkách se zásadami uchovávání informací

Časové tabulky mohou zvětšit velikost databáze více než běžné tabulky, zejména pokud uchováte historická data po delší časové období. Proto jsou zásady uchovávání historických dat důležitým aspektem plánování a správy životního cyklu každé časové tabulky. Časové tabulky v Azure SQL Database jsou dodávány se snadno použitelným mechanismem uchovávání informací, který vám pomůže provést tento úkol.

Retence časové historie lze konfigurovat na úrovni jednotlivých tabulek, což umožňuje uživatelům vytvářet flexibilní zásady stárnutí. Použití časové retence je jednoduché: vyžaduje pouze jeden parametr, který má být nastaven během vytváření tabulky nebo změny schématu.

Po definování zásad uchovávání informací Azure SQL Database začne pravidelně kontrolovat, pokud existují historické řádky, které jsou způsobilé pro automatické vyčištění dat. Identifikace odpovídajících řádků a jejich odebrání z tabulky historie probíhá transparentně, v úloze na pozadí, která je naplánována a spuštěna systémem. Podmínka stáří pro řádky tabulky historie je kontrolována na základě sloupce představujícího konec SYSTEM_TIME období. Pokud je například doba uchovávání nastavena na šest měsíců, řádky tabulky způsobilé k vyčištění splňují následující podmínku:

```
ValidTo < DATEADD (MONTH, -6, SYSUTCDATETIME())
```

V předchozím příkladu jsme předpokládali, že sloupec **ValidTo** odpovídá konci SYSTEM_TIME období.

## <a name="how-to-configure-retention-policy"></a>Konfigurace zásad uchovávání informací

Před konfigurací zásad uchovávání informací pro časovou tabulku nejprve zkontrolujte, zda je *na úrovni databáze*povoleno časové historické uchovávání .

```
SELECT is_temporal_history_retention_enabled, name
FROM sys.databases
```

Příznak databáze **is_temporal_history_retention_enabled** je ve výchozím nastavení nastaven na zapnuto, ale uživatelé jej mohou změnit pomocí příkazu ALTER DATABASE. Je také automaticky nastavena na OFF po [bodu v čase obnovení](sql-database-recovery-using-backups.md) operace. Chcete-li povolit vyčištění uchování časové historie pro databázi, proveďte následující příkaz:

```sql
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
```

> [!IMPORTANT]
> Uchování pro časové tabulky můžete nakonfigurovat i v případě, **že je is_temporal_history_retention_enabled** vypnuto, ale v takovém případě se nespustí automatické vyčištění pro staré řádky.

Zásady uchovávání informací jsou konfigurovány během vytváření tabulky zadáním hodnoty parametru HISTORY_RETENTION_PERIOD:

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

Azure SQL Database umožňuje určit dobu uchování pomocí různých časových jednotek: DNY, TÝDNY, MĚSÍCE a ROKY. Pokud je vynechánHISTORY_RETENTION_PERIOD předpokládá se, že se předpokládá nekonečná uchování. Můžete také použít INFINITE klíčové slovo explicitně.

V některých případech můžete chtít nakonfigurovat uchovávání informací po vytvoření tabulky nebo změnit dříve nakonfigurovanou hodnotu. V takovém případě použijte příkaz ALTER TABLE:

```sql
ALTER TABLE dbo.WebsiteUserInfo
SET (SYSTEM_VERSIONING = ON (HISTORY_RETENTION_PERIOD = 9 MONTHS));
```

> [!IMPORTANT]
> Nastavení SYSTEM_VERSIONING na OFF *nezachová* hodnotu retenčního období. Nastavení SYSTEM_VERSIONING na ZAPNUTO bez HISTORY_RETENTION_PERIOD výslovně určené ho za následek nekonečné retenční období.

Chcete-li zkontrolovat aktuální stav zásad uchovávání informací, použijte následující dotaz, který spojuje příznak povolení časového uchovávání na úrovni databáze s obdobími uchovávání informací pro jednotlivé tabulky:

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


## <a name="how-sql-database-deletes-aged-rows"></a>Jak databáze SQL odstraňuje staré řádky

Proces čištění závisí na rozložení indexu tabulky historie. Je důležité si všimnout, že *pouze tabulky historie s seskupeným indexem (B-strom nebo columnstore) může mít nakonfigurované zásady omezené horečné akce*. Úloha na pozadí je vytvořena k provedení vyčištění starých dat pro všechny časové tabulky s omezenou dobou uchovávání.
Logika vyčištění pro seskupený index rowstore (B-tree) odstraní starý řádek v menších blocích (až 10 kB) a minimalizuje tlak na protokol databáze a podsystém VO. Přestože logika vyčištění využívá požadovaný index B-stromu, pořadí odstranění pro řádky starší než retenční období nelze pevně zaručit. Proto *nepoužívejte žádnou závislost na pořadí čištění ve vašich aplikacích*.

Úloha vyčištění pro clusterované columnstore odebere celé [skupiny řádků](https://msdn.microsoft.com/library/gg492088.aspx) najednou (obvykle obsahují 1 milion řádků každý), což je velmi efektivní, zejména při generování historických dat ve vysokém tempu.

![Uchovávání informací úložiště sloupců seskupeného v clusteru](./media/sql-database-temporal-tables-retention-policy/cciretention.png)

Vynikající komprese dat a efektivní vyčištění uchovávání dat umožňuje clusterovaný index columnstore ideální volbou pro scénáře, kdy vaše úloha rychle generuje velké množství historických dat. Tento vzor je typický pro [úlohy intenzivní transakční zpracování, které používají časové tabulky](https://msdn.microsoft.com/library/mt631669.aspx) pro sledování změn a auditování, analýzu trendů nebo ingestování dat IoT.

## <a name="index-considerations"></a>Aspekty indexu

Úloha vyčištění pro tabulky s indexem s clusterovaným úložištěm řádků vyžaduje, aby index začal se sloupcem odpovídajícím konci SYSTEM_TIME období. Pokud takový index neexistuje, nelze nakonfigurovat omezenou dobu uchování:

*Msg 13765, Level 16, State 1 <br> </br> Nastavení konečné doby uchovávání se nezdařilo v časové tabulce s verzí systému 'temporaltestdb.dbo.WebsiteUserInfo', protože tabulka historie 'temporalstagetestdb.dbo.WebsiteUserInfoHistory' neobsahuje požadovaný seskupený index. Zvažte vytvoření clusterované columnstore nebo B-tree index počínaje sloupec, který odpovídá konec SYSTEM_TIME období, v tabulce historie.*

Je důležité si všimnout, že výchozí tabulka historie vytvořená službou Azure SQL Database již obsahuje clusterovaný index, který je kompatibilní se zásadami uchovávání informací. Pokud se pokusíte odebrat tento index v tabulce s omezenou dobu uchování, operace se nezdaří s následující chybou:

*Msg 13766, Level 16, State 1 <br> </br> Nelze vynechat seskupený index WebsiteUserInfoHistory.IX_WebsiteUserInfoHistory' protože se používá pro automatické vyčištění starých dat. Zvažte nastavení HISTORY_RETENTION_PERIOD na INFINITE na odpovídající systémové verzi časové tabulky, pokud potřebujete k poklesu tohoto indexu.*

Vyčištění v indexu clusterovanécolumnstore funguje optimálně, pokud jsou vloženy historické řádky ve vzestupném pořadí (seřazeno podle sloupce konce období), což je vždy případ, kdy je tabulka historie naplněna výhradně SYSTEM_VERSIONIOING mechanismus. Pokud řádky v tabulce historie nejsou seřazeny do konce období sloupce (což může být případ, pokud jste migrovali existující historická data), měli byste znovu vytvořit clusterovaný columnstore index nad b-strom rowstore index, který je správně seřazené, k dosažení optimálního Výkon.

Vyhněte se opětovnému sestavení clusterovaného indexu columnstore v tabulce historie s omezenou dobu uchování, protože může změnit řazení ve skupinách řádků přirozeně vynucených operací správy verzí systému. Pokud potřebujete znovu sestavit clusterovaný index columnstore v tabulce historie, udělejte to opětovným vytvořením nad kompatibilním indexem B-stromu a zachováním řazení ve skupinách řádků, které jsou nezbytné pro pravidelné čištění dat. Stejný přístup by měl být přijat, pokud vytvoříte datální tabulku s existující historií tabulky, která má seskupený index sloupců bez zaručeného pořadí dat:

```sql
/*Create B-tree ordered by the end of period column*/
CREATE CLUSTERED INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory (ValidTo)
WITH (DROP_EXISTING = ON);
GO
/*Re-create clustered columnstore index*/
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON);
```

Pokud je pro tabulku historie s indexem clusterovaného columnstore nakonfigurována doba omezené horečnaté doby uchovávání, nelze v této tabulce vytvořit další indexy b-stromu bez clusteru:

```sql
CREATE NONCLUSTERED INDEX IX_WebHistNCI ON WebsiteUserInfoHistory ([UserName])
```

Pokus o spuštění výše uvedeného příkazu se nezdaří s následující chybou:

*Msg 13772, Level 16, State 1 <br> </br> Nelze vytvořit index bez clusteru v tabulce časové historie "WebsiteUserInfoHistory", protože má definované omezené retenční období a index clusterovaného columnstore.*

## <a name="querying-tables-with-retention-policy"></a>Dotazování tabulek se zásadami uchovávání informací

Všechny dotazy na časové tabulce automaticky odfiltrovat historické řádky odpovídající konečné zásady uchovávání informací, aby se zabránilo nepředvídatelné a nekonzistentní výsledky, protože staré řádky mohou být odstraněny úlohou vyčištění, *v libovolném okamžiku a v libovolném pořadí*.

Následující obrázek znázorňuje plán dotazu pro jednoduchý dotaz:

```sql
SELECT * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME ALL;
```

Plán dotazu obsahuje další filtr použitý na konec sloupce období (ValidTo) v operátoru Clustered Index Scan v tabulce historie (zvýrazněno). Tento příklad předpokládá, že jedno období uchování MĚSÍCE bylo nastaveno v tabulce WebsiteUserInfo.

![Filtr retenčního dotazu](./media/sql-database-temporal-tables-retention-policy/queryexecplanwithretention.png)

Pokud však dotazujete tabulku historie přímo, mohou se zobrazit řádky, které jsou starší než zadaná doba uchovávání, ale bez záruky za opakovatelné výsledky dotazu. Následující obrázek znázorňuje plán spuštění dotazu pro dotaz v tabulce historie bez použití dalších filtrů:

![Dotazování historie bez filtru uchovávání informací](./media/sql-database-temporal-tables-retention-policy/queryexecplanhistorytable.png)

Nespoléhejte se na obchodní logiku na čtení tabulky historie po dobu uchování, protože může dojít k nekonzistentním nebo neočekávaným výsledkům. Doporučujeme použít časové dotazy s FOR SYSTEM_TIME klauzule pro analýzu dat v časových tabulkách.

## <a name="point-in-time-restore-considerations"></a>Důležité informace o obnovení bodu v čase

Při vytváření nové databáze [obnovením existující databáze do určitého bodu v čase](sql-database-recovery-using-backups.md), má časové uchovávání zakázáno na úrovni databáze. **(is_temporal_history_retention_enabled** příznak nastaven na OFF). Tato funkce umožňuje prozkoumat všechny historické řádky při obnovení, aniž byste se museli obávat, že jsou starší řádky odebrány dříve, než se k nim dostanete dotaz. Můžete ji použít ke *kontrole historických dat nad rámec nakonfigurované doby uchovávání*.

Řekněme, že časová tabulka má zadanou dobu uchování jednoho měsíce. Pokud vaše databáze byla vytvořena v úrovni služby Premium, budete moci vytvořit kopii databáze se stavem databáze až 35 dní zpět v minulosti. To efektivně by vám umožní analyzovat historické řádky, které jsou až 65 dní staré dotazováním na tabulku historie přímo.

Pokud chcete aktivovat časové vyčištění uchovávání informací, spusťte následující příkaz Transact-SQL po obnovení bodu v čase:

```sql
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
```

## <a name="next-steps"></a>Další kroky

Chcete-li se dozvědět, jak používat časové tabulky ve vašich aplikacích, podívejte se na [začínáme s časovými tabulkami v Azure SQL Database](sql-database-temporal-tables.md).

Navštivte Channel 9 slyšet [skutečný zákazník časové provádění úspěch a](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) sledovat živé časové [demonstrace](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016).

Podrobné informace o časových tabulkách naleznete v [dokumentaci k msdn](https://msdn.microsoft.com/library/dn935015.aspx).
