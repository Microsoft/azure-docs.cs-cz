---
title: 'Známé problémy: Online migrace z PostgreSQL do Azure Database for PostgreSQL'
titleSuffix: Azure Database Migration Service
description: Přečtěte si o známých problémech a omezeních migrace při online migracích z PostgreSQL na Azure Database for PostgreSQL – jeden server pomocí Azure Database Migration Service.
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
ms.date: 10/27/2019
ms.openlocfilehash: c5c0015c5034dd3b30b716264fd97e9881b3fe67
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75437860"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-postgresql-to-azure-db-for-postgresql-single-server"></a>Známé problémy/omezení migrace pro online migrace z PostgreSQL do Azure DB pro PostgreSQL-Single server

Známé problémy a omezení související s online migracemi z PostgreSQL do Azure Database for PostgreSQL-Single server jsou popsány v následujících částech.

## <a name="online-migration-configuration"></a>Konfigurace online migrace

- Na zdrojovém serveru PostgreSQL musí běžet verze 9.5.11, 10,3 9.6.7 nebo novější. Další informace najdete v článku [podporované verze databáze PostgreSQL](../postgresql/concepts-supported-versions.md).
- Podporovány jsou pouze stejné migrace verzí. Například migrace PostgreSQL 9.5.11 na Azure Database for PostgreSQL 9.6.7 není podporována.

    > [!NOTE]
    > Pro PostgreSQL verze 10 v současné době DMS podporuje pouze migraci verze 10,3 na Azure Database for PostgreSQL. Plánujeme, aby se už brzy podporovaly novější verze PostgreSQL.

- Pokud chcete povolit logickou replikaci ve **zdrojovém souboru PostgreSQL PostgreSQL. conf** , nastavte následující parametry:
  - **wal_level** = logická
  - **max_replication_slots** = [maximální počet databází pro migraci]; Pokud chcete migrovat čtyři databáze, nastavte tuto hodnotu na 4.
  - **max_wal_senders** = [počet databází, které jsou spuštěny souběžně]; Doporučená hodnota je 10.
- Přidejte IP adresu agenta DMS do zdrojového PostgreSQL pg_hba. conf
  1. Po dokončení zřizování instance DMS si poznamenejte IP adresu DMS.
  2. Přidejte IP adresu do souboru pg_hba. conf, jak je znázorněno na následujícím obrázku:

        hostovat všechny 172.16.136.18y hostitele MD5 pro replikaci Postgres 172.16.136.18/10

- Uživatel musí mít oprávnění superuživatele na serveru, který je hostitelem zdrojové databáze.
- Kromě toho, že se má výčet ve schématu zdrojové databáze vyhodnotit, se musí shodovat schémata zdrojové a cílové databáze.
- Schéma v cílovém Azure Database for PostgreSQL-jednom serveru nesmí mít cizí klíče. K vyřazení cizích klíčů použijte následující dotaz:

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

- Schéma v cílovém Azure Database for PostgreSQL – jeden server nesmí obsahovat žádné triggery. K zakázání triggerů v cílové databázi použijte následující postup:

     ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
     ```

## <a name="datatype-limitations"></a>Omezení datového typu

- **Omezení**: Pokud ve zdrojové databázi PostgreSQL existuje datový typ Enum, migrace během nepřetržité synchronizace selže.

    **Alternativní řešení**: Změňte typ výčtu enum na znak proměnlivý v Azure Database for PostgreSQL.

- **Omezení**: Pokud v tabulkách není žádný primární klíč, průběžná synchronizace se nezdaří.

    **Alternativní řešení**: dočasně nastavte primární klíč pro tabulku, aby migrace pokračovala. Po dokončení migrace dat můžete primární klíč odebrat.

- **Omezení**: datový typ JSONB není pro migraci podporován.

## <a name="lob-limitations"></a>Omezení LOB

Sloupce Large Object (LOB) jsou sloupce, které mohou dosáhnout většího množství. Pro PostgreSQL příklady datových typů LOB zahrnuje XML, JSON, IMAGE, TEXT atd.

- **Omezení**: Pokud jsou datové typy LOB používány jako primární klíče, migrace se nezdaří.

    **Alternativní řešení**: Nahraďte primární klíč jinými typy nebo sloupci, které nejsou typu LOB.

- **Omezení**: Pokud je délka sloupce large object (LOB) větší než 32 KB, data můžou být v cíli zkrácená. Můžete kontrolovat délku sloupce LOB pomocí tohoto dotazu:

    ```
    SELECT max(length(cast(body as text))) as body FROM customer_mail
    ```

    **Alternativní řešení**: Pokud máte objekt LOB, který je větší než 32 KB, kontaktujte technický tým na [vyžádání migrace databáze Azure](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

- **Omezení**: Pokud jsou v tabulce sloupce LOB a pro tabulku není nastaven žádný primární klíč, data nemusí být pro tuto tabulku migrována.

    **Alternativní řešení**: dočasně nastavte primární klíč pro tabulku, aby bylo možné pokračovat v migraci. Po dokončení migrace dat můžete primární klíč odebrat.

## <a name="postgresql10-workaround"></a>PostgreSQL10 řešení

PostgreSQL 10. x provede různé změny názvů složek pg_xlog, což způsobí, že migrace neběží podle očekávání. Pokud migrujete z PostgreSQL 10. x na Azure Database for PostgreSQL 10,3, spusťte následující skript na zdrojové databázi PostgreSQL a vytvořte funkci obálky kolem pg_xlog funkcí.

```
BEGIN;
CREATE SCHEMA IF NOT EXISTS fnRenames;
CREATE OR REPLACE FUNCTION fnRenames.pg_switch_xlog() RETURNS pg_lsn AS $$ 
   SELECT pg_switch_wal(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlog_replay_pause() RETURNS VOID AS $$ 
   SELECT pg_wal_replay_pause(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlog_replay_resume() RETURNS VOID AS $$ 
   SELECT pg_wal_replay_resume(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_current_xlog_location() RETURNS pg_lsn AS $$ 
   SELECT pg_current_wal_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_is_xlog_replay_paused() RETURNS boolean AS $$ 
   SELECT pg_is_wal_replay_paused(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlogfile_name(lsn pg_lsn) RETURNS TEXT AS $$ 
   SELECT pg_walfile_name(lsn); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_last_xlog_replay_location() RETURNS pg_lsn AS $$ 
   SELECT pg_last_wal_replay_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_last_xlog_receive_location() RETURNS pg_lsn AS $$ 
   SELECT pg_last_wal_receive_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_current_xlog_flush_location() RETURNS pg_lsn AS $$ 
   SELECT pg_current_wal_flush_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_current_xlog_insert_location() RETURNS pg_lsn AS $$ 
   SELECT pg_current_wal_insert_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlog_location_diff(lsn1 pg_lsn, lsn2 pg_lsn) RETURNS NUMERIC AS $$ 
   SELECT pg_wal_lsn_diff(lsn1, lsn2); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlogfile_name_offset(lsn pg_lsn, OUT TEXT, OUT INTEGER) AS $$ 
   SELECT pg_walfile_name_offset(lsn); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_create_logical_replication_slot(slot_name name, plugin name, 
   temporary BOOLEAN DEFAULT FALSE, OUT slot_name name, OUT xlog_position pg_lsn) RETURNS RECORD AS $$ 
   SELECT slot_name::NAME, lsn::pg_lsn FROM pg_catalog.pg_create_logical_replication_slot(slot_name, plugin, 
   temporary); $$ LANGUAGE SQL;
ALTER USER PG_User SET search_path = fnRenames, pg_catalog, "$user", public;

-- DROP SCHEMA fnRenames CASCADE;
-- ALTER USER PG_User SET search_path TO DEFAULT;
COMMIT;
```

  > [!NOTE]
  > V předchozím skriptu "PG_User" odkazuje na uživatelské jméno používané pro připojení ke zdroji migrace.

## <a name="limitations-when-migrating-online-from-aws-rds-postgresql"></a>Omezení při migraci online z AWS VP PostgreSQL

Při pokusu o provedení online migrace z AWS VP PostgreSQL pro Azure Database for PostgreSQL se může vyskytnout následující chyby.

- **Chyba**: výchozí hodnota sloupce {Column} v tabulce {table} v databázi {Database} je odlišná na zdrojovém a cílovém serveru. Hodnota na zdrojovém serveru: {value on source}. Hodnota na cílovém serveru: {value on target}.

  **Omezení**: k této chybě dochází, pokud je výchozí hodnota ve schématu sloupce odlišná mezi zdrojovou a cílovou databází.
  **Alternativní řešení**: Zajistěte, aby schéma na cíli odpovídalo schématu na zdroji. Podrobnosti o migraci schématu najdete v [online dokumentaci k migraci pro Azure PostgreSQL](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema).

- **Chyba**: v cílové databázi {Database} je {Number of Tables} tabulek, ve kterých má zdrojová databáze {Database} tabulky {Number of Tables}. Počet tabulek ve zdrojové i cílové databázi musí být stejný.

  **Omezení**: k této chybě dochází, pokud se počet tabulek liší od zdrojové a cílové databáze.
  **Alternativní řešení**: Zajistěte, aby schéma na cíli odpovídalo schématu na zdroji. Podrobnosti o migraci schématu najdete v [online dokumentaci k migraci pro Azure PostgreSQL](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema).

- **Chyba:** Zdrojová databáze {Database} je prázdná.

  **Omezení**: k této chybě dochází, když je zdrojová databáze prázdná. Nejčastější příčinou je výběr nesprávné zdrojové databáze.
  **Alternativní řešení**: poklikejte na zdrojovou databázi, kterou jste vybrali pro migraci, a pak to zkuste znovu.

- **Chyba:** Cílová databáze {Database} je prázdná. Proveďte migraci schématu.

  **Omezení**: k této chybě dochází, pokud není v cílové databázi žádné schéma. Ujistěte se, že schéma na cíli odpovídá schématu na zdroji.
  **Alternativní řešení**: Zajistěte, aby schéma na cíli odpovídalo schématu na zdroji. Podrobnosti o migraci schématu najdete v [online dokumentaci k migraci pro Azure PostgreSQL](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema).

## <a name="other-limitations"></a>Další omezení

- Název databáze nesmí obsahovat středník (;).
- Řetězec hesla, který má levou a pravou složenou závorku {}, se nepodporuje. Toto omezení platí pro připojení ke zdrojovému PostgreSQL a cílovým Azure Database for PostgreSQL.
- Zachycená tabulka musí mít primární klíč. Pokud tabulka nemá primární klíč, výsledek operace odstranění a aktualizace záznamů nebude možné předpovědět.
- Aktualizace segmentu primárního klíče se ignoruje. V takových případech bude použití takové aktualizace identifikována cílem jako aktualizace, která neaktualizovala žádné řádky, a výsledkem bude záznam zapsaný do tabulky výjimky.
- Migrace více tabulek se stejným názvem, ale jiným případem (například Tabulka1, Tabulka1 a Tabulka1), může způsobit nepředvídatelné chování a není proto podporována.
- Změnit zpracování [vytvořit | ZMĚNIT | DROP] tabulka DDLs je podporována, pokud nejsou držena v bloku těla funkce/procedury nebo v jiných vnořených konstrukcích. Například následující změna nebude zachycena:

    ```
    CREATE OR REPLACE FUNCTION pg.create_distributors1() RETURNS void
    LANGUAGE plpgsql
    AS $$
    BEGIN
    create table pg.distributors1(did serial PRIMARY KEY,name varchar(40)
    NOT NULL);
    END;
    $$;
    ```

- Změny zpracování (průběžná synchronizace) ZKRÁCENých operací se nepodporují. Migrace dělených tabulek není podporována. Při zjištění dělené tabulky dojde k následujícím akcím:

  - Databáze oznámí seznam nadřazených a podřízených tabulek.
  - Tabulka se vytvoří v cíli jako běžná tabulka se stejnými vlastnostmi, jako mají vybrané tabulky.
  - Pokud má nadřazená tabulka ve zdrojové databázi stejnou hodnotu primárního klíče jako jeho podřízené tabulky, bude vygenerována chyba "duplicitní klíč".

- V DMS je limit databází pro migraci v jedné aktivitě migrace čtyři.
