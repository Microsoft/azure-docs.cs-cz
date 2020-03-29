---
title: Formát protokolu auditu
description: Zjistěte, jak jsou strukturovány protokoly auditování databáze SQL.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 01/03/2019
ms.openlocfilehash: 13746b86eed75055ceb5203afafb2d27a78ce1d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76722080"
---
# <a name="sql-database-audit-log-format"></a>Formát protokolu auditu databáze SQL

[Azure SQL Database auditování](sql-database-auditing.md) sleduje události databáze a zapíše je do protokolu auditu ve vašem účtu úložiště Azure nebo je odešle do Centra událostí nebo analýzy protokolů pro následné zpracování a analýzu.

## <a name="naming-conventions"></a>Konvence vytváření názvů

### <a name="blob-audit"></a>Blob Audit

Protokoly auditu uložené v úložišti objektů `sqldbauditlogs` Blob jsou uložené v kontejneru pojmenovaném v účtu Úložiště Azure. Hierarchie adresářů v kontejneru `<ServerName>/<DatabaseName>/<AuditName>/<Date>/`je formuláře . Formát názvu souboru `<CreationTime>_<FileNumberInSession>.xel`objektů `CreationTime` Blob je `hh_mm_ss_ms` , kde `FileNumberInSession` je ve formátu UTC a je spuštěnindex v protokolech relace případu zahrnuje více souborů objektů Blob.

Například pro `Database1` databázi na `Server1` následující je možná platná cesta:

    Server1/Database1/SqlDbAuditing_ServerAudit_NoRetention/2019-02-03/12_23_30_794_0.xel

[Repliky jen pro čtení](sql-database-read-scale-out.md) Protokoly auditu jsou uloženy ve stejném kontejneru. Hierarchie adresářů v kontejneru `<ServerName>/<DatabaseName>/<AuditName>/<Date>/RO/`je formuláře . Název souboru objektů Blob sdílí stejný formát. Protokoly auditu replik jen pro čtení jsou uloženy ve stejném kontejneru.


### <a name="event-hub"></a>Centrum událostí

Události auditu jsou zapsány do oboru názvů a centra událostí, které byly definovány během konfigurace auditování, a jsou zachyceny v těle událostí [Apache Avro](https://avro.apache.org/) a uloženy pomocí formátování JSON s kódováním UTF-8. Chcete-li číst protokoly auditu, můžete použít [Nástroje Avro](../event-hubs/event-hubs-capture-overview.md#use-avro-tools) nebo podobné nástroje, které tento formát zpracovávají.

### <a name="log-analytics"></a>Log Analytics

Události auditu jsou zapsány do pracovního prostoru Log `AzureDiagnostics` Analytics definovanéběhem konfigurace auditování, do tabulky s kategorií `SQLSecurityAuditEvents`. Další užitečné informace o vyhledávacím jazyce a příkazech služby Log Analytics naleznete [v tématu Odkaz na vyhledávání analýzy protokolů](../log-analytics/log-analytics-log-search.md).

## <a name="audit-log-fields"></a><a id="subheading-1"></a>Pole protokolu auditu

| Jméno (blob) | Název (centra událostí/analýza protokolů) | Popis | Typ objektu blob | Typ centra událostí/analýzy protokolů |
|-------------|---------------------------------|-------------|-----------|-------------------------------|
| action_id | action_id_s | ID akce | Varchar(4) | řetězec |
| action_name | action_name_s | Název akce | Není dostupné. | řetězec |
| additional_information | additional_information_s | Jakékoli další informace o události uložené ve formátu XML | nvarchar (4000) | řetězec |
| affected_rows | affected_rows_d | Počet řádků ovlivněných dotazem | bigint | int |
| Název_aplikace | application_name_s| Název klientské aplikace | nvarchar(128) | řetězec |
| audit_schema_version | audit_schema_version_d | Vždy 1 | int | int |
| class_type | class_type_s | Typ auditovatelného subjektu, ke kterému audit dochází | Varchar(2) | řetězec |
| class_type_desc | class_type_description_s | Popis auditovatelného subjektu, ke kterému audit dochází | Není dostupné. | řetězec |
| client_ip | client_ip_s | Zdrojová IP adresa klientské aplikace | nvarchar(128) | řetězec |
| connection_id | Není dostupné. | ID připojení na serveru | GUID | Není dostupné. |
| data_sensitivity_information | data_sensitivity_information_s | Typy informací a popisky citlivosti vrácené auditovaným dotazem na základě klasifikovaných sloupců v databázi. Další informace o [zjišťování a klasifikaci dat azure SQL database](sql-database-data-discovery-and-classification.md) | nvarchar (4000) | řetězec |
| Název_databáze | database_name_s | Kontext databáze, ve kterém došlo k akci | Sysname | řetězec |
| database_principal_id | database_principal_id_d | ID kontextu uživatele databáze, ve které je akce provedena | int | int |
| database_principal_name | database_principal_name_s | Název kontextu uživatele databáze, ve kterém je akce provedena | Sysname | řetězec |
| duration_milliseconds | duration_milliseconds_d | Doba provádění dotazu v milisekundách | bigint | int |
| event_time | event_time_t | Datum a čas, kdy je auditovatelná akce aktivována | datetime2 | datetime |
| Název_hostitele | Není dostupné. | Název hostitele klienta | řetězec | Není dostupné. |
| is_column_permission | is_column_permission_s | Příznak označující, pokud se jedná o oprávnění na úrovni sloupce. 1 = pravda, 0 = false | bitové | řetězec |
| Není dostupné. | is_server_level_audit_s | Příznak označující, zda je tento audit na úrovni serveru | Není dostupné. | řetězec |
| object_ id | object_id_d | ID entity, u které audit proběhl. To zahrnuje : serverové objekty, databáze, databázové objekty a objekty schématu. 0, pokud je entita samotným serverem nebo pokud audit není proveden na úrovni objektu | int | int |
| Object_name | object_name_s | Název entity, u které došlo k auditu. To zahrnuje : serverové objekty, databáze, databázové objekty a objekty schématu. 0, pokud je entita samotným serverem nebo pokud audit není proveden na úrovni objektu | Sysname | řetězec |
| permission_bitmask | permission_bitmask_s | V příslušných případech zobrazuje oprávnění, která byla udělena, odepřena nebo odvolána. | varbinary(16) | řetězec |
| response_rows | response_rows_d | Počet řádků vrácených v sadě výsledků | bigint | int |
| Schema_name | schema_name_s | Kontext schématu, ve kterém došlo k akci. Null pro audity vyskytující se mimo schéma | Sysname | řetězec |
| Není dostupné. | securable_class_type_s | Sevyléčitelný objekt, který se mapuje na class_type auditovaný | Není dostupné. | řetězec |
| sequence_group_id | sequence_group_id_g | Jedinečný identifikátor | Varbinary | GUID |
| sequence_number | sequence_number_d | Sleduje pořadí záznamů v rámci jednoho záznamu auditu, který byl příliš velký a nevešel se do vyrovnávací paměti pro zápis pro audity. | int | int |
| server_instance_name | server_instance_name_s | Název instance serveru, kde došlo k auditu | Sysname | řetězec |
| server_principal_id | server_principal_id_d | ID přihlašovacího kontextu, ve kterém se akce provádí | int | int |
| server_principal_name | server_principal_name_s | Aktuální přihlášení | Sysname | řetězec |
| server_principal_sid | server_principal_sid_s | Aktuální přihlašovací SID | Varbinary | řetězec |
| Session_id | session_id_d | ID relace, ve které k události došlo | smallint | int |
| session_server_principal_name | session_server_principal_name_s | Objekt zabezpečení serveru pro relaci | Sysname | řetězec |
| Prohlášení | statement_s | T-SQL příkaz, který byl proveden (pokud existuje) | nvarchar (4000) | řetězec |
| succeeded | succeeded_s | Označuje, zda byla akce, která událost spustila, úspěšná. U jiných událostí než přihlášení a dávky to pouze hlásí, zda kontrola oprávnění proběhla úspěšně nebo se nezdařila, nikoli operace. 1 = úspěch, 0 = selhání | bitové | řetězec |
| target_database_principal_id | target_database_principal_id_d | Objekt zabezpečení databáze, na kterých se provádí operace GRANT/DENY/REVOKE. 0, pokud není použitelné | int | int |
| target_database_principal_name | target_database_principal_name_s | Cílový uživatel akce. NULL, pokud není použitelná | řetězec | řetězec |
| target_server_principal_id | target_server_principal_id_d | Hlavní server, na kterých se provádí operace GRANT/DENY/REVOKE. Vrátí hodnotu 0, pokud není použitelná. | int | int |
| target_server_principal_name | target_server_principal_name_s | Cílové přihlášení akce. NULL, pokud není použitelná | Sysname | řetězec |
| target_server_principal_sid | target_server_principal_sid_s | SID cílového přihlášení. NULL, pokud není použitelná | Varbinary | řetězec |
| transaction_id | transaction_id_d | Jenom SQL Server (počínaje rokem 2016) – 0 pro Azure SQL DB | bigint | int |
| user_defined_event_id | user_defined_event_id_d | ID události definované uživatelem bylo předáno jako argument k sp_audit_write. Null pro systémové události (výchozí) a nenulové pro uživatelem definované události. Další informace naleznete [v tématu sp_audit_write (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) | smallint | int |
| user_defined_information | user_defined_information_s | Uživatelem definované informace předané jako argument k sp_audit_write. Null pro systémové události (výchozí) a nenulové pro uživatelem definované události. Další informace naleznete [v tématu sp_audit_write (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) | nvarchar (4000) | řetězec |

## <a name="next-steps"></a>Další kroky

Další informace o [auditování Azure SQL Database](sql-database-auditing.md).
