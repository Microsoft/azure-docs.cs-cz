---
title: SQL Database formát protokolu auditu
description: Pochopte, jak jsou strukturované protokoly auditu Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: reference
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.custom: sqldbrb=1
ms.date: 06/03/2020
ms.openlocfilehash: f5c176db4f679c79bb42c6ceb46b3588e9440874
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100572226"
---
# <a name="sql-database-audit-log-format"></a>SQL Database formát protokolu auditu

[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

[Azure SQL Database auditování](auditing-overview.md) sleduje události databáze a zapisuje je do protokolu auditu ve vašem účtu služby Azure Storage nebo je odesílá do centra událostí nebo Log Analytics pro zpracování a analýzu pro příjem dat.

## <a name="naming-conventions"></a>Zásady vytváření názvů

### <a name="blob-audit"></a>Audit objektů BLOB

Protokoly auditu uložené v Azure Blob Storage se ukládají do kontejneru s názvem `sqldbauditlogs` v účtu služby Azure Storage. Hierarchie adresáře v rámci kontejneru má formu `<ServerName>/<DatabaseName>/<AuditName>/<Date>/` . Formát názvu souboru objektu BLOB je `<CreationTime>_<FileNumberInSession>.xel` , kde `CreationTime` je ve `hh_mm_ss_ms` formátu UTC, a `FileNumberInSession` je spuštěným indexem v případě, že protokoly relací jsou rozloženy mezi více souborů objektů BLOB.

Například pro databázi `Database1` v následujícím příkladu `Server1` je možnou platnou cestou:

`Server1/Database1/SqlDbAuditing_ServerAudit_NoRetention/2019-02-03/12_23_30_794_0.xel`

Protokoly auditu [replik jen pro čtení](read-scale-out.md) se ukládají do stejného kontejneru. Hierarchie adresáře v rámci kontejneru má formu `<ServerName>/<DatabaseName>/<AuditName>/<Date>/RO/` . Název souboru objektu BLOB sdílí stejný formát. Protokoly auditu replik jen pro čtení jsou uloženy ve stejném kontejneru.


### <a name="event-hub"></a>Centrum událostí

Události auditu se zapisují do oboru názvů a centra událostí definovaných během konfigurace auditování, zachytávají se v textu událostí [Apache Avro](https://avro.apache.org/) a ukládají se ve formátu JSON s kódováním UTF-8. Ke čtení protokolů auditu můžete použít [nástroje Avro](../../event-hubs/event-hubs-capture-overview.md#use-avro-tools) nebo podobné nástroje, které dokáží tento formát zpracovat.

### <a name="log-analytics"></a>Log Analytics

Události auditu se zapisují do pracovního prostoru služby Log Analytics definovaného během konfigurace auditování do tabulky `AzureDiagnostics` v kategorii `SQLSecurityAuditEvents`. Další užitečné informace o jazyku a příkazech pro vyhledávání v Log Analytics najdete v [referenčních informacích o vyhledávání v Log Analytics](../../azure-monitor/logs/log-query-overview.md).

## <a name="audit-log-fields"></a><a id="subheading-1"></a>Pole protokolu auditu

| Název (objekt BLOB) | Název (Event Hubs/Log Analytics) | Description | Typ objektu blob | Typ Event Hubs/Log Analytics |
|-------------|---------------------------------|-------------|-----------|-------------------------------|
| action_id | action_id_s | ID akce | varchar (4) | řetězec |
| action_name | action_name_s | Název akce | – | řetězec |
| additional_information | additional_information_s | Jakékoli další informace o události uložené jako XML | nvarchar (4000) | řetězec |
| affected_rows | affected_rows_d | Počet řádků ovlivněných dotazem | bigint | int |
| application_name | application_name_s| Název klientské aplikace | nvarchar (128) | řetězec |
| audit_schema_version | audit_schema_version_d | Vždycky 1 | int | int |
| class_type | class_type_s | Typ auditované entity, na které probíhá audit | varchar (2) | řetězec |
| class_type_desc | class_type_description_s | Popis auditované entity, na které probíhá audit | – | řetězec |
| client_ip | client_ip_s | Zdrojová IP adresa klientské aplikace | nvarchar (128) | řetězec |
| connection_id | – | ID připojení na serveru | Identifikátor GUID | – |
| data_sensitivity_information | data_sensitivity_information_s | Typy informací a popisky citlivosti vrácené auditovaným dotazem v závislosti na klasifikovaných sloupcích v databázi. Další informace o [Azure SQL Database zjišťování a klasifikace dat](data-discovery-and-classification-overview.md) | nvarchar (4000) | řetězec |
| database_name | database_name_s | Kontext databáze, ve kterém došlo k akci | musí | řetězec |
| database_principal_id | database_principal_id_d | ID kontextu uživatele databáze, ve kterém se akce provádí | int | int |
| database_principal_name | database_principal_name_s | Název kontextu uživatele databáze, ve kterém se akce provádí | musí | řetězec |
| duration_milliseconds | duration_milliseconds_d | Doba spuštění dotazu v milisekundách | bigint | int |
| event_time | event_time_t | Datum a čas, kdy se aktivuje akce auditování | datetime2 | datetime |
| host_name | – | Název hostitele klienta | řetězec | – |
| is_column_permission | is_column_permission_s | Příznak označující, zda se jedná o oprávnění na úrovni sloupce 1 = true, 0 = false | bit | řetězec |
| – | is_server_level_audit_s | Příznak označující, jestli je tento audit na úrovni serveru | – | řetězec |
| ID object_ | object_id_d | ID entity, na které došlo k auditu. To zahrnuje: objekty serveru, databáze, databázové objekty a objekty schématu. 0, pokud je entita samotný server nebo pokud audit není proveden na úrovni objektu | int | int |
| object_name | object_name_s | Název entity, na které došlo k auditu. To zahrnuje: objekty serveru, databáze, databázové objekty a objekty schématu. 0, pokud je entita samotný server nebo pokud audit není proveden na úrovni objektu | musí | řetězec |
| permission_bitmask | permission_bitmask_s | V případě potřeby se zobrazí oprávnění udělená, zamítnutá nebo odvolaná. | varbinary (16) | řetězec |
| response_rows | response_rows_d | Počet řádků vrácených v sadě výsledků dotazu | bigint | int |
| schema_name | schema_name_s | Kontext schématu, ve kterém došlo k akci. Hodnota NULL pro audity, ke kterým došlo mimo schéma | musí | řetězec |
| – | securable_class_type_s | Zabezpečitelné objekty, které se mapují na class_type auditovány | – | řetězec |
| sequence_group_id | sequence_group_id_g | Jedinečný identifikátor | varbinary | Identifikátor GUID |
| sequence_number | sequence_number_d | Sleduje sekvenci záznamů v rámci jednoho záznamu auditu, který byl příliš velký, aby se vešel do vyrovnávací paměti pro zápis pro audity. | int | int |
| server_instance_name | server_instance_name_s | Název instance serveru, ve které došlo k auditu | musí | řetězec |
| server_principal_id | server_principal_id_d | ID přihlašovacího kontextu, ve kterém se akce provádí | int | int |
| server_principal_name | server_principal_name_s | Aktuální přihlašovací jméno | musí | řetězec |
| server_principal_sid | server_principal_sid_s | Identifikátor SID aktuálního přihlášení | varbinary | řetězec |
| session_id | session_id_d | ID relace, ve které došlo k události | smallint | int |
| session_server_principal_name | session_server_principal_name_s | Objekt zabezpečení serveru pro relaci | musí | řetězec |
| vydá | statement_s | Příkaz jazyka T-SQL, který byl proveden (pokud existuje) | nvarchar (4000) | řetězec |
| succeeded | succeeded_s | Určuje, zda akce, která aktivovala událost, byla úspěšná. Pro jiné události než přihlašovací údaje a dávky Tato sestava oznamuje, zda byla ověření oprávnění úspěšná nebo neúspěšná, nikoli operace. 1 = úspěch, 0 = selhání | bit | řetězec |
| target_database_principal_id | target_database_principal_id_d | Objekt zabezpečení databáze: operace udělení/ZAMÍTNUTí/odvolání se provádí. 0, pokud není k dispozici | int | int |
| target_database_principal_name | target_database_principal_name_s | Cílový uživatel akce NULL, pokud není k dispozici | řetězec | řetězec |
| target_server_principal_id | target_server_principal_id_d | Objekt zabezpečení serveru, na kterém je prováděna operace GRANT/DENY/REVOKE. Vrátí 0, pokud není k dispozici. | int | int |
| target_server_principal_name | target_server_principal_name_s | Cílové přihlášení akce NULL, pokud není k dispozici | musí | řetězec |
| target_server_principal_sid | target_server_principal_sid_s | Identifikátor SID cílového přihlášení NULL, pokud není k dispozici | varbinary | řetězec |
| transaction_id | transaction_id_d | Pouze SQL Server (počínaje 2016)-0 pro Azure SQL Database | bigint | int |
| user_defined_event_id | user_defined_event_id_d | Uživatelem definované ID události předané jako argument pro sp_audit_write. Hodnota NULL pro systémové události (výchozí) a nenulová pro uživatelem definovanou událost. Další informace naleznete v tématu [sp_audit_write (Transact-SQL)](/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) . | smallint | int |
| user_defined_information | user_defined_information_s | Uživatelem definované informace předané jako argument pro sp_audit_write. Hodnota NULL pro systémové události (výchozí) a nenulová pro uživatelem definovanou událost. Další informace naleznete v tématu [sp_audit_write (Transact-SQL)](/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) . | nvarchar (4000) | řetězec |

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [Azure SQL Database auditování](auditing-overview.md).