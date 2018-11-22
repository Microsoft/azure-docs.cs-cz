---
title: Článek o omezeních známé problémy a migrace s online migrace do služby Azure Database for MySQL | Dokumentace Microsoftu
description: Přečtěte si o známých problémech a migrace omezení online migrace do služby Azure Database for MySQL.
services: database-migration
author: HJToland3
ms.author: scphang
manager: ''
ms.reviewer: ''
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 09/22/2018
ms.openlocfilehash: b83c889e72acb320c308c3ad5ee6243e715fd523
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2018
ms.locfileid: "52282872"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-db-for-postgresql"></a>Známé problémy a migrace omezení online migrace do Azure DB for PostgreSQL

Známé problémy a omezení související s online migraci z PostgreSQL do služby Azure Database for PostgreSQL jsou popsány v následujících částech. 

## <a name="online-migration-configuration"></a>Online migrace konfigurace
- Zdrojový PostgreSQL Server musí běžet verze 9.5.11, 9.6.7 nebo 10.3 nebo novější. Další informace najdete v článku [podporované verze databáze PostgreSQL](../postgresql/concepts-supported-versions.md).
- Jsou podporovány pouze stejné verze migrace. Například migrace PostgreSQL 9.5.11 ke službě Azure Database for PostgreSQL 9.6.7 nepodporuje.
- Povolení logické replikace v **source PostgreSQL postgresql.conf** souborů, nastavit následující parametry:
    - **wal_level** = logické
    - **max_replication_slots** = [maximální počet databází pro migraci]; Pokud chcete migrovat databáze s 4, nastavte hodnotu na 4
    - **max_wal_senders** = [počet databází, které jsou spuštěny souběžně]; Doporučená hodnota je 10
- Přidat IP adresu agenta DMS do pg_hba.conf PostgresSQL zdroje
    1. Poznamenejte DMS IP adresu, po dokončení zřizování instance DMS.
    2. Přidáte IP adresu do souboru pg_hba.conf, jak je znázorněno:

        hostování všech 172.16.136.18/10 md5 hostitele replikace postgres 172.16.136.18/10 md5

- Uživatel musí mít oprávnění superuživatele na serveru, který hostuje zdrojové databáze
- Kromě nutnosti VÝČTU ve schématu zdrojové databáze, musí odpovídat zdrojovými a cílovými schématy databáze.
- Cizí klíče nesmí mít schéma v cílovou službu Azure Database for PostgreSQL. Vyřaďte cizí klíče pomocí následujícího dotazu:

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

- Schéma v cílovou službu Azure Database for PostgreSQL nesmí obsahovat žádné aktivační události. Pomocí následujícího postupu zakázat aktivační události v cílové databázi:

     ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
     ```

## <a name="datatype-limitations"></a>Datový typ omezení

- **Omezení**: Pokud je ve zdrojové databázi PostgreSQL datový typ VÝČTU, migrace selže během průběžné synchronizace.

    **Alternativní řešení**: datový typ změnit VÝČTU znak různé ve službě Azure Database for PostgreSQL.

- **Omezení**: Pokud neexistuje žádný primární klíč v tabulkách, průběžné synchronizace se nezdaří.

    **Alternativní řešení**: dočasně nastavit primární klíč pro tabulku pro migraci, aby bylo možné pokračovat. Po dokončení migrace dat, můžete odebrat primární klíč.

## <a name="lob-limitations"></a>Omezení LOB
Sloupce velkého objektu (LOB) jsou sloupce, které můžou růst velké. Pro PostgreSQL typů dat LOB příklady XML, JSON, OBRÁZKŮ, textu, atd.

- **Omezení**: Pokud LOB datové typy se používají jako primární klíče, migrace selže.

    **Alternativní řešení**: nahradit primární klíč s jinými typy nebo sloupce, které nejsou LOB.

- **Omezení**: Pokud sloupec velkého objektu (LOB) je větší než 32 KB, může data zkrácen na cíl. Délka sloupce LOB pomocí tohoto dotazu, můžete zkontrolovat:

    ```
    SELECT max(length(cast(body as text))) as body FROM customer_mail
    ```

    **Alternativní řešení**: Pokud budete mít obchodní objekt, který je větší než 32 KB, obraťte se na technický tým na adrese [ dmsfeedback@microsoft.com ](mailto:dmsfeedback@microsoft.com).

- **Omezení**: Pokud existuje obchodní sloupců v tabulce a není nastaven primární klíč pro tabulku, nemusí být data migrována pro tuto tabulku.

    **Alternativní řešení**: dočasně nastavit primární klíč pro tabulku pro migraci, aby bylo možné pokračovat. Po dokončení migrace dat, můžete odebrat primární klíč.

## <a name="postgresql10-workaround"></a>Alternativní řešení PostgreSQL10
PostgreSQL 10.x změní různé názvy složek pg_xlog a proto způsobí migrace neběží podle očekávání. Pokud migrujete z PostgreSQL 10.x k Azure Database for PostgreSQL 10.3, spusťte následující skript ve zdrojové databázi PostgreSQL vytvoříte funkci obálku kolem pg_xlog funkce.

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

## <a name="other-limitations"></a>Další omezení
- Název databáze nesmí obsahovat středníkem (;).
- Řetězec hesla, která má otevírací a uzavírací složené závorky {} není podporován. Toto omezení platí pro obě připojení k PostgreSQL zdroj a cíl – Azure Database for PostgreSQL.
- Zachycené tabulka musí obsahovat primární klíč. Pokud tabulka nemá primární klíč, bude výsledek operace odstranění a aktualizace záznamu nepředvídatelné.
- Aktualizuje se segment primárního klíče se ignoruje. V takových případech použití takové aktualizace budou označeny v cíli jako aktualizace, která nejsou aktualizovány žádné řádky a bude mít za následek záznam zapisují do tabulky výjimky.
- Migrace z více tabulek se stejným názvem, ale jiné případu (například Tabulka1, Tabulka1 a Tabulka1) může způsobit nepředvídatelné chování a není proto podporována.
- Změnit zpracování [vytvořit | PŘÍKAZ ALTER | PŘETAŽENÍ] tabulky DDLs jsou podporováno, pokud se ukládají v těle bloku vnitřní funkci či proceduru nebo v jiných vnořených objektů. Například následující změnu nebude možné zaznamenat:

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

- Zpracování změny (Průběžná synchronizace) TRUNCATE operací není podporováno. Migrace dělené tabulky není podporována. Když se zjistí dělenou tabulku, dojde k následující věci:
    - Databáze budou hlásit seznam nadřazené a podřízené tabulky.
    - V tabulce se vytvoří v cílové jako o běžnou tabulku s stejné vlastnosti jako vybrané tabulky.
    - Pokud nadřazená tabulka v databázi správy zdrojových má stejnou hodnotu primárního klíče jako jeho podřízených tabulek, vygeneruje se chyba "duplicitní klíč".
- Limit databáze k migraci v aktivitě migrací za jeden v DMS, jsou čtyři.