---
title: Protokoly serveru pro Azure Database for MySQL
description: Popisuje protokoly pomalých dotazů, které jsou k dispozici v Azure Database for MySQL, a dostupné parametry pro povolení různých úrovní protokolování.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: 4d801ada8fd8a8b35c71601d3ca274f26afb24f6
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262283"
---
# <a name="slow-query-logs-in-azure-database-for-mysql"></a>Pomalé dotazování protokolů v Azure Database for MySQL
V Azure Database for MySQL je k dispozici pro uživatele protokol pomalých dotazů. Přístup k transakčnímu protokolu není podporován. Protokol pomalých dotazů se dá použít k identifikaci problémových míst výkonu pro řešení problémů.

Další informace o protokolu pomalého dotazu MySQL najdete v [části protokol pomalých dotazů](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)v referenční příručce MySQL.

## <a name="access-slow-query-logs"></a>Přístup k protokolům pomalým dotazům
Pomocí Azure Portal a Azure CLI můžete zobrazit a stáhnout protokoly pomalých dotazů Azure Database for MySQL.

Na webu Azure Portal vyberte váš server Azure Database for MySQL. Pod hlavičkou **monitorování** vyberte stránku **protokoly serveru** .

Další informace o rozhraní příkazového řádku Azure najdete v tématu [Konfigurace a přístup k protokolům serveru pomocí Azure CLI](howto-configure-server-logs-in-cli.md).

## <a name="log-retention"></a>Uchovávání protokolů
Protokoly jsou k dispozici po dobu až sedmi dnů od jejich vytvoření. Pokud celková velikost dostupných protokolů přesáhne 7 GB, pak se nejstarší soubory odstraní, dokud nebude k dispozici dostatek místa. 

Protokoly se otočí každých 24 hodin nebo 7 GB, podle toho, co nastane dřív.

## <a name="configure-slow-query-logging"></a>Konfigurace pomalého protokolování dotazů 
Ve výchozím nastavení je protokol pomalého dotazu zakázán. Pokud ho chcete povolit, nastavte slow_query_log na ZAPNUTo.

Mezi další parametry, které můžete upravit, patří:

- **long_query_time**: Pokud dotaz trvá déle než long_query_time (v sekundách), dotaz se zaznamená do protokolu. Výchozí hodnota je 10 sekund.
- **log_slow_admin_statements**: Pokud on zahrnuje příkazy pro správu jako ALTER_TABLE a ANALYZE_TABLE v příkazech zapsaných do slow_query_log.
- **log_queries_not_using_indexes**: Určuje, zda jsou dotazy, které nepoužívají indexy, protokolovány do slow_query_log
- **log_throttle_queries_not_using_indexes**: Tento parametr omezuje počet neindexovaných dotazů, které lze zapsat do protokolu pomalých dotazů. Tento parametr se projeví, když je log_queries_not_using_indexes nastaveno na ZAPNUTo.

Úplný popis pomalých parametrů protokolu dotazů najdete v [dokumentaci k protokolu pomalého dotazů](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) MySQL.

## <a name="diagnostic-logs"></a>Diagnostické protokoly
Azure Database for MySQL je integrován s protokoly diagnostiky Azure Monitor. Po povolení protokolů pomalých dotazů na serveru MySQL se můžete rozhodnout, že se mají vysílat Azure Monitor protokoly, Event Hubs nebo Azure Storage. Další informace o tom, jak povolit diagnostické protokoly, naleznete v části Postupy v [dokumentaci diagnostické protokoly](../azure-monitor/platform/resource-logs-overview.md).

> [!IMPORTANT]
> Tato diagnostická funkce pro protokoly serveru je k dispozici pouze v Pro obecné účely a v paměťově optimalizovaných [cenových úrovních](concepts-pricing-tiers.md).

Následující tabulka popisuje, co je v každém protokolu. V závislosti na metodě Output se pole, která jsou součástí, a pořadí, ve kterém se zobrazují, můžou lišit.

| **Vlastnost** | **Popis** |
|---|---|
| `TenantId` | ID tenanta |
| `SourceSystem` | `Azure` |
| `TimeGenerated`UTC | Časové razítko, kdy se protokol zaznamenal v UTC |
| `Type` | Typ protokolu Vždy `AzureDiagnostics` |
| `SubscriptionId` | Identifikátor GUID předplatného, ke kterému server patří |
| `ResourceGroup` | Název skupiny prostředků, do které server patří |
| `ResourceProvider` | Název poskytovatele prostředků Vždy `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Identifikátor URI prostředku |
| `Resource` | Název serveru |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | Název serveru |
| `start_time_t`UTC | Čas, kdy dotaz začal |
| `query_time_s` | Celkový čas, kdy se dotaz trvalo spustit |
| `lock_time_s` | Celkový čas, kdy byl dotaz uzamčen |
| `user_host_s` | Uživatelské jméno |
| `rows_sent_s` | Počet odeslaných řádků |
| `rows_examined_s` | Počet testovaných řádků |
| `last_insert_id_s` | [last_insert_id](https://dev.mysql.com/doc/refman/8.0/en/information-functions.html#function_last-insert-id) |
| `insert_id_s` | Vložit ID |
| `sql_text_s` | Úplný dotaz |
| `server_id_s` | ID serveru |
| `thread_id_s` | ID vlákna |
| `\_ResourceId` | Identifikátor URI prostředku |

## <a name="next-steps"></a>Další kroky
- [Jak nakonfigurovat protokoly serveru a přistupovat k nim z Azure CLI](howto-configure-server-logs-in-cli.md).
