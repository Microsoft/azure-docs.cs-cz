---
title: 'Známé problémy: Online migrace do SQL Database'
titleSuffix: Azure Database Migration Service
description: Přečtěte si o známých problémech nebo omezeních migrace s online migracemi k Azure SQL Database používání Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: 748c3f8e38b98b2cbdcfecdf7d755827230fdb3d
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94962463"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-database"></a>Známé problémy/omezení migrace pro online migrace Azure SQL Database

Známé problémy a omezení související s online migracemi z SQL Server na Azure SQL Database jsou popsány níže.

> [!IMPORTANT]
> S online migracemi SQL Server Azure SQL Database není podporována migrace datových typů SQL_variant.

### <a name="migration-of-temporal-tables-not-supported"></a>Migrace dočasná tabulky není podporovaná.

**Příznaky**

Pokud se zdrojová databáze skládá z jedné nebo několika dočasných tabulek, během operace úplného načtení dat se vaše migrace databáze nezdařila a může se zobrazit následující zpráva:

```
{ "resourceId":"/subscriptions/<subscription id>/resourceGroups/migrateready/providers/Microsoft.DataMigration/services/<DMS Service name>", "errorType":"Database migration error", "errorEvents":"["Capture functionalities could not be set. RetCode: SQL_ERROR SqlState: 42000 NativeError: 13570 Message: [Microsoft][SQL Server Native Client 11.0][SQL Server]The use of replication is not supported with system-versioned temporal table '[Application. Cities]' Line: 1 Column: -1 "]" }
```

 ![Příklad chyb v dočasné tabulce](media/known-issues-azure-sql-online/dms-temporal-tables-errors.png)

**Alternativní řešení**

Použijte k tomu následující postup.

1. Pomocí níže uvedeného dotazu Najděte dočasné tabulky ve zdrojovém schématu.

     ``` 
     select name,temporal_type,temporal_type_desc,* from sys.tables where temporal_type <>0
     ```

2. Tyto tabulky vylučte z okna **Konfigurovat nastavení migrace** , na kterých zadáte tabulky pro migraci.

3. Spusťte znovu aktivitu migrace.

**Prostředky**

Další informace najdete v článku [dočasné tabulky](/sql/relational-databases/tables/temporal-tables?view=sql-server-2017).

### <a name="migration-of-tables-includes-one-or-more-columns-with-the-hierarchyid-data-type"></a>Migrace tabulek obsahuje jeden nebo více sloupců s datovým typem hierarchyid

**Příznaky**

Při operaci úplného načtení dat se může zobrazit výjimka SQL naznačující, že "ntext není kompatibilní s hierarchyid":

![Příklad chyb hierarchyid](media/known-issues-azure-sql-online/dms-hierarchyid-errors.png)

**Alternativní řešení**

Použijte k tomu následující postup.

1. Pomocí dotazu níže vyhledejte tabulky uživatelů, které obsahují sloupce s datovým typem hierarchyid.

      ``` 
      select object_name(object_id) 'Table name' from sys.columns where system_type_id =240 and object_id in (select object_id from sys.objects where type='U')
      ```

2. Tyto tabulky vylučte z okna **Konfigurovat nastavení migrace** , na kterých zadáte tabulky pro migraci.

3. Spusťte znovu aktivitu migrace.

### <a name="migration-failures-with-various-integrity-violations-with-active-triggers-in-the-schema-during-full-data-load-or-incremental-data-sync"></a>Selhání migrace s různými narušeními integrity s aktivními aktivačními událostmi ve schématu během úplného načtení dat nebo přírůstkové synchronizace dat

**Alternativní řešení**

Použijte k tomu následující postup.

1. Vyhledá aktivační události, které jsou aktuálně aktivní ve zdrojové databázi, pomocí následujícího dotazu:

     ```
     select * from sys.triggers where is_disabled =0
     ```

2. Pomocí kroků uvedených v článku [zakázání triggeru (Transact-SQL)](/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017)zakažte aktivační procedury ve zdrojové databázi.

3. Spusťte znovu aktivitu migrace.

### <a name="support-for-lob-data-types"></a>Podpora datových typů LOB

**Příznaky**

Pokud je délka sloupce Large Object (LOB) větší než 32 KB, může se stát, že se data v cíli zkrátí. Délku sloupce LOB můžete ověřit pomocí následujícího dotazu:

``` 
SELECT max(DATALENGTH(ColumnName)) as LEN from TableName
```

**Alternativní řešení**

Pokud máte sloupec LOB, který je větší než 32 KB, obraťte se na technický tým na [vyžádání migrace databáze Azure](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

### <a name="issues-with-timestamp-columns"></a>Problémy se sloupci časových razítek

**Příznaky**

Azure Database Migration Service nemigruje hodnotu zdrojového časového razítka. místo toho Azure Database Migration Service vygeneruje novou hodnotu časového razítka v cílové tabulce.

**Alternativní řešení**

Pokud potřebujete Azure Database Migration Service migrovat přesnou hodnotu časového razítka uloženou ve zdrojové tabulce, obraťte se na technický tým na [vyžádání migrace databáze Azure](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

### <a name="data-migration-errors-dont-provide-additional-details-on-the-database-detailed-status-blade"></a>Chyby migrace dat neposkytují další podrobnosti v okně podrobný stav databáze.

**Příznaky**

Pokud dojde k selhání migrace v zobrazení stav databáze podrobnosti, výběr odkazu **chyby migrace dat** na horním pásu karet nemusí poskytnout další podrobnosti, které jsou specifické pro selhání migrace.

![chyby migrace dat – příklad bez podrobností](media/known-issues-azure-sql-online/dms-data-migration-errors-no-details.png)

**Alternativní řešení**

Pokud se chcete dostat k určitým podrobnostem o selhání, použijte následující postup.

1. Zavřete okno podrobný stav databáze a zobrazte tak obrazovku aktivity migrace.

     ![obrazovka aktivity migrace](media/known-issues-azure-sql-online/dms-migration-activity-screen.png)

2. Pokud chcete zobrazit konkrétní chybové zprávy, které vám pomůžou vyřešit chyby migrace, vyberte **Zobrazit podrobnosti o chybě** .

### <a name="geography-datatype-not-supported-in-sqldb-online-migration"></a>Zeměpisná vlastnost DataType není v online migraci SQLDB podporovaná.

**Příznaky**

Migrace se nezdařila s chybovou zprávou obsahující následující text:

```output
"** encountered a fatal error", "errorEvents":<Table>.<Column> is of type 'GEOGRAPHY', which is not supported by 'Full Load' under 'Full LOB' support mode.
```

**Alternativní řešení**

I když Azure Database Migration Service podporuje zeměpisný datový typ pro offline migrace do Azure SQL Database pro online migrace, geografický datový typ se nepodporuje. Než se pokusíte použít Azure Database Migration Service pro online migraci této databáze, zkuste změnit datový typ ve zdroji na podporovaný typ.

### <a name="supported-editions"></a>Podporované edice

**Příznaky**

Migrace se nezdařila s chybovou zprávou obsahující následující text:

```output
Migration settings validation error: The edition of the server [Business Intelligence Edition (64-bit)] does not match the supported edition(s) [Enterprise,Standard,Developer].
```

**Alternativní řešení**

Podpora online migrací pro Azure SQL Database používání Azure Database Migration Service se rozšiřuje jenom na edice Enterprise, Standard a Developer. Před zahájením procesu migrace se ujistěte, že používáte podporovanou edici.