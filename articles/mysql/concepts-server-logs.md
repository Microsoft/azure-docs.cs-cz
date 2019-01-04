---
title: Protokoly serveru Azure Database for MySQL
description: Popisuje protokoly, které jsou k dispozici ve službě Azure Database for MySQL a parametry dostupnými pro povolení úrovní různých protokolování.
author: rachel-msft
ms.author: raagyema
ms.service: mysql
ms.topic: conceptual
ms.date: 10/03/2018
ms.openlocfilehash: c9f8fc4bee370f287b40275b76fa98d2552d7600
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53545069"
---
# <a name="server-logs-in-azure-database-for-mysql"></a>Protokolů serveru ve službě Azure Database for MySQL
Ve službě Azure Database for MySQL je k dispozici uživatelům v protokolu pomalých dotazů. Přístup k protokolu transakcí se nepodporuje. Protokol pomalých dotazů je možné identifikovat kritické body výkonu pro řešení potíží. 

Další informace o protokolu pomalých dotazů MySQL, naleznete v tématu Referenční příručce MySQL [zpomalit část protokol dotazu](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).

## <a name="access-server-logs"></a>Přístup k protokolům serveru
Můžete seznam a stažení protokolů serveru MySQL pomocí webu Azure portal a rozhraní příkazového řádku Azure – Azure Database.

Na webu Azure Portal vyberte váš server Azure Database for MySQL. V části **monitorování** záhlaví, vyberte **protokoly serveru** stránky.

Další informace o Azure CLI najdete v tématu [server protokoly konfigurace a přístup pomocí rozhraní příkazového řádku Azure](howto-configure-server-logs-in-cli.md).

## <a name="log-retention"></a>Uchovávání protokolů
Protokoly jsou k dispozici po dobu až sedmi dní od jejich vytvoření. Pokud celková velikost dostupné protokoly překročí 7 GB, se odstraní nejstarší soubory, dokud je k dispozici místo. 

Protokoly jsou otočeny každých 24 hodin nebo 7 GB, co nastane dříve.


## <a name="configure-logging"></a>Konfigurace protokolování 
Ve výchozím nastavení je zakázaný protokol pomalých dotazů. Ji Pokud chcete povolit, nastavte slow_query_log na ON.

Další parametry, které můžete upravit patří:

- **long_query_time**: Pokud dotaz trvá déle než long_query_time (v sekundách) se přihlásí daný dotaz. Výchozí hodnota je 10 sekund.
- **log_slow_admin_statements**: Pokud dále zahrnuje příkazy pro správu jako ALTER_TABLE a ANALYZE_TABLE v příkazech napsané slow_query_log.
- **log_queries_not_using_indexes**: Určuje, zda dotazy, které nepoužívají indexy jsou protokolovány slow_query_log
- **log_throttle_queries_not_using_indexes**: Tento parametr omezuje počet jiných indexu dotazů, které se dají zapisovat do protokolu pomalých dotazů. Tento parametr se projeví při log_queries_not_using_indexes nastavená na ON.

Zobrazit MySQL [zpomalit dokumentace ke službě log dotazu](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) úplný popis parametrů protokol pomalých dotazů.

## <a name="diagnostic-logs"></a>Diagnostické protokoly
Azure Database for MySQL je integrovaná s diagnostické protokoly Azure monitoru. Jakmile povolíte protokoly pomalých dotazů na váš server MySQL, můžete je mít znovu vygenerován pro Log Analytics a Event Hubs, Azure Storage. Další informace o tom, jak povolit diagnostické protokoly, zjistit, jak část [dokumentace k diagnostickým protokolům](../azure-monitor/platform/diagnostic-logs-overview.md).

Následující tabulka popisuje, co je v každém protokolu. V závislosti na metodě výstup pole zahrnutá a pořadí, ve kterém jsou uvedeny se mohou lišit.

| **Vlastnost** | **Popis** |
|---|---|---|
| TenantId | Vaše ID tenanta |
| SourceSystem | `Azure` |
| TimeGenerated [UTC] | Časové razítko, kdy se přihlášení v protokolu ve standardu UTC |
| Typ | Typ protokolu. Vždy `AzureDiagnostics` |
| SubscriptionId | Identifikátor GUID pro předplatné, které server patří do |
| ResourceGroup | Název skupiny prostředků, do které patří server |
| ResourceProvider | Název poskytovatele prostředků. Vždy `MICROSOFT.DBFORMYSQL` |
| ResourceType | `Servers` |
| ID prostředku | Identifikátor URI prostředku |
| Prostředek | Název serveru |
| Kategorie | `MySqlSlowLogs` |
| OperationName | `LogEvent` |
| Logical_server_name_s | Název serveru |
| start_time_t [UTC] | Čas zahájení dotazu |
| query_time_s | Celková doba, kterou trvalo provádění dotazu |
| lock_time_s | Celkový čas, který dotaz byl uzamčen. |
| user_host_s | Uživatelské jméno |
| rows_sent_s | Počet řádků, které jsou odeslány |
| rows_examined_s | Počet řádků, které jsou zkoumány |
| last_insert_id_s | [last_insert_id](https://dev.mysql.com/doc/refman/8.0/en/information-functions.html#function_last-insert-id) |
| insert_id_s | Vložte id |
| sql_text_s | Celý dotaz |
| server_id_s | Id serveru |
| thread_id_s | id vlákna |
| \_ID prostředku | Identifikátor URI prostředku |

## <a name="next-steps"></a>Další kroky
- [Jak nakonfigurovat a přístup k protokolům server z příkazového řádku Azure](howto-configure-server-logs-in-cli.md).
