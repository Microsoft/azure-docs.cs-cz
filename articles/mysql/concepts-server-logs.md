---
title: Protokoly pomalých dotazů – Azure Database for MySQL
description: Popisuje protokoly pomalých dotazů, které jsou k dispozici v Azure Database for MySQL, a dostupné parametry pro povolení různých úrovní protokolování.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 11/6/2020
ms.openlocfilehash: 1e967c77bc41f0f91674fe55bc622adaf5046f6d
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358996"
---
# <a name="slow-query-logs-in-azure-database-for-mysql"></a>Protokoly pomalých dotazů ve službě Azure Database for MySQL
V Azure Database for MySQL je k dispozici pro uživatele protokol pomalých dotazů. Přístup k transakčnímu protokolu není podporován. Protokol pomalých dotazů se dá použít k identifikaci problémových míst výkonu pro řešení problémů.

Další informace o protokolu pomalého dotazu MySQL najdete v [části protokol pomalých dotazů](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)v referenční příručce MySQL.

Když je na serveru povolené [úložiště dotazů](concepts-query-store.md) , můžou se v `CALL mysql.az_procedure_collect_wait_stats (900, 30);` protokolech pomalých dotazů zobrazovat dotazy, jako je protokol. Toto chování je očekávané, protože funkce úložiště dotazů shromažďuje statistiku o vašich dotazech. 

## <a name="configure-slow-query-logging"></a>Konfigurace pomalého protokolování dotazů 
Ve výchozím nastavení je protokol pomalého dotazu zakázán. Pokud ho chcete povolit, nastavte na `slow_query_log` zapnuto. Tato možnost se dá povolit pomocí Azure Portal nebo Azure CLI. 

Mezi další parametry, které můžete upravit, patří:

- **long_query_time** : Pokud dotaz trvá déle než long_query_time (v sekundách), dotaz se zaznamená do protokolu. Výchozí hodnota je 10 sekund.
- **log_slow_admin_statements** : Pokud on zahrnuje příkazy pro správu, jako je ALTER_TABLE a ANALYZE_TABLE v příkazech zapsaných do slow_query_log.
- **log_queries_not_using_indexes** : Určuje, zda jsou dotazy, které nepoužívají indexy, protokolovány do slow_query_log
- **log_throttle_queries_not_using_indexes** : Tento parametr omezuje počet neindexovaných dotazů, které lze zapsat do protokolu pomalých dotazů. Tento parametr se projeví, když je log_queries_not_using_indexes nastaveno na ZAPNUTo.
- **log_output** : Pokud "File", nástroj umožňuje zapsat protokol pomalých dotazů do úložiště místního serveru i do Azure monitor diagnostických protokolů. Pokud ho nastavíte na hodnotu None (Žádné), protokoly pomalých dotazů se budou zapisovat pouze do diagnostických protokolů služby Azure Monitor. 

> [!IMPORTANT]
> Pokud nejsou tabulky indexovány, `log_queries_not_using_indexes` může nastavení parametrů a na hodnotu `log_throttle_queries_not_using_indexes` on ovlivnit výkon MySQL, protože všechny dotazy běžící proti těmto neindexovaným tabulkám budou zapsány do protokolu pomalého dotazu.<br><br>
> Pokud plánujete protokolování pomalých dotazů po delší dobu, doporučuje se nastavit `log_output` na "žádný". Pokud je nastavená na "soubor", tyto protokoly se zapisují do úložiště místního serveru a můžou ovlivnit výkon MySQL. 

Úplný popis pomalých parametrů protokolu dotazů najdete v [dokumentaci k protokolu pomalého dotazů](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) MySQL.

## <a name="access-slow-query-logs"></a>Přístup k protokolům pomalým dotazům
K dispozici jsou dvě možnosti přístupu k protokolům pomalých dotazů v Azure Database for MySQL: místní úložiště serveru nebo protokoly diagnostiky Azure Monitor. Tento parametr je nastaven pomocí `log_output` parametru.

Pro úložiště na místním serveru můžete zobrazit a stáhnout protokoly pomalých dotazů pomocí Azure Portal nebo rozhraní příkazového řádku Azure CLI. V Azure Portal přejděte na server v Azure Portal. Pod hlavičkou **monitorování** vyberte stránku **protokoly serveru** . Další informace o rozhraní příkazového řádku Azure najdete v tématu [Konfigurace a přístup k protokolům pomalým dotazům pomocí Azure CLI](howto-configure-server-logs-in-cli.md). 

Protokoly diagnostiky Azure Monitor umožňují kanálu pomalých dotazů na protokoly Azure Monitor (Log Analytics), Azure Storage nebo Event Hubs. Další informace najdete [níže](concepts-server-logs.md#diagnostic-logs) .

## <a name="local-server-storage-log-retention"></a>Uchování protokolu úložiště místního serveru
Při protokolování do místního úložiště serveru jsou protokoly k dispozici po dobu až sedmi dnů od jejich vytvoření. Pokud celková velikost dostupných protokolů přesáhne 7 GB, pak se nejstarší soubory odstraní, dokud nebude k dispozici dostatek místa.

Protokoly se otočí každých 24 hodin nebo 7 GB, podle toho, co nastane dřív.

> [!Note]
> Výše uvedené uchování protokolu se nevztahuje na protokoly, které jsou v kanálu Azure Monitor diagnostické protokoly. Dobu uchování dat, která se emitují, můžete změnit (např. Azure Storage).

## <a name="diagnostic-logs"></a>Diagnostické protokoly
Azure Database for MySQL je integrován s protokoly diagnostiky Azure Monitor. Po povolení protokolů pomalých dotazů na serveru MySQL se můžete rozhodnout, že se mají vysílat Azure Monitor protokoly, Event Hubs nebo Azure Storage. Další informace o tom, jak povolit diagnostické protokoly, naleznete v části Postupy v [dokumentaci diagnostické protokoly](../azure-monitor/platform/platform-logs-overview.md).

Následující tabulka popisuje, co je v každém protokolu. V závislosti na metodě Output se pole, která jsou součástí, a pořadí, ve kterém se zobrazují, můžou lišit.

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
| `last_insert_id_s` | [last_insert_id](https://dev.mysql.com/doc/refman/8.0/en/information-functions.html#function_last-insert-id) |
| `insert_id_s` | Vložit ID |
| `sql_text_s` | Úplný dotaz |
| `server_id_s` | ID serveru |
| `thread_id_s` | ID vlákna |
| `\_ResourceId` | Identifikátor URI prostředku |

> [!Note]
> V případě `sql_text` se protokol zkrátí, pokud překračuje 2048 znaků.

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
- [Postup konfigurace protokolů pomalých dotazů z Azure Portal](howto-configure-server-logs-in-portal.md)
- [Jak nakonfigurovat protokoly pomalých dotazů z Azure CLI](howto-configure-server-logs-in-cli.md)