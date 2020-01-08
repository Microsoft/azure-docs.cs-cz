---
title: Protokoly pomalých dotazů – Azure Database for MariaDB
description: V této části najdete popis protokolů dostupných v Azure Database for MariaDB a dostupných parametrů pro povolení různých úrovní protokolování.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 20e521c36db0ab98e4b11d948516ccd04b5f5fdd
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75432027"
---
# <a name="slow-query-logs-in-azure-database-for-mariadb"></a>Pomalé dotazování protokolů v Azure Database for MariaDB
V Azure Database for MariaDB je k dispozici pro uživatele protokol pomalých dotazů. Přístup k transakčnímu protokolu není podporován. Protokol pomalých dotazů se dá použít k identifikaci problémových míst výkonu pro řešení problémů.

Další informace o protokolu pomalých dotazů najdete v dokumentaci k MariaDB pro [protokol pomalých dotazů](https://mariadb.com/kb/en/library/slow-query-log-overview/).

## <a name="access-slow-query-logs"></a>Přístup k protokolům pomalým dotazům
Pomocí Azure Portal a Azure CLI můžete zobrazit a stáhnout protokoly pomalých dotazů Azure Database for MariaDB.

V Azure Portal vyberte server Azure Database for MariaDB. Pod hlavičkou **monitorování** vyberte stránku **protokoly serveru** .

Další informace o rozhraní příkazového řádku Azure najdete v tématu [Konfigurace a přístup k protokolům serveru pomocí Azure CLI](howto-configure-server-logs-cli.md).

Podobně můžete protokoly přesměrovat na Azure Monitor pomocí diagnostických protokolů. Další informace najdete [níže](concepts-server-logs.md#diagnostic-logs) .

## <a name="log-retention"></a>Uchovávání protokolů
Protokoly jsou k dispozici po dobu až sedmi dnů od jejich vytvoření. Pokud celková velikost dostupných protokolů přesáhne 7 GB, pak se nejstarší soubory odstraní, dokud nebude k dispozici dostatek místa.

Protokoly se otočí každých 24 hodin nebo 7 GB, podle toho, co nastane dřív.

## <a name="configure-slow-query-logging"></a>Konfigurace pomalého protokolování dotazů
Ve výchozím nastavení je protokol pomalého dotazu zakázán. Pokud ho chcete povolit, nastavte slow_query_log na ZAPNUTo.

Mezi další parametry, které můžete upravit, patří:

- **long_query_time**: Pokud dotaz trvá déle než long_query_time (v sekundách), dotaz se zaznamená do protokolu. Výchozí hodnota je 10 sekund.
- **log_slow_admin_statements**: Pokud on zahrnuje příkazy pro správu, jako je ALTER_TABLE a ANALYZE_TABLE v příkazech zapsaných do slow_query_log.
- **log_queries_not_using_indexes**: Určuje, zda jsou dotazy, které nepoužívají indexy, protokolovány do slow_query_log
- **log_throttle_queries_not_using_indexes**: Tento parametr omezuje počet neindexovaných dotazů, které lze zapsat do protokolu pomalých dotazů. Tento parametr se projeví, když je log_queries_not_using_indexes nastaveno na ZAPNUTo.
- **log_output**: Pokud "File", nástroj umožňuje zapsat protokol pomalých dotazů do úložiště místního serveru i do Azure monitor diagnostických protokolů. Pokud "none", protokol pomalého dotazu bude zapsán pouze do Azure Monitor diagnostické protokoly. 

Úplný popis pomalých parametrů protokolu dotazů naleznete v [dokumentaci k protokolu pomalého dotazů](https://mariadb.com/kb/en/library/slow-query-log-overview/) MariaDB.

## <a name="diagnostic-logs"></a>Diagnostické protokoly
Azure Database for MariaDB je integrován s protokoly diagnostiky Azure Monitor. Po povolení protokolů pomalých dotazů na serveru MariaDB se můžete rozhodnout, že se mají vysílat pro Azure Monitor protokolů, Event Hubs nebo Azure Storage. Další informace o tom, jak povolit diagnostické protokoly, naleznete v části Postupy v [dokumentaci diagnostické protokoly](../azure-monitor/platform/resource-logs-overview.md).

> [!IMPORTANT]
> Tato diagnostická funkce pro protokoly serveru je k dispozici pouze v Pro obecné účely a v paměťově optimalizovaných [cenových úrovních](concepts-pricing-tiers.md).

Následující tabulka popisuje, co je v každém protokolu. V závislosti na metodě Output se pole, která jsou součástí, a pořadí, ve kterém se zobrazují, můžou lišit.

| **Vlastnost** | **Popis** |
|---|---|
| `TenantId` | ID tenanta |
| `SourceSystem` | `Azure` |
| `TimeGenerated` [UTC] | Časové razítko, kdy se protokol zaznamenal v UTC |
| `Type` | Typ protokolu Vždy `AzureDiagnostics` |
| `SubscriptionId` | Identifikátor GUID předplatného, ke kterému server patří |
| `ResourceGroup` | Název skupiny prostředků, do které server patří |
| `ResourceProvider` | Název poskytovatele prostředků Vždy `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Identifikátor URI prostředku |
| `Resource` | Název serveru |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | Název serveru |
| `start_time_t` [UTC] | Čas, kdy dotaz začal |
| `query_time_s` | Celkový čas, kdy se dotaz trvalo spustit |
| `lock_time_s` | Celkový čas, kdy byl dotaz uzamčen |
| `user_host_s` | Uživatelské jméno |
| `rows_sent_s` | Počet odeslaných řádků |
| `rows_examined_s` | Počet testovaných řádků |
| `last_insert_id_s` | [last_insert_id](https://mariadb.com/kb/en/library/last_insert_id/) |
| `insert_id_s` | Vložit ID |
| `sql_text_s` | Úplný dotaz |
| `server_id_s` | ID serveru |
| `thread_id_s` | ID podprocesu |
| `\_ResourceId` | Identifikátor URI prostředku |

## <a name="next-steps"></a>Další kroky
- [Konfigurace a přístup k protokolům serveru z Azure Portal](howto-configure-server-logs-portal.md).
