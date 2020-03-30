---
title: 'Známé problémy: Online migrace do databáze SQL'
titleSuffix: Azure Database Migration Service
description: Přečtěte si o známých problémech/omezeních migrace s online migrací do Azure SQL Database pomocí služby Migrace databáze Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: e7efdb7244e2c7e4651a4507b538123f8d320c1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650771"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-database"></a>Známé problémy a omezení migrace s online migrací do Azure SQL Database

Známé problémy a omezení spojené s migracemi online z SQL Serveru do Azure SQL Database jsou popsány níže.

> [!IMPORTANT]
> Při online migraci SQL Serveru do Azure SQL Database není migrace SQL_variant datových typů podporována.

### <a name="migration-of-temporal-tables-not-supported"></a>Migrace dočasných tabulek není podporována.

**Příznakem**

Pokud se zdrojová databáze skládá z jedné nebo více časových tabulek, migrace databáze se nezdaří během operace Úplné načtení dat a může se zobrazit následující zpráva:

```
{ "resourceId":"/subscriptions/<subscription id>/resourceGroups/migrateready/providers/Microsoft.DataMigration/services/<DMS Service name>", "errorType":"Database migration error", "errorEvents":"["Capture functionalities could not be set. RetCode: SQL_ERROR SqlState: 42000 NativeError: 13570 Message: [Microsoft][SQL Server Native Client 11.0][SQL Server]The use of replication is not supported with system-versioned temporal table '[Application. Cities]' Line: 1 Column: -1 "]" }
```

 ![Příklad chyb časové tabulky](media/known-issues-azure-sql-online/dms-temporal-tables-errors.png)

**Řešení**

Použijte k tomu následující postup.

1. Najděte časové tabulky ve zdrojovém schématu pomocí níže uvedeného dotazu.

     ``` 
     select name,temporal_type,temporal_type_desc,* from sys.tables where temporal_type <>0
     ```

2. Vylučte tyto tabulky z okna **Konfigurovat nastavení migrace,** u kterého zadáte tabulky pro migraci.

3. Spusťte aktivitu migrace znovu.

**Materiály**

Další informace naleznete v článku [Časové tabulky](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables?view=sql-server-2017).

### <a name="migration-of-tables-includes-one-or-more-columns-with-the-hierarchyid-data-type"></a>Migrace tabulek zahrnuje jeden nebo více sloupců s datovým typem hierarchyid

**Příznakem**

Během operace "Úplné načtení dat" se může zobrazit výjimka SQL, která naznačuje, že "ntext není kompatibilní s hierarchyid":

![příklad chyb hierarchyid](media/known-issues-azure-sql-online/dms-hierarchyid-errors.png)

**Řešení**

Použijte k tomu následující postup.

1. Najděte uživatelské tabulky, které obsahují sloupce s datovým typem hierarchyid pomocí níže uvedeného dotazu.

      ``` 
      select object_name(object_id) 'Table name' from sys.columns where system_type_id =240 and object_id in (select object_id from sys.objects where type='U')
      ```

2. Vylučte tyto tabulky z okna **Konfigurovat nastavení migrace,** u kterého zadáte tabulky pro migraci.

3. Spusťte aktivitu migrace znovu.

### <a name="migration-failures-with-various-integrity-violations-with-active-triggers-in-the-schema-during-full-data-load-or-incremental-data-sync"></a>Selhání migrace s různými porušeními integrity s aktivními aktivačními událostmi ve schématu během "Úplné načtení dat" nebo "Přírůstková synchronizace dat"

**Řešení**

Použijte k tomu následující postup.

1. Pomocí následujícího dotazu vyhledejte aktivační události, které jsou aktuálně aktivní ve zdrojové databázi:

     ```
     select * from sys.triggers where is_disabled =0
     ```

2. Zakažte aktivační události ve zdrojové databázi pomocí kroků uvedených v článku [ZAKÁZAt AKTIVAČNÍ UDÁLOST (Transact-SQL).](https://docs.microsoft.com/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017)

3. Spusťte aktivitu migrace znovu.

### <a name="support-for-lob-data-types"></a>Podpora obchodních datových typů

**Příznakem**

Pokud je délka sloupce Velký objekt (LOB) větší než 32 kB, data mohou získat zkrácena na cíl. Délku sloupce LOB můžete zkontrolovat pomocí následujícího dotazu:

``` 
SELECT max(DATALENGTH(ColumnName)) as LEN from TableName
```

**Řešení**

Pokud máte sloupec LOB, který je větší než 32 kB, obraťte se na technický tým na [Ask Migrace databáze Azure](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

### <a name="issues-with-timestamp-columns"></a>Problémy se sloupci časového razítka

**Příznakem**

Služba migrace databáze Azure nemigruje zdrojovou hodnotu časového razítka. Místo toho služba Migrace databáze Azure generuje novou hodnotu časového razítka v cílové tabulce.

**Řešení**

Pokud potřebujete Azure Database Migration Service migrovat přesnou hodnotu časového razítka uloženou ve zdrojové tabulce, obraťte se na technický tým v [zeptejte se migrace databáze Azure](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

### <a name="data-migration-errors-dont-provide-additional-details-on-the-database-detailed-status-blade"></a>Chyby migrace dat neposkytují další podrobnosti v datovém panelu podrobného stavu databáze

**Příznakem**

Když narazíte na selhání migrace v zobrazení stavu podrobnosti databáze, výběr **chyby migrace dat** odkaz na horní pás karet nemusí poskytnout další podrobnosti specifické pro selhání migrace.

![chyby migrace dat žádný příklad podrobností](media/known-issues-azure-sql-online/dms-data-migration-errors-no-details.png)

**Řešení**

Chcete-li získat konkrétní podrobnosti o selhání, použijte následující kroky.

1. Zavřete okno podrobného stavu databáze, chcete-li zobrazit obrazovku Aktivity migrace.

     ![obrazovka aktivity migrace](media/known-issues-azure-sql-online/dms-migration-activity-screen.png)

2. Výběrem **možnosti Zobrazit podrobnosti o chybě** zobrazíte konkrétní chybové zprávy, které vám pomohou při řešení chyb migrace.

### <a name="geography-datatype-not-supported-in-sqldb-online-migration"></a>Geografický datový typ není podporován v online migraci SQLDB

**Příznakem**

Migrace se nezdaří s chybovou zprávou obsahující následující text:

     “** encountered a fatal error”, "errorEvents":<Table>.<Column> is of type 'GEOGRAPHY', which is not supported by 'Full Load' under 'Full LOB' support mode."

**Řešení**

Zatímco služba Migrace databáze Azure podporuje datový typ Geography pro offline migrace do Azure SQL Database, pro migrace online není datový typ Geography podporován. Zkuste alternativní metody změnit datový typ u zdroje na podporovaný typ před pokusem o použití služby Migrace databáze Azure pro online migraci této databáze.

### <a name="supported-editions"></a>Podporované edice

**Příznakem**

Migrace se nezdaří s chybovou zprávou obsahující následující text:

    Migration settings validation error: The edition of the server [Business Intelligence Edition (64-bit)] does not match the supported edition(s) [Enterprise,Standard,Developer].

**Řešení**

Podpora online migrací do Azure SQL Database pomocí služby Migrace databáze Azure se vztahuje jenom na edice Enterprise, Standard a Developer. Před zahájením procesu migrace se ujistěte, že používáte podporovanou edici.
