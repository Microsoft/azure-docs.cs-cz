---
title: Protokoly auditu – Azure Database for MySQL
description: V této části najdete popis protokolů auditu dostupných v Azure Database for MySQL a dostupných parametrů pro povolení úrovní protokolování.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: eae7e434ce21b5f9d9f3e6c40f94261df8baa426
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74972349"
---
# <a name="audit-logs-in-azure-database-for-mysql"></a>Protokoly auditu v Azure Database for MySQL

V Azure Database for MySQL je k dispozici pro uživatele protokol auditu. Protokol auditu lze použít ke sledování aktivity na úrovni databáze a často se používá pro dodržování předpisů.

> [!IMPORTANT]
> Funkce protokolu auditu je momentálně ve verzi Preview.

## <a name="configure-audit-logging"></a>Konfigurace protokolování auditu

Ve výchozím nastavení je protokol auditu zakázán. Pokud ho chcete povolit, nastavte `audit_log_enabled` na ZAPNUTo.

Mezi další parametry, které můžete upravit, patří:

- `audit_log_events`: řídí události, které mají být protokolovány. Konkrétní události auditu najdete v níže uvedené tabulce.
- `audit_log_include_users`: je potřeba zahrnout uživatele MySQL pro protokolování. Výchozí hodnota pro tento parametr je prázdná, což bude zahrnovat všechny uživatele pro protokolování. Má vyšší prioritu než `audit_log_exclude_users`. Maximální délka parametru je 512 znaků.
> [!Note]
> `audit_log_include_users` má vyšší prioritu než `audit_log_exclude_users`. Pokud například `audit_log_include_users` = `demouser` a `audit_log_exclude_users` = `demouser`, bude uživatel zahrnut do protokolů auditu, protože `audit_log_include_users` má vyšší prioritu.
- `audit_log_exclude_users`: uživatelé MySQL budou vyloučeni z protokolování. Maximální délka parametru je 512 znaků.

> [!Note]
> V případě `sql_text`se protokol zkrátí, pokud překračuje 2048 znaků.

| **Události** | **Popis** |
|---|---|
| `CONNECTION` | – Iniciování připojení (úspěšné nebo neúspěšné) <br> – Opakované ověření uživatele s jiným uživatelem nebo heslem během relace <br> – Ukončení připojení |
| `DML_SELECT`| VYBRAT dotazy |
| `DML_NONSELECT` | Vložit, odstranit nebo aktualizovat dotazy |
| `DML` | DML = DML_SELECT + DML_NONSELECT |
| `DDL` | Dotazy, jako je "DROP DATABASE" |
| `DCL` | Dotazy jako "udělení oprávnění" |
| `ADMIN` | Dotazy jako "Zobrazit stav" |
| `GENERAL` | Vše v DML_SELECT, DML_NONSELECT, DML, DDL, DCL a správce |
| `TABLE_ACCESS` | – Dostupné jenom pro MySQL 5,7 <br> – Příkazy pro čtení z tabulky, jako je například SELECT nebo INSERT INTO... VYBRALI <br> – Příkazy DELETE v tabulce, jako je například DELETE nebo TRUNCATE TABLE <br> – Příkazy INSERT v tabulce, jako je INSERT nebo Replace <br> – Příkazy aktualizace tabulky, jako je například aktualizace |

## <a name="access-audit-logs"></a>Přístup k protokolům auditu

Protokoly auditu jsou integrované s protokoly diagnostiky Azure Monitor. Po povolení protokolů auditu na serveru MySQL je můžete vygenerovat k Azure Monitor protokolů, Event Hubs nebo Azure Storage. Další informace o tom, jak povolit diagnostické protokoly v Azure Portal, najdete v [článku na portálu protokolu auditu](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs).

## <a name="diagnostic-logs-schemas"></a>Schémata protokolů diagnostiky

V následujících částech najdete popis toho, co je výstupem protokolů auditu MySQL na základě typu události. V závislosti na metodě Output se pole, která jsou součástí, a pořadí, ve kterém se zobrazují, můžou lišit.

### <a name="connection"></a>Připojení

| **Vlastnost** | **Popis** |
|---|---|
| `TenantId` | ID tenanta |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Časové razítko, kdy se protokol zaznamenal v UTC |
| `Type` | Typ protokolu Vždy `AzureDiagnostics` |
| `SubscriptionId` | Identifikátor GUID předplatného, ke kterému server patří |
| `ResourceGroup` | Název skupiny prostředků, do které server patří |
| `ResourceProvider` | Název poskytovatele prostředků Vždy `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Identifikátor URI prostředku |
| `Resource` | Název serveru |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Název serveru |
| `event_class_s` | `connection_log` |
| `event_subclass_s` | `CONNECT`, `DISCONNECT``CHANGE USER` (k dispozici pouze pro MySQL 5,7) |
| `connection_id_d` | Jedinečné ID připojení generované MySQL |
| `host_s` | Prázdné |
| `ip_s` | IP adresa klienta připojujícího se k MySQL |
| `user_s` | Jméno uživatele, který provádí dotaz |
| `db_s` | Název databáze, ke které se připojuje |
| `\_ResourceId` | Identifikátor URI prostředku |

### <a name="general"></a>Obecné

Níže uvedené schéma se vztahuje na obecné, DML_SELECT, DML_NONSELECT, DML, DDL, DCL a typy událostí správce.

| **Vlastnost** | **Popis** |
|---|---|
| `TenantId` | ID tenanta |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Časové razítko, kdy se protokol zaznamenal v UTC |
| `Type` | Typ protokolu Vždy `AzureDiagnostics` |
| `SubscriptionId` | Identifikátor GUID předplatného, ke kterému server patří |
| `ResourceGroup` | Název skupiny prostředků, do které server patří |
| `ResourceProvider` | Název poskytovatele prostředků Vždy `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Identifikátor URI prostředku |
| `Resource` | Název serveru |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Název serveru |
| `event_class_s` | `general_log` |
| `event_subclass_s` | `LOG`, `ERROR``RESULT` (k dispozici pouze pro MySQL 5,6) |
| `event_time` | Čas spuštění dotazu v časovém razítku UTC |
| `error_code_d` | Kód chyby, pokud se dotaz nezdařil. `0` neznamenají žádnou chybu. |
| `thread_id_d` | ID vlákna, které provedlo dotaz |
| `host_s` | Prázdné |
| `ip_s` | IP adresa klienta připojujícího se k MySQL |
| `user_s` | Jméno uživatele, který provádí dotaz |
| `sql_text_s` | Úplný text dotazu |
| `\_ResourceId` | Identifikátor URI prostředku |

### <a name="table-access"></a>Přístup k tabulce

| **Vlastnost** | **Popis** |
|---|---|
| `TenantId` | ID tenanta |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Časové razítko, kdy se protokol zaznamenal v UTC |
| `Type` | Typ protokolu Vždy `AzureDiagnostics` |
| `SubscriptionId` | Identifikátor GUID předplatného, ke kterému server patří |
| `ResourceGroup` | Název skupiny prostředků, do které server patří |
| `ResourceProvider` | Název poskytovatele prostředků Vždy `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Identifikátor URI prostředku |
| `Resource` | Název serveru |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Název serveru |
| `event_class_s` | `table_access_log` |
| `event_subclass_s` | `READ`, `INSERT`, `UPDATE` nebo `DELETE` |
| `connection_id_d` | Jedinečné ID připojení generované MySQL |
| `db_s` | Název databázového přistupu |
| `table_s` | Název přistupované tabulky |
| `sql_text_s` | Úplný text dotazu |
| `\_ResourceId` | Identifikátor URI prostředku |

## <a name="next-steps"></a>Další kroky

- [Postup konfigurace protokolů auditu v Azure Portal](howto-configure-audit-logs-portal.md)