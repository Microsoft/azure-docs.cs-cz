---
title: Protokoly auditu pro službu Azure Database pro MariaDB
description: Popisuje protokoly auditu dostupné ve službě Azure Database pro MariaDB a parametry dostupnými pro povolení úrovní protokolování.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 765db8461465b74ac068782c1b91d3c68b73f7d4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079519"
---
# <a name="audit-logs-in-azure-database-for-mariadb"></a>Protokoly auditu v Azure Database pro MariaDB

Ve službě Azure Database pro MariaDB protokolu auditování je dostupné pro uživatele. Protokolu auditu je možné sledovat aktivitu na úrovni databáze a se běžně používá pro dodržování předpisů.

> [!IMPORTANT]
> Funkce protokolu auditu je aktuálně ve verzi preview.

## <a name="configure-audit-logging"></a>Konfigurace protokolování auditu

Ve výchozím nastavení je zakázaný protokol auditu. Chcete-li ji povolit, nastavte `audit_log_enabled` na ON.

Další parametry, které můžete upravit patří:

- `audit_log_events`: Určuje události, které mají být protokolovány. Níže jsou uvedeny tabulce konkrétních událostí auditu.
- `audit_log_exclude_users`: MariaDB uživatelé mají být vyloučeny z protokolování. Povoluje maximálně čtyři uživatelé. Maximální délka parametru je 256 znaků.

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

## <a name="access-audit-logs"></a>Přístup k protokolům auditu

Protokoly auditu jsou integrované s Azure Monitor diagnostické protokoly. Po povolení protokolů auditu na vašem serveru MariaDB, můžete vygenerovat protokoly Azure monitoru, Event Hubs nebo Azure Storage. Další informace o tom, jak povolit diagnostické protokoly na webu Azure Portal, najdete v článku [portálu článku protokolu auditu](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs).

## <a name="schemas"></a>Schémata

Následující části popisují, co je výstupem MariaDB protokolů auditování podle typu události. V závislosti na metodě výstup pole zahrnutá a pořadí, ve kterém jsou uvedeny se mohou lišit.

### <a name="connection"></a>Připojení

| **Vlastnost** | **Popis** |
|---|---|
| `TenantId` | Vaše ID tenanta |
| `SourceSystem` | `Azure` |
| `TimeGenerated` [UTC] | Časové razítko, kdy se přihlášení v protokolu ve standardu UTC |
| `Type` | Typ protokolu. Vždy `AzureDiagnostics` |
| `SubscriptionId` | Identifikátor GUID pro předplatné, které server patří do |
| `ResourceGroup` | Název skupiny prostředků, do které patří server |
| `ResourceProvider` | Název poskytovatele prostředků. Vždy `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Identifikátor URI prostředku |
| `Resource` | Název serveru |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `event_class` | `connection_log` |
| `event_subclass` | `CONNECT`, `DISCONNECT` |
| `connection_id` | Připojení jedinečné ID vygenerované společností MariaDB |
| `host` | Prázdné |
| `ip` | IP adresa připojení klienta k MariaDB |
| `user` | Jméno uživatele, provádění dotazu |
| `db` | Název připojení k databázi |
| `\_ResourceId` | Identifikátor URI prostředku |

### <a name="general"></a>Obecné

Schéma níže se vztahuje na typy Obecné, DML_SELECT, DML_NONSELECT, DML, DDL, DCL a správu událostí.

| **Vlastnost** | **Popis** |
|---|---|
| `TenantId` | Vaše ID tenanta |
| `SourceSystem` | `Azure` |
| `TimeGenerated` [UTC] | Časové razítko při cyrilice c protokolu byla zaznamenána v UTC |
| `Type` | Typ protokolu. Vždy `AzureDiagnostics` |
| `SubscriptionId` | Identifikátor GUID pro předplatné, které server patří do |
| `ResourceGroup` | Název skupiny prostředků, do které patří server |
| `ResourceProvider` | Název poskytovatele prostředků. Vždy `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Identifikátor URI prostředku |
| `Resource` | Název serveru |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `event_class` | `general_log` |
| `event_subclass` | `LOG`, `ERROR`, `RESULT` |
| `event_time` | Spuštění dotazu sekund v UNIXOVÉ časové razítko |
| `error_code` | Kód chyby, pokud dotaz selhal. `0` znamená, že žádná chybová zpráva |
| `thread_id` | ID vlákna, který spouští dotaz |
| `host` | Prázdné |
| `ip` | IP adresa připojení klienta k MariaDB |
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
| `ResourceProvider` | Název poskytovatele prostředků. Vždy `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Identifikátor URI prostředku |
| `Resource` | Název serveru |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `event_class` | `table_access_log` |
| `event_subclass` | `READ`, `INSERT`, `UPDATE`, nebo `DELETE` |
| `connection_id` | Připojení jedinečné ID vygenerované společností MariaDB |
| `db` | Název databáze, získat přístup |
| `table` | Název tabulky přístup |
| `sql_text` | Celý dotaz v textu |
| `\_ResourceId` | Identifikátor URI prostředku |

## <a name="next-steps"></a>Další postup

- [Konfigurace protokolů auditu na portálu Azure portal](howto-configure-audit-logs-portal.md)