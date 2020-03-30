---
title: 'Známé problémy: Online migrace do Databáze Azure pro MySQL'
titleSuffix: Azure Database Migration Service
description: Zjistěte o známých problémech a omezeních migrace s online migrací do databáze Azure pro MySQL při použití služby Migrace databáze Azure.
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
ms.openlocfilehash: 8c3de28ea934302086a5b14e61482e6a4ab9a7ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235282"
---
# <a name="online-migration-issues--limitations-to-azure-db-for-mysql-with-azure-database-migration-service"></a>Problémy s migrací online & omezení na Azure DB pro MySQL se službou migrace do databáze Azure

Známé problémy a omezení související s online migrací z MySQL do databáze Azure pro MySQL jsou popsány v následujících částech.

## <a name="online-migration-configuration"></a>Konfigurace migrace online


- Zdroj ová verze serveru MySQL Server musí být verze 5.6.35, 5.7.18 nebo novější.
- Azure Database for MySQL podporuje:
  - Komunitní vydání MySQL
  - Motor InnoDB
- Stejná verze migrace. Migrace MySQL 5.6 do databáze Azure pro MySQL 5.7 není podporovaná.
- Povolit binární protokolování v my.ini (Windows) nebo my.cnf (Unix)
  - Nastavte Server_id na libovolné číslo větší nebo rovno 1, například Server_id =1 (pouze pro MySQL 5.6)
  - Nastavit log-bin \<=> cesty (pouze pro MySQL 5.6)
  - Nastavit binlog_format = řádek
  - Expire_logs_days = 5 (doporučeno - pouze pro MySQL 5.6)
- Uživatel musí mít roli ReplicationAdmin.
- Řazení definované pro zdrojovou databázi MySQL jsou stejné jako ty, které jsou definovány v cílové databázi Azure pro MySQL.
- Schéma musí odpovídat mezi zdrojovou databázi MySQL a cílovou databází v Azure Database for MySQL.
- Schéma v cílové azure databázi pro MySQL nesmí mít cizí klíče. Pomocí následujícího dotazu přetáhněte cizí klíče:
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
- Schéma v cílové azure databázi pro MySQL nesmí mít žádné aktivační události. Přetažení aktivačních událostí v cílové databázi:
    ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
    ```

## <a name="datatype-limitations"></a>Omezení datových typů

- **Omezení**: Pokud je ve zdrojové databázi MySQL datový typ JSON, migrace se nezdaří během nepřetržité synchronizace.

    **Řešení:** Upravte datový typ JSON na střední text nebo dlouhý text ve zdrojové databázi MySQL.

- **Omezení**: Pokud v tabulkách není žádný primární klíč, nepřetržitá synchronizace se nezdaří.

    **Řešení:** Dočasně nastavte primární klíč pro pokračování migrace v tabulce. Po dokončení migrace dat můžete primární klíč odebrat.

## <a name="lob-limitations"></a>OMEZENÍ LOB

Sloupce velkého objektu (LOB) jsou sloupce, které mohou zvětšit velké velikosti. Pro MySQL, Střední text, Longtext, Blob, Mediumblob, Longblob, atd., jsou některé z datových typů LOB.

- **Omezení**: Pokud jsou jako primární klíče použity obchodní datové typy, migrace se nezdaří.

    **Řešení:** Nahraďte primární klíč jinými datovými typy nebo sloupci, které nejsou obchodní.

- **Omezení**: Pokud je délka sloupce Velký objekt (LOB) větší než 32 kB, data mohou být zkrácena na cíl. Délku sloupce LOB můžete zkontrolovat pomocí tohoto dotazu:
    ```
    SELECT max(length(description)) as LEN from catalog;
    ```

    **Řešení:** Pokud máte lob objekt, který je větší než 32 kB, obraťte se na technický tým na [Ask Migrace databáze Azure](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

## <a name="limitations-when-migrating-online-from-aws-rds-mysql"></a>Omezení při migraci online z AWS RDS MySQL

Při pokusu o provedení online migrace z AWS RDS MySQL do databáze Azure pro MySQL, můžete narazit na následující chyby.

- **Chyba:** Databáze{0}' ' má cizí klíč (y) na cíl. Opravte cíl a spusťte novou aktivitu migrace dat. Spustit pod skript na cíl seznam cizích klíčů

  **Omezení**: Pokud máte cizí klíče ve schématu, počáteční zatížení a průběžná synchronizace migrace se nezdaří.
  **Řešení**: Spusťte následující skript v pracovní stolce MySQL extrahujte skript cizího klíče a přidejte skript cizího klíče:

  ```
  SET group_concat_max_len = 8192; SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery FROM (SELECT KCU.REFERENCED_TABLE_SCHEMA as SchemaName, KCU.TABLE_NAME, KCU.COLUMN_NAME, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC WHERE KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA AND KCU.REFERENCED_TABLE_SCHEMA = 'SchemaName') Queries GROUP BY SchemaName;
  ```

- **Chyba:** Databáze{0}' ' neexistuje na serveru. U zadaného zdrojového serveru MySQL se rozlišují malá a velká písmena. Zkontrolujte název databáze.

  **Omezení**: Při migraci databáze MySQL do Azure pomocí rozhraní příkazového řádku (CLI) mohou uživatelé tuto chybu selhat. Služba nemohla najít databázi na zdrojovém serveru, což může být způsobeno tím, že jste zadali nesprávný název databáze nebo databáze na uvedeném serveru neexistuje. Názvy databází poznámek rozlišují malá a velká písmena.

  **Řešení**: Zadejte přesný název databáze a akci opakujte.

- **Chyba:** V databázi {database} jsou tabulky se stejným názvem. Azure Database for MySQL nepodporuje tabulky, u kterých se rozlišují malá a velká písmena.

  **Omezení**: K této chybě dojde, pokud máte ve zdrojové databázi dvě tabulky se stejným názvem. Azure Database for MySQL nepodporuje tabulky rozlišující malá a velká písmena.

  **Řešení**: Aktualizujte názvy tabulek tak, aby byly jedinečné, a akci opakujte.

- **Chyba:** Cílová databáze {database} je prázdná. Proveďte migraci schématu.

  **Omezení**: K této chybě dochází, když cílová databáze Azure pro databázi MySQL nemá požadované schéma. Migrace schématu je nutná k povolení migrace dat do cíle.

  **Řešení:** [Migrace schématu](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online#migrate-the-sample-schema) ze zdrojové databáze do cílové databáze.

## <a name="other-limitations"></a>Další omezení

- Řetězec hesla, který má otevření a zavření složených závorek { } na začátku a na konci řetězce hesla, není podporován. Toto omezení platí pro připojení ke zdroji MySQL a cílové Azure Database pro MySQL.
- Následující ddl nejsou podporovány:
  - Všechny oddíly DDL
  - Drop tabulka
  - Přejmenovat tabulku
- Použití *<alter tabulky table_name> přidání sloupce <column_name <>* příkaz k přidání sloupců na začátek nebo do středu tabulky není podporováno. THe *změnit tabulku <table_name> přidat sloupec <column_name>* přidá sloupec na konci tabulky.
- Indexy vytvořené pouze na části dat sloupců nejsou podporovány. Následující příkaz je příklad, který vytvoří index pomocí pouze části dat sloupce:

    ``` 
    CREATE INDEX partial_name ON customer (name(10));
    ```

- Ve službě Migrace databáze Azure je limit migrace databází v jedné aktivitě migrace čtyři.

- **Chyba:** Velikost řádku je příliš velká (> 8126). Změna některých sloupců na TEXT nebo BLOB může pomoci. V aktuálním formátu řádku je v řádku uložena předpona BLOB 0 bajtů.

  **Omezení**: K této chybě dochází, když migrujete do databáze Azure pro MySQL pomocí modulu úložiště InnoDB a jakákoli velikost řádku tabulky je příliš velká (>8126 bajtů).

  **Řešení:** Aktualizujte schéma tabulky, která má velikost řádku větší než 8126 bajtů. Nedoporučujeme měnit přísný režim, protože data budou zkrácena. Změna page_size není podporována.
