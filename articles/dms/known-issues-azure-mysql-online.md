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
ms.date: 10/09/2018
ms.openlocfilehash: 6e82c10d8e9109279045095c1b856520245a5a6f
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2018
ms.locfileid: "48884506"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-db-for-mysql"></a>Známé problémy a migrace omezení online migrace do Azure DB for MySQL

Známé problémy a omezení související s online migrace z MySQL do služby Azure Database for MySQL jsou popsány v následujících částech. 

## <a name="online-migration-configuration"></a>Online migrace konfigurace
- Zdrojový MySQL Server musí mít verzi 5.6.35, 5.7.18 nebo novější
- Azure Database for MySQL podporuje:
    - MySQL Community Edition
    - Modul InnoDB
- Migrace stejnou verzi. Migrace MySQL 5.6 na Azure Database for MySQL 5.7 není podporována.
- Povolte binární protokolování v souboru my.ini (Windows) nebo my.cnf (Unix)
    - Nastavte na 1, například Server_id Server_id na libovolné číslo větší nebo rovná = 1 (pouze pro MySQL 5.6)
    - Nastavte log-bin = <path> (pouze pro MySQL 5.6)
    - Nastavit binlog_format = řádek
    - Expire_logs_days = 5 (doporučeno – pouze pro MySQL 5.6)
- Uživatel musí mít roli ReplicationAdmin.
- Kolace definované pro zdrojovou databázi MySQL jsou stejné jako ty, které jsou definovány v cíli – Azure Database for MySQL.
- Schéma musí odpovídat mezi zdrojovou databázi MySQL a cílová databáze ve službě Azure Database for MySQL.
- Cizí klíče nesmí mít schéma v cíl – Azure Database for MySQL. Vyřaďte cizí klíče pomocí následujícího dotazu:
    ```
    SET group_concat_max_len = 8192;
    SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery
    FROM
    (SELECT 
    KCU.REFERENCED_TABLE_SCHEMA as SchemaName, KCU.TABLE_NAME, KCU.COLUMN_NAME,
        CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery,
        CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery
        FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC
        WHERE
          KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME
          AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA
      AND KCU.REFERENCED_TABLE_SCHEMA = ['schema_name']) Queries
      GROUP BY SchemaName;
    ```

    Ve výsledku dotazu. Spusťte rozevírací cizí klíč (což je druhý sloupec).
- Schéma v cíl – Azure Database for MySQL nesmí obsahovat žádné aktivační události. K odpojení aktivačních událostí v cílové databázi:
    ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
    ```

## <a name="datatype-limitations"></a>Datový typ omezení
- **Omezení**: Pokud je datový typ JSON ve zdrojové databázi MySQL, migrace selže během průběžné synchronizace.

    **Alternativní řešení**: datový typ upravit JSON na střední text nebo longtext ve zdrojové databázi MySQL.

- **Omezení**: Pokud neexistuje žádný primární klíč v tabulkách, průběžné synchronizace se nezdaří.
 
    **Alternativní řešení**: dočasně nastavit primární klíč pro tabulku pro migraci, abyste mohli pokračovat. Po dokončení migrace dat, můžete odebrat primární klíč.

## <a name="lob-limitations"></a>Omezení LOB
Sloupce velkého objektu (LOB) jsou sloupce, které může rozvíjet velké. Pro MySQL, střední text Longtext, objektů Blob, Mediumblob, Longblob atd. jsou uvedeny některé typy LOB.

- **Omezení**: Pokud LOB datové typy se používají jako primární klíče, migrace selže.

    **Alternativní řešení**: nahradit primární klíč s jinými typy nebo sloupce, které nejsou LOB.

- **Omezení**: Pokud sloupec velkého objektu (LOB) je větší než 32 KB, může data zkrácen na cíl. Délka sloupce LOB pomocí tohoto dotazu, můžete zkontrolovat:
    ```
    SELECT max(length(description)) as LEN from catalog;
    ```

    **Alternativní řešení**: Pokud budete mít obchodní objekt, který je větší než 32 KB, obraťte se na technický tým na adrese [ dmsfeedback@microsoft.com ](mailto:dmsfeedback@microsoft.com). 

## <a name="other-limitations"></a>Další omezení
- Řetězec hesla, která má otevírací a uzavírací složené závorky {} na začátku a konce řetězce hesla se nepodporuje. Toto omezení platí pro obě připojení k MySQL zdroj a cíl – Azure Database for MySQL.
- Následující DDLs nejsou podporovány:
    - Všechny oddílu DDLs
    - Odstranit tabulku
    - Přejmenovat tabulku
- Pomocí *příkaz alter table < table_name > Přidat sloupec < Název_sloupce >* příkaz Přidat sloupce na začátku nebo uprostřed tabulky není podporován. *Příkaz alter table < table_name > Přidat sloupec < Název_sloupce >* přidá sloupec na konec tabulky.
- Indexy vytvořené na pouze část dat sloupce nejsou podporovány. Následující příkaz je příklad, který vytvoří index pomocí pouze část dat sloupce:
    ``` 
    CREATE INDEX partial_name ON customer (name(10));
    ```

- Limit databáze k migraci v aktivitě migrací za jeden v DMS, jsou čtyři.
