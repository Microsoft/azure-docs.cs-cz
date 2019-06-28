---
title: Protokoly auditu pro službu Azure Database for MySQL
description: Popisuje protokoly auditu dostupné ve službě Azure Database for MySQL a parametry dostupnými pro povolení úrovní protokolování.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: a82afe6f5299609fd6dd57a54f04f49fad5d2268
ms.sourcegitcommit: a7ea412ca4411fc28431cbe7d2cc399900267585
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2019
ms.locfileid: "67357646"
---
# <a name="audit-logs-in-azure-database-for-mysql"></a>Protokoly auditu v Azure Database for MySQL

Ve službě Azure Database for MySQL je k dispozici uživatelům v protokolu auditu. Protokolu auditu je možné sledovat aktivitu na úrovni databáze a se běžně používá pro dodržování předpisů.

> [!IMPORTANT]
> Funkce protokolu auditu je aktuálně ve verzi preview.

## <a name="configure-audit-logging"></a>Konfigurace protokolování auditu

Ve výchozím nastavení je zakázaný protokol auditu. Chcete-li ji povolit, nastavte `audit_log_enabled` na ON.

Další parametry, které můžete upravit patří:

- `audit_log_events`: Určuje události, které mají být protokolovány. Níže jsou uvedeny tabulce konkrétních událostí auditu.
- `audit_log_exclude_users`: Uživatelé MySQL mají být vyloučeny z protokolování. Povoluje maximálně čtyři uživatelé. Maximální délka parametru je 256 znaků.

| **Události** | **Popis** |
|---|---|
| `CONNECTION` | -Zahájení connection (úspěšných nebo neúspěšných) <br> – Opětovné ověření uživatele s různé uživatele a hesla během relace <br> – Ukončení připojení |
| `DML_SELECT`| Dotazů SELECT |
| `DML_NONSELECT` | Dotazy INSERT/DELETE/UPDATE |
| `DML` | DML = DML_SELECT + DML_NONSELECT |
| `DDL` | Dotazy, jako jsou "DROP DATABASE" |
| `DCL` | Dotazy, jako jsou "Udělit oprávnění" |
| `ADMIN` | Dotazy, jako jsou "Zobrazit stav" |
| `GENERAL` | Vše na DML_SELECT DML_NONSELECT, DML, DDL, DCL a správce |
| `TABLE_ACCESS` | – K dispozici pouze pro MySQL 5.7 <br> -Table další příkazy, jako je například výběr nebo INSERT INTO... SELECT <br> -Table delete příkazy, jako je například DELETE nebo TRUNCATE TABLE <br> -Příkazy insert table, jako je například vložení nebo nahrazení <br> -Příkazy update table, jako je například aktualizace |

## <a name="access-audit-logs"></a>Přístup k protokolům auditu

Protokoly auditu jsou integrované s Azure Monitor diagnostické protokoly. Po povolení protokolů auditu na váš server MySQL, můžete vygenerovat protokoly Azure monitoru, Event Hubs nebo Azure Storage. Další informace o tom, jak povolit diagnostické protokoly na webu Azure Portal, najdete v článku [portálu článku protokolu auditu](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs).

## <a name="diagnostic-logs-schemas"></a>Schémata pro diagnostické protokoly

Následující části popisují, co je výstupem MySQL protokolů auditování podle typu události. V závislosti na metodě výstup pole zahrnutá a pořadí, ve kterém jsou uvedeny se mohou lišit.

### <a name="connection"></a>Připojení

| **Vlastnost** | **Popis** |
|---|---|
| `TenantId` | Vaše ID tenanta |
| `SourceSystem` | `Azure` |
| `TimeGenerated` [UTC] | Časové razítko, kdy se přihlášení v protokolu ve standardu UTC |
| `Type` | Typ protokolu. Vždy `AzureDiagnostics` |
| `SubscriptionId` | Identifikátor GUID pro předplatné, které server patří do |
| `ResourceGroup` | Název skupiny prostředků, do které patří server |
| `ResourceProvider` | Název poskytovatele prostředků. Vždy `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Identifikátor URI prostředku |
| `Resource` | Název serveru |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `event_class` | `connection_log` |
| `event_subclass` | `CONNECT`, `DISCONNECT`, `CHANGE USER` (dostupné pouze pro MySQL 5.7) |
| `connection_id` | Připojení jedinečné ID vygenerované společností MySQL |
| `host` | Prázdné |
| `ip` | IP adresa klienta připojení k MySQL |
| `user` | Jméno uživatele, provádění dotazu |
| `db` | Název připojení k databázi |
| `\_ResourceId` | Identifikátor URI prostředku |

### <a name="general"></a>Obecné

Schéma níže se vztahuje na typy Obecné, DML_SELECT, DML_NONSELECT, DML, DDL, DCL a správu událostí.

| **Vlastnost** | **Popis** |
|---|---|
| `TenantId` | Vaše ID tenanta |
| `SourceSystem` | `Azure` |
| `TimeGenerated` [UTC] | Časové razítko, kdy se přihlášení v protokolu ve standardu UTC |
| `Type` | Typ protokolu. Vždy `AzureDiagnostics` |
| `SubscriptionId` | Identifikátor GUID pro předplatné, které server patří do |
| `ResourceGroup` | Název skupiny prostředků, do které patří server |
| `ResourceProvider` | Název poskytovatele prostředků. Vždy `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Identifikátor URI prostředku |
| `Resource` | Název serveru |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `event_class` | `general_log` |
| `event_subclass` | `LOG`, `ERROR`, `RESULT` (dostupné pouze pro MySQL 5.6) |
| `event_time` | Spuštění dotazu sekund v UNIXOVÉ časové razítko |
| `error_code` | Kód chyby, pokud dotaz selhal. `0` znamená, že žádná chybová zpráva |
| `thread_id` | ID vlákna, který spouští dotaz |
| `host` | Prázdné |
| `ip` | IP adresa klienta připojení k MySQL |
| `user` | Jméno uživatele, provádění dotazu |
| `sql_text` | Celý dotaz v textu |
| `\_ResourceId` | Identifikátor URI prostředku |

### <a name="table-access"></a>Přístup k tabulce

| **Vlastnost** | **Popis** |
|---|---|
| `TenantId` | Vaše ID tenanta |
| `SourceSystem` | `Azure` |
| `TimeGenerated` [UTC] | Časové razítko, kdy se přihlášení v protokolu ve standardu UTC |
| `Type` | Typ protokolu. Vždy `AzureDiagnostics` |
| `SubscriptionId` | Identifikátor GUID pro předplatné, které server patří do |
| `ResourceGroup` | Název skupiny prostředků, do které patří server |
| `ResourceProvider` | Název poskytovatele prostředků. Vždy `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Identifikátor URI prostředku |
| `Resource` | Název serveru |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `event_class` | `table_access_log` |
| `event_subclass` | `READ`, `INSERT`, `UPDATE`, nebo `DELETE` |
| `connection_id` | Připojení jedinečné ID vygenerované společností MySQL |
| `db` | Název databáze, získat přístup |
| `table` | Název tabulky přístup |
| `sql_text` | Celý dotaz v textu |
| `\_ResourceId` | Identifikátor URI prostředku |

## <a name="next-steps"></a>Další postup

- [Konfigurace protokolů auditu na portálu Azure portal](howto-configure-audit-logs-portal.md)