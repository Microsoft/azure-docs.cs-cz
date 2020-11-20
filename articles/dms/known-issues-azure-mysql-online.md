---
title: 'Známé problémy: Online migrace do Azure Database for MySQL'
titleSuffix: Azure Database Migration Service
description: Přečtěte si o známých problémech a omezeních migrace pro online migrace do Azure Database for MySQL při použití Azure Database Migration Service.
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
ms.openlocfilehash: a9ac4830d11aa3360a272ac1feb167eb20c26c9a
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94962616"
---
# <a name="online-migration-issues--limitations-to-azure-db-for-mysql-with-azure-database-migration-service"></a>Problémy online migrace & omezení pro Azure DB for MySQL s Azure Database Migration Service

Známé problémy a omezení související s online migracemi z MySQL na Azure Database for MySQL jsou popsány v následujících částech.

## <a name="online-migration-configuration"></a>Konfigurace online migrace


- Zdrojový server MySQL verze musí být verze 5.6.35, 5.7.18 nebo novější.
- Azure Database for MySQL podporuje:
  - MySQL Community Edition
  - Modul InnoDB
- Migrace stejné verze. Migrace MySQL 5,6 na Azure Database for MySQL 5,7 není podporována. Migrace do MySQL 8,0 se nepodporují.
- Povolit binární přihlášení my.ini (Windows) nebo My. CNF (UNIX)
  - Nastavte Server_id na libovolné číslo větší nebo rovno 1, například Server_id = 1 (pouze pro MySQL 5,6).
  - Nastavení log-bin = \<path> (jenom pro MySQL 5,6)
  - Nastavit binlog_format = řádek
  - Expire_logs_days = 5 (doporučeno – jenom pro MySQL 5,6)
- Uživatel musí mít roli ReplicationAdmin.
- Kolace definovaná pro zdrojovou databázi MySQL jsou shodná s těmi, která jsou definována v cílovém Azure Database for MySQL.
- Schéma se musí shodovat se zdrojovou databází MySQL a cílovou databází v Azure Database for MySQL.
- Schéma v cílovém Azure Database for MySQL nesmí obsahovat cizí klíče. K vyřazení cizích klíčů použijte následující dotaz:
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

    Spusťte skript pro odstranění cizího klíče (druhý sloupec) ve výsledku dotazu odstraňte cizí klíč.
- Schéma v cílovém Azure Database for MySQL nesmí obsahovat žádné triggery. Zrušení aktivačních událostí v cílové databázi:
    ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
    ```

## <a name="datatype-limitations"></a>Omezení datového typu

- **Omezení**: Pokud ve zdrojové databázi MySQL existuje datový typ JSON, během průběžné synchronizace se migrace nezdaří.

    **Alternativní řešení**: změňte datový typ JSON na střední text nebo LONGTEXT ve zdrojové databázi MySQL.

- **Omezení**: Pokud v tabulkách není žádný primární klíč, průběžná synchronizace se nezdaří.

    **Alternativní řešení**: dočasně nastavte primární klíč pro tabulku, aby migrace pokračovala. Po dokončení migrace dat můžete primární klíč odebrat.

## <a name="lob-limitations"></a>Omezení LOB

Sloupce Large Object (LOB) jsou sloupce, které mohou dosáhnout větší velikosti. Pro MySQL, střední text, LONGTEXT, BLOB, Mediumblob, Longblob atd. jsou některé z typů dat LOB.

- **Omezení**: Pokud jsou datové typy LOB používány jako primární klíče, migrace se nezdaří.

    **Alternativní řešení**: Nahraďte primární klíč jinými typy nebo sloupci, které nejsou typu LOB.

- **Omezení**: Pokud je sloupec délka large object (LOB) větší než parametr omezení velikosti LOB (by neměl být větší než 64 KB), může dojít ke zkrácení dat v cíli. Můžete kontrolovat délku sloupce LOB pomocí tohoto dotazu:
    ```
    SELECT max(length(description)) as LEN from catalog;
    ```

    **Alternativní řešení**: Pokud máte objekt LOB, který je větší než 64 KB, použijte parametr "Allow neomezenou velikost LOB". Počítejte s tím, že migrace pomocí parametru "povolí neomezenou velikost LOB" bude pomalejší než migrace pomocí parametru "omezení velikosti LOB".

## <a name="limitations-when-migrating-online-from-aws-rds-mysql"></a>Omezení při migraci online z AWS VP MySQL

Při pokusu o provedení online migrace z AWS VP MySQL do Azure Database for MySQL může docházet k následujícím chybám.

- **Chyba:** Databáze {0} má v cíli cizí klíče (y). Opravte cíl a spusťte novou aktivitu migrace dat. Spustit pod skriptem na cíli a vypsat cizí klíč (y)

  **Omezení**: Pokud máte ve svém schématu cizí klíče, počáteční zatížení a nepřetržitá synchronizace migrace se nezdaří.
  **Alternativní řešení**: spusťte následující skript v aplikaci MySQL Workbench pro extrakci skriptu cizího klíče a přidejte skript cizího klíče:

  ```
  SET group_concat_max_len = 8192; SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery FROM (SELECT KCU.REFERENCED_TABLE_SCHEMA as SchemaName, KCU.TABLE_NAME, KCU.COLUMN_NAME, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC WHERE KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA AND KCU.REFERENCED_TABLE_SCHEMA = 'SchemaName') Queries GROUP BY SchemaName;
  ```

- **Chyba:** Databáze {0} na serveru neexistuje. U zadaného zdrojového serveru MySQL se rozlišují malá a velká písmena. Zkontrolujte název databáze.

  **Omezení**: při migraci databáze MySQL do Azure pomocí rozhraní příkazového řádku (CLI) můžou uživatelé tuto chybu narazit. Služba nemohla najít databázi na zdrojovém serveru, což může být způsobeno tím, že jste pravděpodobně zadali nesprávný název databáze nebo databáze na uvedeném serveru neexistuje. Poznámka: v názvech databází se rozlišují velká a malá písmena.

  **Alternativní řešení**: Zadejte přesný název databáze a pak to zkuste znovu.

- **Chyba:** V databázi {Database} jsou tabulky se stejným názvem. Azure Database for MySQL nepodporuje tabulky, u kterých se rozlišují malá a velká písmena.

  **Omezení**: k této chybě dochází, pokud ve zdrojové databázi máte dvě tabulky se stejným názvem. Azure Database for MySQL nepodporuje tabulky citlivé na velká a malá písmena.

  **Alternativní řešení**: aktualizujte názvy tabulek tak, aby byly jedinečné, a akci opakujte.

- **Chyba:** Cílová databáze {Database} je prázdná. Proveďte migraci schématu.

  **Omezení**: k této chybě dochází, pokud cílová databáze Azure Database for MySQL nemá požadované schéma. Migrace schématu je nutná k tomu, aby bylo možné migrovat data do cíle.

  **Alternativní řešení**: [migrujte schéma](./tutorial-mysql-azure-mysql-online.md#migrate-the-sample-schema) ze zdrojové databáze do cílové databáze.

## <a name="other-limitations"></a>Další omezení

- Řetězec hesla, který má levou a pravou složenou závorku {} na začátku a konci řetězce hesla, není podporován. Toto omezení platí pro připojení ke zdrojovému MySQL a cílové Azure Database for MySQL.
- Následující DDLs nejsou podporovány:
  - Všechny oddíly DDLs
  - Odkládací tabulka
  - Přejmenovat tabulku
- Pomocí příkazu *alter table <table_name> příkaz Přidat sloupec <column_name>* přidat sloupce do začátku nebo do středu tabulky se nepodporuje. *Příkaz ALTER table <table_name> přidat sloupec <column_name>* přidá sloupec na konci tabulky.
- Indexy vytvořené pouze v části dat sloupce nejsou podporovány. Následující příkaz je příklad, který vytvoří index pomocí pouze části dat sloupce:

    ``` 
    CREATE INDEX partial_name ON customer (name(10));
    ```

- V Azure Database Migration Service je limit databází pro migraci v jedné aktivitě migrace čtyři.

- Azure DMS nepodporuje referenční akci CASCADE, která pomáhá automaticky odstranit nebo aktualizovat shodný řádek v podřízené tabulce při odstranění nebo aktualizaci řádku v nadřazené tabulce. Další informace najdete v části referenční akce článku [omezení cizího klíče](https://dev.mysql.com/doc/refman/8.0/en/create-table-foreign-keys.html)v dokumentaci k MySQL. Azure DMS vyžaduje, abyste při počátečním načtení dat vyřadíte omezení cizího klíče v cílovém databázovém serveru a nemůžete použít referenční akce. Pokud vaše zatížení závisí na aktualizaci související podřízené tabulky prostřednictvím této referenční akce, doporučujeme místo toho provést [Výpis a obnovení](../mysql/concepts-migrate-dump-restore.md) . 

- **Chyba:** Velikost řádku je moc velká (> 8126). Změna některých sloupců na TEXT nebo objekt BLOB může pomáhat. V aktuálním formátu řádku je předpona objektu BLOB 0 bajtů uložená jako vložená.

  **Omezení**: k této chybě dochází, když migrujete na Azure Database for MySQL pomocí modulu úložiště InnoDB a jakákoli velikost řádku tabulky je moc velká (>8126 bajtů).

  **Alternativní řešení**: aktualizujte schéma tabulky, která má velikost řádku větší než 8126 bajtů. Nedoporučujeme měnit striktní režim, protože data se zkrátí. Změna page_size není podporovaná.