---
title: Protokoly auditu – Azure Database for MySQL – flexibilní Server
description: Popisuje protokoly auditu, které jsou k dispozici v Azure Database for MySQL flexibilním serveru.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 9/21/2020
ms.openlocfilehash: 1232a0753c988f5a28ebba28f9819aa67ce28603
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101718739"
---
# <a name="track-database-activity-with-audit-logs-in-azure-database-for-mysql-flexible-server"></a>Sledovat činnost databáze pomocí protokolů auditu v Azure Database for MySQL flexibilním serveru

> [!IMPORTANT] 
> Azure Database for MySQL flexibilní Server je momentálně ve verzi Public Preview.

Azure Database for MySQL flexibilní Server poskytuje uživatelům možnost konfigurovat protokoly auditu. Protokoly auditu lze použít ke sledování aktivity na úrovni databáze, včetně událostí připojení, správce, DDL a DML. Tyto typy protokolů se běžně používají pro účely dodržování předpisů.

## <a name="configure-audit-logging"></a>Konfigurace protokolování auditu

>[!IMPORTANT]
> Doporučujeme pouze protokolovat typy událostí a uživatele požadované pro vaše účely auditování, aby se zajistilo, že výkon serveru nebude velmi ovlivněn.

Ve výchozím nastavení jsou protokoly auditu zakázané. Pokud je chcete povolit, nastavte `audit_log_enabled` parametr serveru na *zapnuto*. Dá se nakonfigurovat pomocí Azure Portal nebo Azure CLI. <!-- add link to server parameter-->. 

Další parametry, které můžete upravit pro řízení chování protokolování auditu, zahrnují:

- `audit_log_events`: řídí události, které mají být protokolovány. Konkrétní události auditu najdete v níže uvedené tabulce.
- `audit_log_include_users`: Uživatelé MySQL budou zahrnuti do protokolování. Výchozí hodnota pro tento parametr je prázdná, což bude zahrnovat všechny uživatele pro protokolování. Má vyšší prioritu `audit_log_exclude_users` . Maximální délka parametru je 512 znaků.
- `audit_log_exclude_users`: Uživatelé MySQL budou vyloučeni z protokolování. Maximální délka parametru je 512 znaků.

> [!NOTE]
> `audit_log_include_users` má vyšší prioritu `audit_log_exclude_users` . Například pokud `audit_log_include_users`  =  `demouser` a `audit_log_exclude_users`  =  `demouser` , bude uživatel zahrnut v protokolech auditu, protože `audit_log_include_users` má vyšší prioritu.

| **Událost** | **Popis** |
|---|---|
| `CONNECTION` | – Iniciování připojení (úspěšné nebo neúspěšné) <br> – Opakované ověření uživatele s jiným uživatelem nebo heslem během relace <br> – Ukončení připojení |
| `DML_SELECT`| VYBRAT dotazy |
| `DML_NONSELECT` | Vložit, odstranit nebo aktualizovat dotazy |
| `DML` | DML = DML_SELECT + DML_NONSELECT |
| `DDL` | Dotazy, jako je "DROP DATABASE" |
| `DCL` | Dotazy jako "udělení oprávnění" |
| `ADMIN` | Dotazy jako "Zobrazit stav" |
| `GENERAL` | Vše v DML_SELECT, DML_NONSELECT, DML, DDL, DCL a správce |
| `TABLE_ACCESS` | – Příkazy pro čtení z tabulky, jako je například SELECT nebo INSERT INTO... VYBRALI <br> – Příkazy DELETE v tabulce, jako je například DELETE nebo TRUNCATE TABLE <br> – Příkazy INSERT v tabulce, jako je INSERT nebo Replace <br> – Příkazy aktualizace tabulky, jako je například aktualizace |

## <a name="access-audit-logs"></a>Přístup k protokolům auditu

Protokoly auditu jsou integrované s nastavením diagnostiky Azure Monitor. Po povolení protokolů auditu na flexibilním serveru MySQL je můžete vygenerovat k Azure Monitor protokolů, Event Hubs nebo Azure Storage. Další informace o nastavení diagnostiky najdete v [dokumentaci k diagnostickým protokolům](../../azure-monitor/essentials/platform-logs-overview.md). Další informace o tom, jak povolit nastavení diagnostiky v Azure Portal, najdete v [článku na portálu protokolu auditu](how-to-configure-audit-logs-portal.md#set-up-diagnostics).

Následující části popisují výstup protokolů auditu MySQL na základě typu události. V závislosti na metodě Output se pole, která jsou součástí, a pořadí, ve kterém se zobrazují, můžou lišit.

### <a name="connection"></a>Připojení

| **Vlastnost** | **Popis** |
|---|---|
| `TenantId` | ID tenanta |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Časové razítko, kdy se protokol zaznamenal v UTC |
| `Type` | Typ protokolu Stál `AzureDiagnostics` |
| `SubscriptionId` | Identifikátor GUID předplatného, ke kterému server patří |
| `ResourceGroup` | Název skupiny prostředků, do které server patří |
| `ResourceProvider` | Název poskytovatele prostředků Stál `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Identifikátor URI prostředku |
| `Resource` | Název serveru |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Název serveru |
| `event_class_s` | `connection_log` |
| `event_subclass_s` | `CONNECT`, `DISCONNECT`, `CHANGE USER` |
| `connection_id_d` | Jedinečné ID připojení generované MySQL |
| `host_s` | Funkce Blank |
| `ip_s` | IP adresa klienta připojujícího se k MySQL |
| `user_s` | Jméno uživatele, který provádí dotaz |
| `db_s` | Název databáze, ke které se připojuje |
| `\_ResourceId` | Identifikátor URI prostředku |

### <a name="general"></a>Obecné

Níže uvedené schéma se vztahuje na obecné, DML_SELECT, DML_NONSELECT, DML, DDL, DCL a typy událostí správce.

> [!NOTE]
> V případě `sql_text_s` se protokol zkrátí, pokud překračuje 2048 znaků.

| **Vlastnost** | **Popis** |
|---|---|
| `TenantId` | ID tenanta |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Časové razítko, kdy se protokol zaznamenal v UTC |
| `Type` | Typ protokolu Stál `AzureDiagnostics` |
| `SubscriptionId` | Identifikátor GUID předplatného, ke kterému server patří |
| `ResourceGroup` | Název skupiny prostředků, do které server patří |
| `ResourceProvider` | Název poskytovatele prostředků Stál `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Identifikátor URI prostředku |
| `Resource` | Název serveru |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Název serveru |
| `event_class_s` | `general_log` |
| `event_subclass_s` | `LOG`, `ERROR` , `RESULT` (k dispozici pouze pro MySQL 5,6) |
| `event_time` | Čas spuštění dotazu v časovém razítku UTC |
| `error_code_d` | Kód chyby, pokud se dotaz nezdařil. `0` Nejedná se o žádnou chybu |
| `thread_id_d` | ID vlákna, které provedlo dotaz |
| `host_s` | Funkce Blank |
| `ip_s` | IP adresa klienta připojujícího se k MySQL |
| `user_s` | Jméno uživatele, který provádí dotaz |
| `sql_text_s` | Úplný text dotazu |
| `\_ResourceId` | Identifikátor URI prostředku |

### <a name="table-access"></a>Přístup k tabulce

> [!NOTE]
> V případě `sql_text_s` se protokol zkrátí, pokud překračuje 2048 znaků.

| **Vlastnost** | **Popis** |
|---|---|
| `TenantId` | ID tenanta |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Časové razítko, kdy se protokol zaznamenal v UTC |
| `Type` | Typ protokolu Stál `AzureDiagnostics` |
| `SubscriptionId` | Identifikátor GUID předplatného, ke kterému server patří |
| `ResourceGroup` | Název skupiny prostředků, do které server patří |
| `ResourceProvider` | Název poskytovatele prostředků Stál `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Identifikátor URI prostředku |
| `Resource` | Název serveru |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Název serveru |
| `event_class_s` | `table_access_log` |
| `event_subclass_s` | `READ`, `INSERT` , `UPDATE` nebo `DELETE` |
| `connection_id_d` | Jedinečné ID připojení generované MySQL |
| `db_s` | Název databázového přistupu |
| `table_s` | Název přistupované tabulky |
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

- Vypíše auditované události na všech serverech MySQL s povolenými diagnostickými protokoly v protokolech auditu.

    ```kusto
    AzureDiagnostics
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ``` 

## <a name="next-steps"></a>Další kroky
- Další informace o [protokolech pomalých dotazů](concepts-slow-query-logs.md)
- Konfigurace protokolů dotazů auditu z [Azure Portal](how-to-configure-audit-logs-portal.md)
<!-- - [How to configure audit logs in the Azure portal](howto-configure-audit-logs-portal.md)-->
