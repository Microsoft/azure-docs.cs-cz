---
title: Protokoly serveru Azure Database pro MariaDB
description: Popisuje protokoly, které jsou k dispozici ve službě Azure Database pro MariaDB a parametry dostupnými pro povolení úrovní různých protokolování.
author: rachel-msft
ms.author: raagyema
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/12/2019
ms.openlocfilehash: 7a517be49a249b0b73c901137381bd05946aa4cc
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67065706"
---
# <a name="slow-query-logs-in-azure-database-for-mariadb"></a>Protokoly pomalých dotazů ve službě Azure Database pro MariaDB
Ve službě Azure Database pro MariaDB protokol pomalých dotazů je dostupné pro uživatele. Přístup k protokolu transakcí se nepodporuje. Protokol pomalých dotazů je možné identifikovat kritické body výkonu pro řešení potíží.

Další informace o protokolu pomalých dotazů, najdete v dokumentaci MariaDB pro [protokol pomalých dotazů](https://mariadb.com/kb/en/library/slow-query-log-overview/).

## <a name="access-slow-query-logs"></a>Zobrazení protokolů pomalých dotazů
Můžete seznam a stáhněte si – Azure Database pro MariaDB protokoly pomalých dotazů pomocí webu Azure portal a rozhraní příkazového řádku Azure.

Na webu Azure Portal vyberte váš server Azure Database for MariaDB. V části **monitorování** záhlaví, vyberte **protokoly serveru** stránky.

Další informace o Azure CLI najdete v tématu [server protokoly konfigurace a přístup pomocí rozhraní příkazového řádku Azure](howto-configure-server-logs-cli.md).

## <a name="log-retention"></a>Uchovávání protokolů
Protokoly jsou k dispozici po dobu až sedmi dní od jejich vytvoření. Pokud celková velikost dostupné protokoly překročí 7 GB, se odstraní nejstarší soubory, dokud je k dispozici místo.

Protokoly jsou otočeny každých 24 hodin nebo 7 GB, co nastane dříve.

## <a name="configure-slow-query-logging"></a>Nakonfigurovat protokolování pomalých dotazů
Ve výchozím nastavení je zakázaný protokol pomalých dotazů. Ji Pokud chcete povolit, nastavte slow_query_log na ON.

Další parametry, které můžete upravit patří:

- **long_query_time**: Pokud dotaz trvá déle než long_query_time (v sekundách) se přihlásí daný dotaz. Výchozí hodnota je 10 sekund.
- **log_slow_admin_statements**: Pokud dále zahrnuje příkazy pro správu jako ALTER_TABLE a ANALYZE_TABLE v příkazech napsané slow_query_log.
- **log_queries_not_using_indexes**: Určuje, zda dotazy, které nepoužívají indexy jsou protokolovány slow_query_log
- **log_throttle_queries_not_using_indexes**: Tento parametr omezuje počet jiných indexu dotazů, které se dají zapisovat do protokolu pomalých dotazů. Tento parametr se projeví při log_queries_not_using_indexes nastavená na ON.

Najdete v článku MariaDB [zpomalit dokumentace ke službě log dotazu](https://mariadb.com/kb/en/library/slow-query-log-overview/) úplný popis parametrů protokol pomalých dotazů.

## <a name="diagnostic-logs"></a>Diagnostické protokoly
Azure Database pro MariaDB je integrované v protokolech diagnostiky Azure Monitor. Jakmile povolíte protokoly pomalých dotazů serveru MariaDB, můžete je mít znovu vygenerován pro protokoly Azure monitoru, Event Hubs nebo Azure Storage. Další informace o tom, jak povolit diagnostické protokoly, zjistit, jak část [dokumentace k diagnostickým protokolům](../azure-monitor/platform/diagnostic-logs-overview.md).

> [!IMPORTANT]
> Tato funkce diagnostických protokolů serveru je k dispozici v obecné účely a optimalizovaný pro paměť pouze [cenové úrovně](concepts-pricing-tiers.md).

Následující tabulka popisuje, co je v každém protokolu. V závislosti na metodě výstup pole zahrnutá a pořadí, ve kterém jsou uvedeny se mohou lišit.

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
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | Název serveru |
| `start_time_t` [UTC] | Čas zahájení dotazu |
| `query_time_s` | Celková doba, kterou trvalo provádění dotazu |
| `lock_time_s` | Celkový čas, který dotaz byl uzamčen. |
| `user_host_s` | Uživatelské jméno |
| `rows_sent_s` | Počet řádků, které jsou odeslány |
| `rows_examined_s` | Počet řádků, které jsou zkoumány |
| `last_insert_id_s` | [last_insert_id](https://mariadb.com/kb/en/library/last_insert_id/) |
| `insert_id_s` | Vložte ID |
| `sql_text_s` | Celý dotaz |
| `server_id_s` | ID serveru |
| `thread_id_s` | ID vlákna |
| `\_ResourceId` | Identifikátor URI prostředku |

## <a name="next-steps"></a>Další postup
- [Jak nakonfigurovat a přístup k protokolům server z webu Azure portal](howto-configure-server-logs-portal.md).
