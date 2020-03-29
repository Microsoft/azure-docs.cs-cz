---
title: Protokoly auditu – databáze Azure pro MariaDB
description: Popisuje protokoly auditu, které jsou k dispozici v Azure Database pro MariaDB a dostupné parametry pro povolení úrovně protokolování.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/19/2020
ms.openlocfilehash: e8d5abd81feb86ba48fc442ee95615cb52230a24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063818"
---
# <a name="audit-logs-in-azure-database-for-mariadb"></a>Protokoly auditu v databázi Azure pro MariaDB

V Azure Database pro MariaDB protokolu auditu je k dispozici uživatelům. Protokol auditu lze použít ke sledování aktivity na úrovni databáze a běžně se používá pro dodržování předpisů.

> [!IMPORTANT]
> Funkce protokolu auditu je nyní ve verzi Preview.

## <a name="configure-audit-logging"></a>Konfigurace protokolování auditu

Ve výchozím nastavení je protokol auditu zakázán. Chcete-li jej `audit_log_enabled` povolit, nastavte možnost ZAPNUTO.

Mezi další parametry, které můžete upravit, patří:

- `audit_log_events`: řídí události, které mají být protokolovány. Konkrétní události auditu naleznete v následující tabulce.
- `audit_log_include_users`: MariaDB uživatelé mají být zahrnuty pro protokolování. Výchozí hodnota tohoto parametru je prázdná, která bude zahrnovat všechny uživatele pro protokolování. To má vyšší `audit_log_exclude_users`prioritu než . Maximální délka parametru je 512 znaků.
> [!Note]
> `audit_log_include_users`má vyšší `audit_log_exclude_users`prioritu nad . Například if `audit_log_include_users`  =  `demouser` `audit_log_exclude_users`  =  `demouser`a , uživatel bude zahrnut do `audit_log_include_users` protokolů auditu, protože má vyšší prioritu.
- `audit_log_exclude_users`: Uživatelé MariaDB budou z protokolování vyloučeni. Umožňuje maximálně čtyři uživatele. Maximální délka parametru je 256 znaků.

| **Událost** | **Popis** |
|---|---|
| `CONNECTION` | - Zahájení připojení (úspěšné nebo neúspěšné) <br> - Opakované ověření uživatele s jiným uživatelem / heslem během relace <br> - Ukončení připojení |
| `DML_SELECT`| DOTAZY SELECT |
| `DML_NONSELECT` | Vložit/odstranit/aktualizovat dotazy |
| `DML` | DML = DML_SELECT + DML_NONSELECT |
| `DDL` | Dotazy jako "DROP DATABÁZE" |
| `DCL` | Dotazy jako "UDĚLENÍ OPRÁVNĚNÍ" |
| `ADMIN` | Dotazy jako "ZOBRAZIT STATUS" |
| `GENERAL` | Vše v DML_SELECT, DML_NONSELECT, DML, DDL, DCL a ADMIN |

## <a name="access-audit-logs"></a>Přístup k protokolům auditu

Protokoly auditu jsou integrované s diagnostickými protokoly monitorování Azure. Jakmile na serveru MariaDB aktivujete protokoly auditu, můžete je vypouštět do protokolů Azure Monitor, centra událostí nebo do Azure Storage. Další informace o povolení diagnostických protokolů na webu Azure Portal najdete v [článku portálu protokolu auditu](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs).

## <a name="diagnostic-logs-schemas"></a>Schémata diagnostických protokolů

Následující části popisují, co je výstup protokoly auditu MariaDB na základě typu události. V závislosti na výstupní metodě se zahrnutá pole a pořadí, ve kterém se zobrazují, se mohou lišit.

### <a name="connection"></a>Připojení

| **Vlastnost** | **Popis** |
|---|---|
| `TenantId` | ID vašeho klienta |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Časové razítko při zaznamenání protokolu v utc |
| `Type` | Typ protokolu. Vždy`AzureDiagnostics` |
| `SubscriptionId` | Identifikátor GUID pro předplatné, do kterého server patří |
| `ResourceGroup` | Název skupiny prostředků, do které server patří |
| `ResourceProvider` | Název zprostředkovatele prostředků. Vždy`MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Identifikátor URI prostředku |
| `Resource` | Název serveru |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `event_class_s` | `connection_log` |
| `event_subclass_s` | `CONNECT`, `DISCONNECT` |
| `connection_id_d` | Jedinečné ID připojení generované MariaDB |
| `host_s` | Funkce Blank |
| `ip_s` | IP adresa klienta připojujícího se k MariaDB |
| `user_s` | Jméno uživatele provádějícího dotaz |
| `db_s` | Název databáze připojené k databázi |
| `\_ResourceId` | Identifikátor URI prostředku |

### <a name="general"></a>Obecné

Schéma níže platí pro obecné, DML_SELECT, DML_NONSELECT, DML, DDL, DCL a ADMIN typy událostí.

| **Vlastnost** | **Popis** |
|---|---|
| `TenantId` | ID vašeho klienta |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Časové razítko při zaznamenání protokolu v utc |
| `Type` | Typ protokolu. Vždy`AzureDiagnostics` |
| `SubscriptionId` | Identifikátor GUID pro předplatné, do kterého server patří |
| `ResourceGroup` | Název skupiny prostředků, do které server patří |
| `ResourceProvider` | Název zprostředkovatele prostředků. Vždy`MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Identifikátor URI prostředku |
| `Resource` | Název serveru |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Název serveru |
| `event_class_s` | `general_log` |
| `event_subclass_s` | `LOG`, `ERROR`, `RESULT` |
| `event_time` | Sekundy spuštění dotazu v časovém razítku systému UNIX |
| `error_code_d` | Kód chyby, pokud se dotaz nezdařil. `0`znamená, že žádná chyba |
| `thread_id_d` | ID vlákna, které provedlo dotaz |
| `host_s` | Funkce Blank |
| `ip_s` | IP adresa klienta připojujícího se k MariaDB |
| `user_s` | Jméno uživatele provádějícího dotaz |
| `sql_text_s` | Úplný text dotazu |
| `\_ResourceId` | Identifikátor URI prostředku |

## <a name="analyze-logs-in-azure-monitor-logs"></a>Analýza protokolů v protokolech monitorování Azure

Jakmile jsou protokoly auditu kanálem do protokolů monitorování Azure prostřednictvím diagnostických protokolů, můžete provést další analýzu auditovaných událostí. Níže jsou uvedeny některé ukázkové dotazy, které vám pomohou začít. Nezapomeňte aktualizovat níže s názvem serveru.

- Seznam obecných událostí na konkrétním serveru

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs' and event_class_s == "general_log"
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last 
    ```

- Seznam událostí PŘIPOJENÍ na konkrétním serveru

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs' and event_class_s == "connection_log"
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ```

- Shrneme auditované události na konkrétním serveru

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | summarize count() by event_class_s, event_subclass_s, user_s, ip_s
    ```

- Graf distribuce typu události auditu na konkrétním serveru

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart 
    ```

- Seznam auditovaných událostí na všech serverech MariaDB s povolenými diagnostickými protokoly pro protokoly auditu

    ```kusto
    AzureDiagnostics
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ``` 

## <a name="next-steps"></a>Další kroky

- [Konfigurace protokolů auditu na webu Azure Portal](howto-configure-audit-logs-portal.md)