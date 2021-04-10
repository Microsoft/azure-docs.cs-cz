---
title: 'Známé problémy: Online migrace z PostgreSQL do Azure Database for PostgreSQL'
titleSuffix: Azure Database Migration Service
description: Přečtěte si o známých problémech a omezeních migrace při online migracích z PostgreSQL k Azure Database for PostgreSQL používání Azure Database Migration Service.
services: database-migration
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom:
- seo-lt-2019
- seo-dt-2019
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: fdefcabdda64402610f115832976ec9f7af81b80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99258825"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-postgresql-to-azure-db-for-postgresql"></a>Známé problémy/omezení migrace pro online migrace z PostgreSQL do Azure DB pro PostgreSQL

Známé problémy a omezení související s online migracemi z PostgreSQL do Azure Database for PostgreSQL jsou popsány v následujících částech.

## <a name="online-migration-configuration"></a>Konfigurace online migrace

- Na zdrojovém serveru PostgreSQL musí běžet verze 9,4, 9,5, 9,6, 10 nebo 11. Další informace najdete v článku [podporované verze databáze PostgreSQL](../postgresql/concepts-supported-versions.md).
- Jsou podporovány pouze migrace do stejné nebo vyšší verze. Například migrace PostgreSQL 9,5 na Azure Database for PostgreSQL 9,6 nebo 10 je podporována, ale migrace z PostgreSQL 11 na PostgreSQL 9,6 není podporována.
- Pokud chcete povolit logickou replikaci ve **zdrojovém souboru PostgreSQL PostgreSQL. conf** , nastavte následující parametry:
  - **wal_level** = logická
  - **max_replication_slots** = [minimálně maximální počet databází pro migraci]; Pokud chcete migrovat čtyři databáze, nastavte hodnotu aspoň na 4.
  - **max_wal_senders** = [počet databází, které jsou spuštěny souběžně]; Doporučená hodnota je 10.
- Přidejte IP adresu agenta DMS do zdrojového PostgreSQL pg_hba. conf
  1. Po dokončení zřizování instance Azure Database Migration Service si poznamenejte IP adresu DMS.
  2. Přidejte IP adresu do souboru pg_hba. conf, jak je znázorněno na následujícím obrázku:

      ```
          host    all    172.16.136.18/10    md5
          host    replication postgres    172.16.136.18/10     md5
      ```

- Uživatel musí mít roli replikace na serveru, který je hostitelem zdrojové databáze.
- Zdrojové a cílové schéma databáze se musí shodovat.
- Schéma v cílovém serveru Azure Database for PostgreSQL-Single nesmí obsahovat cizí klíče. K vyřazení cizích klíčů použijte následující dotaz:

    ```
                  SELECT Queries.tablename
           ,concat('alter table ', Queries.tablename, ' ', STRING_AGG(concat('DROP CONSTRAINT ', Queries.foreignkey), ',')) as DropQuery
                ,concat('alter table ', Queries.tablename, ' ', 
                                                STRING_AGG(concat('ADD CONSTRAINT ', Queries.foreignkey, ' FOREIGN KEY (', column_name, ')', 'REFERENCES ', foreign_table_name, '(', foreign_column_name, ')' ), ',')) as AddQuery
        FROM
        (SELECT
        tc.table_schema, 
        tc.constraint_name as foreignkey, 
        tc.table_name as tableName, 
        kcu.column_name, 
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name 
    FROM 
        information_schema.table_constraints AS tc 
        JOIN information_schema.key_column_usage AS kcu
          ON tc.constraint_name = kcu.constraint_name
          AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu
          ON ccu.constraint_name = tc.constraint_name
          AND ccu.table_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY') Queries
      GROUP BY Queries.tablename;
    
    ```

    Spusťte skript pro odstranění cizího klíče (druhý sloupec) ve výsledku dotazu odstraňte cizí klíč.

- Schéma v cílovém serveru Azure Database for PostgreSQL-Single nesmí obsahovat žádné triggery. K zakázání triggerů v cílové databázi použijte následující postup:

     ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
     ```

## <a name="size-limitations"></a>Omezení velikosti
- Pomocí jedné služby DMS můžete migrovat až 2 TB dat z PostgreSQL do Azure DB pro PostgreSQL.
## <a name="datatype-limitations"></a>Omezení datového typu

  **Omezení**: Pokud v tabulkách není žádný primární klíč, změny se nemusí synchronizovat s cílovou databází.

  **Alternativní řešení**: dočasně nastavte primární klíč pro tabulku, aby migrace pokračovala. Po dokončení migrace dat můžete primární klíč odebrat.

## <a name="limitations-when-migrating-online-from-aws-rds-postgresql"></a>Omezení při migraci online z AWS VP PostgreSQL

Při pokusu o provedení online migrace z AWS VP PostgreSQL pro Azure Database for PostgreSQL se může vyskytnout následující chyby.

- **Chyba:** Výchozí hodnota sloupce {column} v tabulce {table} v databázi {database} se na zdrojovém a cílovém serveru liší. Hodnota na zdrojovém serveru: {value on source}. Hodnota na cílovém serveru: {value on target}.

  **Omezení**: k této chybě dochází, pokud je výchozí hodnota ve schématu sloupce odlišná mezi zdrojovou a cílovou databází.
  **Alternativní řešení**: Zajistěte, aby schéma na cíli odpovídalo schématu na zdroji. Podrobnosti o migraci schématu najdete v [online dokumentaci k migraci pro Azure PostgreSQL](./tutorial-postgresql-azure-postgresql-online.md#migrate-the-sample-schema).

- **Chyba:** Cílová databáze {database} obsahuje {number of tables} tabulek, ale zdrojová databáze {database} obsahuje {number of tables} tabulek. Počet tabulek ve zdrojové i cílové databázi musí být stejný.

  **Omezení**: k této chybě dochází, pokud se počet tabulek liší od zdrojové a cílové databáze.

  **Alternativní řešení**: Zajistěte, aby schéma na cíli odpovídalo schématu na zdroji. Podrobnosti o migraci schématu najdete v [online dokumentaci k migraci pro Azure PostgreSQL](./tutorial-postgresql-azure-postgresql-online.md#migrate-the-sample-schema).

- **Chyba:** Zdrojová databáze {Database} je prázdná.

  **Omezení**: k této chybě dochází, když je zdrojová databáze prázdná. Je to pravděpodobně proto, že jste vybrali špatnou databázi jako zdroj.

  **Alternativní řešení**: poklikejte na zdrojovou databázi, kterou jste vybrali pro migraci, a pak to zkuste znovu.

- **Chyba:** Cílová databáze {Database} je prázdná. Proveďte migraci schématu.

  **Omezení**: k této chybě dochází, pokud není v cílové databázi žádné schéma. Ujistěte se, že schéma na cíli odpovídá schématu na zdroji.
  **Alternativní řešení**: Zajistěte, aby schéma na cíli odpovídalo schématu na zdroji. Podrobnosti o migraci schématu najdete v [online dokumentaci k migraci pro Azure PostgreSQL](./tutorial-postgresql-azure-postgresql-online.md#migrate-the-sample-schema).

## <a name="other-limitations"></a>Další omezení

- Název databáze nesmí obsahovat středník (;).
- Zachycená tabulka musí mít primární klíč. Pokud tabulka nemá primární klíč, výsledek operace odstranění a aktualizace záznamů nebude možné předpovědět.
- Aktualizace segmentu primárního klíče se ignoruje. V takových případech bude použití takové aktualizace identifikována cílem jako aktualizace, která neaktualizovala žádné řádky, a výsledkem bude záznam zapsaný do tabulky výjimky.
- Migrace více tabulek se stejným názvem, ale jiným případem (například Tabulka1, Tabulka1 a Tabulka1), může způsobit nepředvídatelné chování a není proto podporována.
- Změnit zpracování [vytvořit | ZMĚNIT | DROP | ZKRÁCENí] tabulka DDLs není podporována.
- V Azure Database Migration Service může jedna aktivita migrace pojmout pouze až čtyři databáze.
- Migrace pg_largeobject tabulky není podporována. 
