---
title: Formát protokolu auditování databáze SQL | Dokumentace Microsoftu
description: Pochopte, jakým způsobem jsou strukturovaná protokoly auditu SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: vainolo
ms.author: arib
ms.reviewer: vanto
manager: craigg
ms.date: 01/03/2019
ms.openlocfilehash: 915ccc60216f3f206bcdc53825decac4d6d020d0
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2019
ms.locfileid: "57012025"
---
# <a name="sql-database-audit-log-format"></a>Formát protokolu auditování databáze SQL

[Auditování služby Azure SQL Database](sql-database-auditing.md) sleduje události databáze a zapisuje je do auditování ve vašem účtu úložiště Azure protokolu nebo je odešle do centra událostí nebo Log Analytics pro zpracování příjmu dat a analýzu.

## <a name="naming-conventions"></a>Zásady vytváření názvů

### <a name="blob-audit"></a>Auditování objektů BLOB

Protokoly auditu uložené v úložišti objektů Blob jsou uloženy v kontejneru nazvaném `sqldbauditlogs` v účtu Azure Storage. Hierarchie adresáře v rámci kontejneru je ve formátu `<ServerName>/<DatabaseName>/<AuditName>/<Date>/`. Formát názvu souboru objektu Blob je `<CreationTime>_<FileNumberInSession>.xel`, kde `CreationTime` je ve standardu UTC, `hh_mm_ss_ms` formátu, a `FileNumberInSession` je spuštěné indexu v případě, že relace protokoly rozsahy napříč více souborů objektů Blob.

Například pro databáze `Database1` na `Server1` tady je možné platnou cestu:

    Server1/Database1/SqlDbAuditing_ServerAudit_NoRetention/2019-02-03/12_23_30_794_0.xel

### <a name="event-hub"></a>Centrum událostí

Události auditu se zapisují do obor názvů a Centrum událostí, který byl určen během konfigurace auditování a jsou zachyceny v těle [Apache Avro](http://avro.apache.org/) události a uložit pomocí formátování JSON s kódováním UTF-8. Čtení protokolů auditu, můžete použít [nástroje Avro](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview#use-avro-tools) nebo podobné nástroje, které zpracovávají tento formát.

### <a name="log-analytics"></a>Log Analytics

Události auditu se zapisují do definovaného během konfigurace auditování do pracovního prostoru Log Analytics `AzureDiagnostics` tabulky s kategorií `SQLSecurityAuditEvents`. Další užitečné informace o vyhledávací jazyk Log Analytics a příkazy najdete v tématu [referenční příručce k vyhledávání Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search).

## <a id="subheading-1"></a>Pole protokolu auditu

| Název (Blob) | Název (Event Hubs/Log Analytics) | Popis | Typ objektu BLOB | Typ události rozbočovače/Log Analytics |
|-------------|---------------------------------|-------------|-----------|-------------------------------|
| action_id | action_id_s | ID akce | varchar(4) | string |
| action_name | action_name_s | Název akce | neuvedeno | string |
| additional_information | additional_information_s | Jakékoli další informace o události, které jsou uložené ve formátu XML | nvarchar(4000) | string |
| affected_rows | affected_rows_d | Počet řádků, které jsou ovlivněny dotazu | bigint | int |
| název_aplikace | application_name_s| Název klientské aplikace | nvarchar(128) | string |
| audit_schema_version | audit_schema_version_d | Vždy 1 | int | int |
| class_type | class_type_s | Typ auditovatelných entity, která proběhne auditu | varchar(2) | string |
| class_type_desc | class_type_description_s | Popis auditovatelných entity, která proběhne auditu | neuvedeno | string |
| client_ip | client_ip_s | Zdrojová IP adresa klientské aplikace | nvarchar(128) | string |
| connection_id | neuvedeno | ID připojení na serveru | GUID | neuvedeno |
| data_sensitivity_information | data_sensitivity_information_s | Typy informací a popisky citlivosti vrácené dotazem toto auditování se provádí, podle klasifikované sloupce v databázi. Další informace o [Azure SQL Database data zjišťování a klasifikace](sql-database-data-discovery-and-classification.md) | nvarchar(4000) | string |
| Název databáze database_name | database_name_s | Kontext databáze, ve kterém došlo k akci | sysname | string |
| database_principal_id | database_principal_id_d | ID, která akce se provádí v kontextu uživatele databáze | int | int |
| database_principal_name | database_principal_name_s | Název databáze uživatelský kontext provedení akce | sysname | string |
| duration_milliseconds | duration_milliseconds_d | Doba trvání spuštění dotazu v milisekundách | bigint | int |
| event_time | event_time_t | Datum a čas, kdy se spustí auditovatelných akce | datetime2 | datetime |
| název_hostitele | neuvedeno | Název hostitele klienta | string | neuvedeno |
| is_column_permission | is_column_permission_s | Příznak označující, jestli jde úrovně oprávnění sloupců. 1 = true, 0 = false | Bit | string |
| neuvedeno | is_server_level_audit_s | Příznak označující, jestli je toto auditování na úrovni serveru | neuvedeno | string |
| object_ – id | object_id_d | ID entity, na kterém došlo k auditu. Jedná se o: objekty serveru, databází, databázové objekty a objekty schématu. 0, pokud entita je samotný server nebo pokud je auditování provést na úrovni objektu | int | int |
| object_name | object_name_s | Název entity, na kterém došlo k auditu. Jedná se o: objekty serveru, databází, databázové objekty a objekty schématu. 0, pokud entita je samotný server nebo pokud je auditování provést na úrovni objektu | sysname | string |
| permission_bitmask | permission_bitmask_s | V případě potřeby, ukazuje, které byly udělen, byl odepřen, nebo odvolat oprávnění | varbinary(16) | string |
| response_rows | response_rows_d | Počet řádků vrácených sady výsledků dotazu | bigint | int |
| schema_name | schema_name_s | Schéma kontext, ve kterém došlo k akci. Hodnota NULL pro audity, ke kterým dochází mimo schéma | sysname | string |
| neuvedeno | securable_class_type_s | Zabezpečitelných objektů, který se mapuje na class_type se Audituje | neuvedeno | string |
| sequence_group_id | sequence_group_id_g | Jedinečný identifikátor | varbinary | GUID |
| sequence_number | sequence_number_d | Sleduje pořadí záznamů v rámci jednoho auditu záznam, který byl příliš velký a nevejde se do vyrovnávací paměti pro zápis pro audit | int | int |
| server_instance_name | server_instance_name_s | Název instance serveru, kde došlo k auditu | sysname | string |
| server_principal_id | server_principal_id_d | ID kontext přihlášení provedení akce | int | int |
| server_principal_name | server_principal_name_s | Aktuální přihlášení | sysname | string |
| server_principal_sid | server_principal_sid_s | Aktuální přihlášení SID | varbinary | string |
| session_id | session_id_d | ID relace, ve kterém došlo k události | smallint | int |
| session_server_principal_name | session_server_principal_name_s | Objekt zabezpečení serveru pro relaci | sysname | string |
| – Příkaz | statement_s | Příkaz T-SQL, který se spustil (pokud existuje) | nvarchar(4000) | string |
| Bylo úspěšně dokončeno | succeeded_s | Určuje, jestli akce, který spustil danou událost byla úspěšná. Pro události než přihlášení a služby batch to podává zprávy pouze zda kontrola oprávnění úspěšné nebo neúspěšné, není operace. 1 = úspěch, 0 = selhání | Bit | string |
| target_database_principal_id | target_database_principal_id_d | Objekt zabezpečení databáze GRANT/DENY/REVOKE operace se provádí na. 0, pokud není k dispozici | int | int |
| target_database_principal_name | target_database_principal_name_s | Cílový uživatel akce. Hodnota NULL, pokud není k dispozici | string | string |
| target_server_principal_id | target_server_principal_id_d | Hlavní server, který provádí operace GRANT/DENY/REVOKE na. Vrátí hodnotu 0, pokud není k dispozici | int | int |
| target_server_principal_name | target_server_principal_name_s | Cíl přihlášení akce. Hodnota NULL, pokud není k dispozici | sysname | string |
| target_server_principal_sid | target_server_principal_sid_s | Identifikátor SID cílového přihlášení. Hodnota NULL, pokud není k dispozici | varbinary | string |
| transaction_id | transaction_id_d | Pouze SQL Server (počínaje 2016) - 0 pro službu Azure SQL DB | bigint | int |
| user_defined_event_id | user_defined_event_id_d | Id události jako argument předána sp_audit_write definovaný uživatelem. Pro události systému (výchozí) hodnotu NULL a nenulovou pro uživatelem definovanou událost. Další informace najdete v tématu [sp_audit_write (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) | smallint | int |
| user_defined_information | user_defined_information_s | Informace o předat jako argument sp_audit_write definovaný uživatelem. Pro události systému (výchozí) hodnotu NULL a nenulovou pro uživatelem definovanou událost. Další informace najdete v tématu [sp_audit_write (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) | nvarchar(4000) | string |

## <a name="next-steps"></a>Další kroky

Další informace o [auditování služby Azure SQL Database](sql-database-auditing.md).