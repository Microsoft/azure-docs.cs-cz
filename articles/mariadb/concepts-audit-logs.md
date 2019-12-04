---
title: Protokoly auditu – Azure Database for MariaDB
description: V této části najdete popis protokolů auditu dostupných v Azure Database for MariaDB a dostupných parametrů pro povolení úrovní protokolování.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 64662499b4ee782bbf04e9e706cd659e84c90eec
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74773069"
---
# <a name="audit-logs-in-azure-database-for-mariadb"></a>Protokoly auditu v Azure Database for MariaDB

V Azure Database for MariaDB je k dispozici pro uživatele protokol auditu. Protokol auditu lze použít ke sledování aktivity na úrovni databáze a často se používá pro dodržování předpisů.

> [!IMPORTANT]
> Funkce protokolu auditu je momentálně ve verzi Preview.

## <a name="configure-audit-logging"></a>Konfigurace protokolování auditu

Ve výchozím nastavení je protokol auditu zakázán. Pokud ho chcete povolit, nastavte `audit_log_enabled` na ZAPNUTo.

Mezi další parametry, které můžete upravit, patří:

- `audit_log_events`: řídí události, které mají být protokolovány. Konkrétní události auditu najdete v níže uvedené tabulce.
- `audit_log_exclude_users`: MariaDB uživatele, kteří mají být vyloučeni z protokolování. Umožňuje maximálně čtyři uživatele. Maximální délka parametru je 256 znaků.

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

## <a name="access-audit-logs"></a>Přístup k protokolům auditu

Protokoly auditu jsou integrované s protokoly diagnostiky Azure Monitor. Jakmile povolíte protokoly auditu na serveru MariaDB, můžete je vygenerovat Azure Monitor protokolů, Event Hubs nebo Azure Storage. Další informace o tom, jak povolit diagnostické protokoly v Azure Portal, najdete v [článku na portálu protokolu auditu](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs).

## <a name="diagnostic-logs-schemas"></a>Schémata protokolů diagnostiky

V následujících částech najdete popis toho, co má výstup MariaDB protokoly auditu na základě typu události. V závislosti na metodě Output se pole, která jsou součástí, a pořadí, ve kterém se zobrazují, můžou lišit.

### <a name="connection"></a>Připojení

| **Vlastnost** | **Popis** |
|---|---|
| `TenantId` | ID tenanta |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Časové razítko, kdy se protokol zaznamenal v UTC |
| `Type` | Typ protokolu Vždycky `AzureDiagnostics` |
| `SubscriptionId` | Identifikátor GUID předplatného, ke kterému server patří |
| `ResourceGroup` | Název skupiny prostředků, do které server patří |
| `ResourceProvider` | Název poskytovatele prostředků Vždycky `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Identifikátor URI prostředku |
| `Resource` | Název serveru |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `event_class_s` | `connection_log` |
| `event_subclass_s` | `CONNECT`, `DISCONNECT` |
| `connection_id_d` | Jedinečné ID připojení generované MariaDB |
| `host_s` | Prázdné |
| `ip_s` | IP adresa klienta připojujícího se k MariaDB |
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
| `Type` | Typ protokolu Vždycky `AzureDiagnostics` |
| `SubscriptionId` | Identifikátor GUID předplatného, ke kterému server patří |
| `ResourceGroup` | Název skupiny prostředků, do které server patří |
| `ResourceProvider` | Název poskytovatele prostředků Vždycky `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Identifikátor URI prostředku |
| `Resource` | Název serveru |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Název serveru |
| `event_class_s` | `general_log` |
| `event_subclass_s` | `LOG`, `ERROR`, `RESULT` |
| `event_time` | Počet sekund zahájení dotazu v časovém razítku systému UNIX |
| `error_code_d` | Kód chyby, pokud se dotaz nezdařil. `0` neznamenají žádnou chybu. |
| `thread_id_d` | ID vlákna, které provedlo dotaz |
| `host_s` | Prázdné |
| `ip_s` | IP adresa klienta připojujícího se k MariaDB |
| `user_s` | Jméno uživatele, který provádí dotaz |
| `sql_text_s` | Úplný text dotazu |
| `\_ResourceId` | Identifikátor URI prostředku |

### <a name="table-access"></a>Přístup k tabulce

| **Vlastnost** | **Popis** |
|---|---|
| `TenantId` | ID tenanta |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Časové razítko, kdy se protokol zaznamenal v UTC |
| `Type` | Typ protokolu Vždycky `AzureDiagnostics` |
| `SubscriptionId` | Identifikátor GUID předplatného, ke kterému server patří |
| `ResourceGroup` | Název skupiny prostředků, do které server patří |
| `ResourceProvider` | Název poskytovatele prostředků Vždycky `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Identifikátor URI prostředku |
| `Resource` | Název serveru |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Název serveru |
| `event_class_s` | `table_access_log` |
| `event_subclass_s` | `READ`, `INSERT`, `UPDATE`nebo `DELETE` |
| `connection_id_d` | Jedinečné ID připojení generované MariaDB |
| `db_s` | Název databázového přistupu |
| `table_s` | Název přistupované tabulky |
| `sql_text_s` | Úplný text dotazu |
| `\_ResourceId` | Identifikátor URI prostředku |

## <a name="next-steps"></a>Další kroky

- [Postup konfigurace protokolů auditu v Azure Portal](howto-configure-audit-logs-portal.md)