---
title: 'Známé problémy: Online migrace z PostgreSQL do databáze Azure pro PostgreSQL'
titleSuffix: Azure Database Migration Service
description: Zjistěte o známých problémech a omezeních migrace s online migrací z PostgreSQL do Azure Database for PostgreSQL pomocí služby Migrace databáze Azure.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom:
- seo-lt-2019
- seo-dt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 3d1bc627ccb8814ab2dfb61fb0653ef0ac644038
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235267"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-postgresql-to-azure-db-for-postgresql"></a>Známé problémy/omezení migrace s online migrací z PostgreSQL do Azure DB pro PostgreSQL

Známé problémy a omezení související s online migrací z PostgreSQL do Azure Database for PostgreSQL jsou popsány v následujících částech.

## <a name="online-migration-configuration"></a>Konfigurace migrace online

- Zdrojový server PostgreSQL musí být spuštěn verze 9.4, 9.5, 9.6, 10 nebo 11. Další informace naleznete v článku [Podporované verze databáze PostgreSQL](../postgresql/concepts-supported-versions.md).
- Podporovány jsou pouze migrace na stejnou nebo vyšší verzi. Například migrace PostgreSQL 9.5 do databáze Azure pro PostgreSQL 9.6 nebo 10 je podporovaná, ale migrace z PostgreSQL 11 na PostgreSQL 9.6 není podporována.
- Chcete-li povolit logickou replikaci ve zdrojovém souboru **PostgreSQL postgresql.conf,** nastavte následující parametry:
  - **wal_level** = logické
  - **max_replication_slots** = [alespoň maximální počet databází pro migraci]; Pokud chcete migrovat čtyři databáze, nastavte hodnotu alespoň na 4.
  - **max_wal_senders** = [počet současně spuštěných databází]; doporučená hodnota je 10
- Přidat IP agenta DMS do zdroje PostgreSQL pg_hba.conf
  1. Poznamenejte si IP adresu DMS po dokončení zřizování instance služby Migrace databáze Azure.
  2. Přidejte ip adresu do souboru pg_hba.conf, jak je znázorněno na obrázku:

      ```
          host  all     172.16.136.18/10    md5
          host  replication postgres    172.16.136.18/10    md5
      ```

- Uživatel musí mít roli REPLIKACE na serveru hostujícím zdrojovou databázi.
- Schémata zdrojové a cílové databáze se musí shodovat.
- Schéma v cílové databázi Azure pro server PostgreSQL-Single nesmí mít cizí klíče. Pomocí následujícího dotazu přetáhněte cizí klíče:

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

- Schéma v cílové databázi Azure pro server PostgreSQL-Single nesmí obsahovat žádné aktivační události. K zakázání aktivačních událostí v cílové databázi použijte následující:

     ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
     ```

## <a name="datatype-limitations"></a>Omezení datových typů

  **Omezení**: Pokud v tabulkách není žádný primární klíč, změny nemusí být synchronizovány s cílovou databází.

  **Řešení:** Dočasně nastavte primární klíč pro pokračování migrace v tabulce. Po dokončení migrace dat můžete primární klíč odebrat.

## <a name="limitations-when-migrating-online-from-aws-rds-postgresql"></a>Omezení při migraci online z AWS RDS PostgreSQL

Při pokusu o provedení online migrace z AWS RDS PostgreSQL do databáze Azure pro PostgreSQL, může dojít k následujícím chybám.

- **Chyba:** Výchozí hodnota sloupce {sloupec} v tabulce {table} v databázi {database} se na zdrojových a cílových serverech liší. Hodnota na zdrojovém serveru: {value on source}. Hodnota na cílovém serveru: {value on target}.

  **Omezení**: K této chybě dochází, když se výchozí hodnota schématu sloupců liší mezi zdrojovou a cílovou databází.
  **Řešení**: Ujistěte se, že schéma na cíl odpovídá schématu na zdroj. Podrobnosti o migraci schématu, naleznete v [dokumentaci k migraci Azure PostgreSQL online](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema)migrace .

- **Chyba**: Cílová databáze {database}' obsahuje tabulky {number of tables}, kde jako zdrojová databáze {database}' je tabulka {number of tables}. Počet tabulek ve zdrojové i cílové databázi musí být stejný.

  **Omezení**: K této chybě dochází, když se počet tabulek liší mezi zdrojovou a cílovou databází.

  **Řešení**: Ujistěte se, že schéma na cíl odpovídá schématu na zdroj. Podrobnosti o migraci schématu, naleznete v [dokumentaci k migraci Azure PostgreSQL online](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema)migrace .

- **Chyba:** Zdrojová databáze {database} je prázdná.

  **Omezení**: K této chybě dochází, když je zdrojová databáze prázdná. Je to s největší pravděpodobností proto, že jste vybrali nesprávnou databázi jako zdroj.

  **Řešení:** Zkontrolujte zdrojovou databázi vybranou pro migraci a akci opakujte.

- **Chyba:** Cílová databáze {database} je prázdná. Proveďte migraci schématu.

  **Omezení**: K této chybě dochází, pokud v cílové databázi není žádné schéma. Ujistěte se, že schéma na cílové odpovídá schéma na zdroj.
  **Řešení**: Ujistěte se, že schéma na cíl odpovídá schématu na zdroj. Podrobnosti o migraci schématu, naleznete v [dokumentaci k migraci Azure PostgreSQL online](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema)migrace .

## <a name="other-limitations"></a>Další omezení

- Název databáze nemůže obsahovat středník (;).
- Zachycená tabulka musí mít primární klíč. Pokud tabulka nemá primární klíč, výsledek operace záznamu DELETE a UPDATE bude nepředvídatelný.
- Aktualizace segmentu primárního klíče je ignorována. V takových případech bude použití takové aktualizace označeno cílem jako aktualizace, která neaktualizovala žádné řádky a bude mít za následek záznam zapsaný do tabulky výjimek.
- Migrace více tabulek se stejným názvem, ale jiný případ (např. tabulka1, TABLE1 a Tabulka1) může způsobit nepředvídatelné chování a proto není podporována.
- Změnit zpracování [CREATE | ZMĚNIT | DROP | Zkrátit] ddl tabulky není podporováno.
- Ve službě Migrace databáze Azure může jedna aktivita migrace pojmout až čtyři databáze.
