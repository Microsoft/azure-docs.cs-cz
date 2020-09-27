---
title: Protokoly pomalých dotazů – Azure Database for MySQL-flexibilní Server
description: Popisuje protokoly pomalých dotazů, které jsou k dispozici v Azure Database for MySQL flexibilním serveru.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 9/21/2020
ms.openlocfilehash: 512a103f5ac116f1f4eb18631cdc8e4a8b9380c9
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2020
ms.locfileid: "91399206"
---
# <a name="slow-query-logs-in-azure-database-for-mysql-flexible-server-preview"></a>Pomalé dotazy v protokolech v Azure Database for MySQL flexibilním serveru (Preview)

> [!IMPORTANT] 
> Azure Database for MySQL – flexibilní Server je momentálně ve verzi Public Preview.

V Azure Database for MySQL flexibilním serveru je protokol pomalých dotazů dostupný uživatelům, aby mohli konfigurovat a přistupovat. Pomalé protokoly dotazů jsou ve výchozím nastavení zakázané a můžou být povolené, aby při řešení potíží pomohly identifikovat problém s výkonem.

Další informace o protokolu pomalého dotazování MySQL najdete v [části pomalého protokolu dotazů](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) v dokumentaci k modulu MySQL.

## <a name="configure-slow-query-logging"></a>Konfigurace pomalého protokolování dotazů 
Ve výchozím nastavení je protokol pomalého dotazu zakázán. Pokud chcete protokoly povolit, nastavte `slow_query_log` parametr serveru na *zapnuto*. Dá se nakonfigurovat pomocí Azure Portal nebo Azure CLI. <!-- add link to server parameter-->. 

Další parametry, které můžete upravit pro řízení chování pomalého protokolování dotazů, zahrnují:

- **long_query_time**: Zaprotokolujte dotaz, pokud trvá dokončení déle než `long_query_time` (v sekundách). Výchozí hodnota je 10 sekund.
- **log_slow_admin_statements**: Určuje, zda jsou příkazy správy (např. `ALTER_TABLE`, `ANALYZE_TABLE` ) jsou protokolovány.
- **log_queries_not_using_indexes**: Určuje, zda jsou protokolovány dotazy, které nepoužívají indexy.
- **log_throttle_queries_not_using_indexes**: omezuje počet neindexovaných dotazů, které lze zapsat do protokolu pomalých dotazů. Tento parametr se projeví `log_queries_not_using_indexes` , pokud je nastaven na hodnotu *on* .

> [!IMPORTANT]
> Pokud nejsou tabulky indexovány, `log_queries_not_using_indexes` může nastavení parametrů a na hodnotu `log_throttle_queries_not_using_indexes` **on** ovlivnit výkon MySQL, protože všechny dotazy běžící proti těmto neindexovaným tabulkám budou zapsány do protokolu pomalého dotazu.

Úplný popis pomalých parametrů protokolu dotazů najdete v [dokumentaci k protokolu pomalého dotazů](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) MySQL.

## <a name="access-slow-query-logs"></a>Přístup k protokolům pomalým dotazům

Pomalé protokoly dotazů jsou integrované s Azure Monitor nastavení diagnostiky. Po povolení protokolů pomalých dotazů na flexibilním serveru MySQL je můžete vygenerovat, abyste Azure Monitor protokoly, Event Hubs nebo Azure Storage. Další informace o nastavení diagnostiky najdete v [dokumentaci k diagnostickým protokolům](../../azure-monitor/platform/platform-logs-overview.md). Další informace o tom, jak povolit nastavení diagnostiky v Azure Portal, najdete v [článku pomalým na portálu pro protokol dotazů](how-to-configure-slow-query-logs-portal.md#set-up-diagnostics).

Následující tabulka popisuje výstup pomalého protokolu dotazů. V závislosti na metodě Output se pole, která jsou součástí, a pořadí, ve kterém se zobrazují, můžou lišit.

| **Vlastnost** | **Popis** |
|---|---|
| `TenantId` | ID tenanta |
| `SourceSystem` | `Azure` |
| `TimeGenerated` UTC | Časové razítko, kdy se protokol zaznamenal v UTC |
| `Type` | Typ protokolu Stál `AzureDiagnostics` |
| `SubscriptionId` | Identifikátor GUID předplatného, ke kterému server patří |
| `ResourceGroup` | Název skupiny prostředků, do které server patří |
| `ResourceProvider` | Název poskytovatele prostředků Stál `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Identifikátor URI prostředku |
| `Resource` | Název serveru |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | Název serveru |
| `start_time_t` UTC | Čas, kdy dotaz začal |
| `query_time_s` | Celková doba v sekundách, po kterou se dotaz trvalo spustit |
| `lock_time_s` | Celková doba v sekundách, po kterou byl dotaz uzamčen |
| `user_host_s` | Uživatelské jméno |
| `rows_sent_s` | Počet odeslaných řádků |
| `rows_examined_s` | Počet testovaných řádků |
| `last_insert_id_s` | [last_insert_id](https://dev.mysql.com/doc/refman/5.7/en/information-functions.html#function_last-insert-id) |
| `insert_id_s` | Vložit ID |
| `sql_text_s` | Úplný dotaz |
| `server_id_s` | ID serveru |
| `thread_id_s` | ID vlákna |
| `\_ResourceId` | Identifikátor URI prostředku |

> [!Note]
> V případě `sql_text_s` se protokol zkrátí, pokud překračuje 2048 znaků.

## <a name="analyze-logs-in-azure-monitor-logs"></a>Analyzovat protokoly v protokolu Azure Monitor

Až budou protokoly pomalých dotazů směrované do Azure Monitor protokolů prostřednictvím diagnostických protokolů, můžete provádět další analýzu pomalých dotazů. Tady je několik ukázkových dotazů, které vám pomůžou začít. Nezapomeňte níže uvedený název serveru aktualizovat.

- Dotazy delší než 10 sekund na konkrétním serveru

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```

- Vypsat prvních 5 nejdelších dotazů na konkrétní server

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | order by query_time_d desc
    | take 5
    ```

- Sumarizace pomalých dotazů podle minimálního, maximálního, průměrného a standardního času dotazu na konkrétní server

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count(), min(query_time_d), max(query_time_d), avg(query_time_d), stdev(query_time_d), percentile(query_time_d, 95) by LogicalServerName_s
    ```

- Nagrafování pomalé distribuce dotazů na konkrétním serveru

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart
    ```

- Zobrazit dotazy delší než 10 sekund napříč všemi servery MySQL s povolenými diagnostickými protokoly

    ```Kusto
    AzureDiagnostics
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```    
    
## <a name="next-steps"></a>Další kroky
- Další informace o [protokolech auditu](concepts-audit-logs.md)
- Konfigurace pomalých protokolů dotazů z [Azure Portal](how-to-configure-slow-query-logs-portal.md)
<!-- - [How to configure slow query logs from the Azure CLI](howto-configure-server-logs-in-cli.md). -->