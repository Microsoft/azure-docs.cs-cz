---
title: Protokoly auditu – Azure Database for MariaDB
description: V této části najdete popis protokolů auditu dostupných v Azure Database for MariaDB a dostupných parametrů pro povolení úrovní protokolování.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 6/24/2020
ms.openlocfilehash: 7c9d59eee1e1ce69394301023b108952eaf46790
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85362420"
---
# <a name="audit-logs-in-azure-database-for-mariadb"></a>Protokoly auditu v Azure Database for MariaDB

V Azure Database for MariaDB je k dispozici pro uživatele protokol auditu. Protokol auditu lze použít ke sledování aktivity na úrovni databáze a často se používá pro dodržování předpisů.

## <a name="configure-audit-logging"></a>Konfigurace protokolování auditu

>[!IMPORTANT]
> Doporučujeme pouze protokolovat typy událostí a uživatele požadované pro vaše účely auditování, aby se zajistilo, že výkon serveru nebude velmi ovlivněn.

Ve výchozím nastavení je protokol auditu zakázán. Pokud ho chcete povolit, nastavte na `audit_log_enabled` zapnuto.

Mezi další parametry, které můžete upravit, patří:

- `audit_log_events`: řídí události, které mají být protokolovány. Konkrétní události auditu najdete v níže uvedené tabulce.
- `audit_log_include_users`: MariaDB uživatelům, kteří mají být zahrnuti do protokolování. Výchozí hodnota pro tento parametr je prázdná, což bude zahrnovat všechny uživatele pro protokolování. Má vyšší prioritu `audit_log_exclude_users` . Maximální délka parametru je 512 znaků.
- `audit_log_exclude_users`: MariaDB uživatelům, kteří mají být vyloučení z protokolování. Umožňuje maximálně čtyři uživatele. Maximální délka parametru je 256 znaků.

> [!Note]
> `audit_log_include_users` má vyšší prioritu `audit_log_exclude_users` . Například pokud `audit_log_include_users`  =  `demouser` a `audit_log_exclude_users`  =  `demouser` , bude uživatel zahrnut v protokolech auditu, protože `audit_log_include_users` má vyšší prioritu.

| **Událostí** | **Popis** |
|---|---|
| `CONNECTION` | – Iniciování připojení (úspěšné nebo neúspěšné) <br> – Opakované ověření uživatele s jiným uživatelem nebo heslem během relace <br> – Ukončení připojení |
| `DML_SELECT`| VYBRAT dotazy |
| `DML_NONSELECT` | Vložit, odstranit nebo aktualizovat dotazy |
| `DML` | DML = DML_SELECT + DML_NONSELECT |
| `DDL` | Dotazy, jako je "DROP DATABASE" |
| `DCL` | Dotazy jako "udělení oprávnění" |
| `ADMIN` | Dotazy jako "Zobrazit stav" |
| `GENERAL` | Vše v DML_SELECT, DML_NONSELECT, DML, DDL, DCL a správce |

## <a name="access-audit-logs"></a>Přístup k protokolům auditu

Protokoly auditu se integrují s diagnostickými protokoly služby Azure Monitor. Po povolení protokolů auditu na serveru MariaDB je můžete posílat do protokolů služby Azure Monitor, služby Event Hubs nebo služby Azure Storage. Další informace o tom, jak povolit diagnostické protokoly v Azure Portal, najdete v [článku na portálu protokolu auditu](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs).

## <a name="diagnostic-logs-schemas"></a>Schémata protokolů diagnostiky

V následujících částech najdete popis toho, co má výstup MariaDB protokoly auditu na základě typu události. V závislosti na metodě Output se pole, která jsou součástí, a pořadí, ve kterém se zobrazují, můžou lišit.

### <a name="connection"></a>Připojení

| **Vlastnost** | **Popis** |
|---|---|
| `TenantId` | ID tenanta |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Časové razítko, kdy se protokol zaznamenal v UTC |
| `Type` | Typ protokolu Stál `AzureDiagnostics` |
| `SubscriptionId` | Identifikátor GUID předplatného, ke kterému server patří |
| `ResourceGroup` | Název skupiny prostředků, do které server patří |
| `ResourceProvider` | Název poskytovatele prostředků Stál `MICROSOFT.DBFORMARIADB` |
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
| `user_s` | Jméno uživatele, který provádí dotaz |
| `db_s` | Název databáze, ke které se připojuje |
| `\_ResourceId` | Identifikátor URI prostředku |

### <a name="general"></a>Obecné

Níže uvedené schéma se vztahuje na obecné, DML_SELECT, DML_NONSELECT, DML, DDL, DCL a typy událostí správce.

> [!NOTE]
> V případě `sql_text` se protokol zkrátí, pokud překračuje 2048 znaků.

| **Vlastnost** | **Popis** |
|---|---|
| `TenantId` | ID tenanta |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Časové razítko, kdy se protokol zaznamenal v UTC |
| `Type` | Typ protokolu Stál `AzureDiagnostics` |
| `SubscriptionId` | Identifikátor GUID předplatného, ke kterému server patří |
| `ResourceGroup` | Název skupiny prostředků, do které server patří |
| `ResourceProvider` | Název poskytovatele prostředků Stál `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Identifikátor URI prostředku |
| `Resource` | Název serveru |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Název serveru |
| `event_class_s` | `general_log` |
| `event_subclass_s` | `LOG`, `ERROR`, `RESULT` |
| `event_time` | Počet sekund zahájení dotazu v časovém razítku systému UNIX |
| `error_code_d` | Kód chyby, pokud se dotaz nezdařil. `0` Nejedná se o žádnou chybu |
| `thread_id_d` | ID vlákna, které provedlo dotaz |
| `host_s` | Funkce Blank |
| `ip_s` | IP adresa klienta připojujícího se k MariaDB |
| `user_s` | Jméno uživatele, který provádí dotaz |
| `sql_text_s` | Úplný text dotazu |
| `\_ResourceId` | Identifikátor URI prostředku |

## <a name="analyze-logs-in-azure-monitor-logs"></a>Analyzovat protokoly v protokolu Azure Monitor

Až budou protokoly auditu směrované do Azure Monitor protokolů prostřednictvím diagnostických protokolů, můžete provádět další analýzu auditovaných událostí. Tady je několik ukázkových dotazů, které vám pomůžou začít. Nezapomeňte níže uvedený název serveru aktualizovat.

- Vypsat Obecné události na konkrétním serveru

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs' and event_class_s == "general_log"
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last 
    ```

- Vypsat události připojení na konkrétním serveru

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs' and event_class_s == "connection_log"
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ```

- Sumarizace auditovaných událostí na konkrétním serveru

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | summarize count() by event_class_s, event_subclass_s, user_s, ip_s
    ```

- Nagrafuje distribuci typu události auditu na konkrétním serveru.

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart 
    ```

- Vypíše auditované události pro všechny servery MariaDB a diagnostické protokoly povolené pro protokoly auditu.

    ```kusto
    AzureDiagnostics
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ``` 

## <a name="next-steps"></a>Další kroky

- [Postup konfigurace protokolů auditu v Azure Portal](howto-configure-audit-logs-portal.md)