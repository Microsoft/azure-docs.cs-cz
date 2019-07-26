---
title: Článek o známých problémech nebo omezeních migrace pro online migrace do Azure SQL Database | Microsoft Docs
description: Přečtěte si o známých problémech nebo omezeních migrace pro online migrace do Azure SQL Database.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 07/23/2019
ms.openlocfilehash: b95e37b4782920f25a16f0750211555d0bef1207
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/22/2019
ms.locfileid: "68383842"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-db"></a>Známé problémy/omezení migrace pro online migrace do Azure SQL DB

Známé problémy a omezení související s online migracemi z SQL Server na Azure SQL Database jsou popsány níže.

> [!IMPORTANT]
> V případě online migrací SQL Server k Azure SQL Database migrace datových typů SQL_variant se nepodporuje.

### <a name="migration-of-temporal-tables-not-supported"></a>Migrace dočasná tabulky není podporovaná.

**Symptom**

Pokud se zdrojová databáze skládá z jedné nebo několika dočasných tabulek, během operace úplného načtení dat se vaše migrace databáze nezdařila a může se zobrazit následující zpráva:

```
{ "resourceId":"/subscriptions/<subscription id>/resourceGroups/migrateready/providers/Microsoft.DataMigration/services/<DMS Service name>", "errorType":"Database migration error", "errorEvents":"["Capture functionalities could not be set. RetCode: SQL_ERROR SqlState: 42000 NativeError: 13570 Message: [Microsoft][SQL Server Native Client 11.0][SQL Server]The use of replication is not supported with system-versioned temporal table '[Application. Cities]' Line: 1 Column: -1 "]" }
```

 ![Příklad chyb v dočasné tabulce](media/known-issues-azure-sql-online/dms-temporal-tables-errors.png)

**Alternativní řešení**

1. Pomocí níže uvedeného dotazu Najděte dočasné tabulky ve zdrojovém schématu.
     ``` 
     select name,temporal_type,temporal_type_desc,* from sys.tables where temporal_type <>0
     ```
2. Tyto tabulky vylučte z okna **Konfigurovat nastavení migrace** , na kterých zadáte tabulky pro migraci.

3. Spusťte znovu aktivitu migrace.

**Prostředky**

Další informace najdete v článku [dočasné tabulky](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables?view=sql-server-2017).
 
### <a name="migration-of-tables-includes-one-or-more-columns-with-the-hierarchyid-data-type"></a>Migrace tabulek obsahuje jeden nebo více sloupců s datovým typem hierarchyid

**Symptom**

Při operaci úplného načtení dat se může zobrazit výjimka SQL naznačující, že "ntext není kompatibilní s hierarchyid":
     
![Příklad chyb hierarchyid](media/known-issues-azure-sql-online/dms-hierarchyid-errors.png)

**Alternativní řešení**

1. Pomocí dotazu níže vyhledejte tabulky uživatelů, které obsahují sloupce s datovým typem hierarchyid.

      ``` 
      select object_name(object_id) 'Table name' from sys.columns where system_type_id =240 and object_id in (select object_id from sys.objects where type='U')
      ``` 

2. Tyto tabulky vylučte z okna **Konfigurovat nastavení migrace** , na kterých zadáte tabulky pro migraci.

3. Spusťte znovu aktivitu migrace.

### <a name="migration-failures-with-various-integrity-violations-with-active-triggers-in-the-schema-during-full-data-load-or-incremental-data-sync"></a>Selhání migrace s různými narušeními integrity s aktivními aktivačními událostmi ve schématu během úplného načtení dat nebo přírůstkové synchronizace dat

**Alternativní řešení**

1. Vyhledá aktivační události, které jsou aktuálně aktivní ve zdrojové databázi, pomocí následujícího dotazu:

     ```
     select * from sys.triggers where is_disabled =0
     ```

2. Pomocí kroků uvedených v článku [zakázání triggeru (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017)zakažte aktivační procedury ve zdrojové databázi.

3. Spusťte znovu aktivitu migrace.

### <a name="support-for-lob-data-types"></a>Podpora datových typů LOB

**Symptom**

Pokud je délka sloupce Large Object (LOB) větší než 32 KB, může se stát, že se data v cíli zkrátí. Délku sloupce LOB můžete ověřit pomocí následujícího dotazu: 

``` 
SELECT max(DATALENGTH(ColumnName)) as LEN from TableName
```

**Alternativní řešení**

Pokud máte sloupec LOB, který je větší než 32 KB, obraťte se na technický tým na [vyžádání migrace databáze Azure](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

### <a name="issues-with-timestamp-columns"></a>Problémy se sloupci časových razítek

**Symptom**

DMS nemigruje zdrojovou hodnotu časového razítka; místo toho DMS vygeneruje novou hodnotu časového razítka v cílové tabulce.

**Alternativní řešení**

Pokud potřebujete DMS, abyste mohli migrovat přesnou hodnotu časového razítka uloženou ve zdrojové tabulce, obraťte se na technický tým na stránce s dotazem na [migrace databáze Azure](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

### <a name="data-migration-errors-dont-provide-additional-details-on-the-database-detailed-status-blade"></a>Chyby migrace dat neposkytují další podrobnosti v okně podrobný stav databáze.

**Symptom**

Pokud dojde k selhání migrace v zobrazení stav databáze podrobnosti, výběr odkazu **chyby migrace dat** na horním pásu karet nemusí poskytnout další podrobnosti, které jsou specifické pro selhání migrace.

![chyby migrace dat – příklad bez podrobností](media/known-issues-azure-sql-online/dms-data-migration-errors-no-details.png)

**Alternativní řešení**

Pokud se chcete dostat k určitým podrobnostem o selhání, postupujte podle následujících kroků.

1. Zavřete okno podrobný stav databáze a zobrazte tak obrazovku aktivity migrace.

     ![obrazovka aktivity migrace](media/known-issues-azure-sql-online/dms-migration-activity-screen.png)

2. Pokud chcete zobrazit konkrétní chybové zprávy, které vám pomůžou vyřešit chyby migrace, vyberte **Zobrazit podrobnosti o chybě** .

### <a name="geography-datatype-not-supported-in-sqldb-online-migration"></a>Zeměpisná vlastnost DataType není v online migraci SQLDB podporovaná.

**Symptom**

Migrace se nezdařila s chybovou zprávou obsahující následující text:

 "* * došlo k závažné chybě", "errorEvents":<Table>.<Column> je typu "GEOGRAFie", který není podporován úplným zatížením v režimu podpory Full LOB. "

**Alternativní řešení** I když Azure Database Migration Service podporuje zeměpisný datový typ pro offline migrace do Azure SQL Database pro online migrace, geografický datový typ se nepodporuje. Než se pokusíte použít Azure Database Migration Service pro online migraci této databáze, zkuste změnit datový typ ve zdroji na podporovaný typ. 
