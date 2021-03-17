---
title: Protokoly auditu – Azure Database for MySQL
description: V této části najdete popis protokolů auditu dostupných v Azure Database for MySQL a dostupných parametrů pro povolení úrovní protokolování.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 6/24/2020
ms.openlocfilehash: fa845e7c402073a64f51d75b1da51f56142eee2e
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2021
ms.locfileid: "98630494"
---
# <a name="audit-logs-in-azure-database-for-mysql"></a>Protokoly auditu v Azure Database for MySQL

V Azure Database for MySQL je k dispozici pro uživatele protokol auditu. Protokol auditu lze použít ke sledování aktivity na úrovni databáze a často se používá pro dodržování předpisů.

## <a name="configure-audit-logging"></a>Konfigurace protokolování auditu

>[!IMPORTANT]
> Doporučujeme pouze protokolovat typy událostí a uživatele požadované pro vaše účely auditování, aby se zajistilo, že výkon serveru nebude velmi ovlivněn.

Ve výchozím nastavení je protokol auditu zakázán. Pokud ho chcete povolit, nastavte na `audit_log_enabled` zapnuto.

Mezi další parametry, které můžete upravit, patří:

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
| `TABLE_ACCESS` | – K dispozici pro MySQL 5,7 a MySQL 8,0 <br> – Příkazy pro čtení z tabulky, jako je například SELECT nebo INSERT INTO... VYBRALI <br> – Příkazy DELETE v tabulce, jako je například DELETE nebo TRUNCATE TABLE <br> – Příkazy INSERT v tabulce, jako je INSERT nebo Replace <br> – Příkazy aktualizace tabulky, jako je například aktualizace |

## <a name="access-audit-logs"></a>Přístup k protokolům auditu

Protokoly auditu se integrují s diagnostickými protokoly služby Azure Monitor. Po povolení protokolů auditu na serveru MySQL je můžete posílat do protokolů služby Azure Monitor, služby Event Hubs nebo služby Azure Storage. Další informace o tom, jak povolit diagnostické protokoly v Azure Portal, najdete v [článku na portálu protokolu auditu](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs).

## <a name="diagnostic-logs-schemas"></a>Schémata protokolů diagnostiky

V následujících částech najdete popis toho, co je výstupem protokolů auditu MySQL na základě typu události. V závislosti na metodě Output se pole, která jsou součástí, a pořadí, ve kterém se zobrazují, můžou lišit.

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
| `event_subclass_s` | `CONNECT`, `DISCONNECT` , `CHANGE USER` (k dispozici pouze pro MySQL 5,7) |
| `connection_id_d` | Jedinečné ID připojení generované MySQL |
| `host_s` | Funkce Blank |
| `ip_s` | IP adresa klienta připojujícího se k MySQL |
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
> Protokoly přístupu k tabulce jsou pouze výstupem pro MySQL 5,7.<br>V případě `sql_text` se protokol zkrátí, pokud překračuje 2048 znaků.

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

- [Postup konfigurace protokolů auditu v Azure Portal](howto-configure-audit-logs-portal.md)
