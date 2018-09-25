---
title: Protokoly serveru Azure Database pro MariaDB
description: Popisuje protokoly, které jsou k dispozici ve službě Azure Database pro MariaDB a parametry dostupnými pro povolení úrovní různých protokolování.
author: rachel-msft
ms.author: raagyema
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: d982af64517ed452d907f62a39e975d472951392
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46992979"
---
# <a name="server-logs-in-azure-database-for-mariadb"></a>Protokolů serveru ve službě Azure Database pro MariaDB
Ve službě Azure Database pro MariaDB protokol pomalých dotazů je dostupné pro uživatele. Přístup k protokolu transakcí se nepodporuje. Protokol pomalých dotazů je možné identifikovat kritické body výkonu pro řešení potíží.

Další informace o protokolu pomalých dotazů, najdete v dokumentaci MariaDB pro [protokol pomalých dotazů](https://mariadb.com/kb/en/library/slow-query-log-overview/).

## <a name="access-server-logs"></a>Přístup k protokolům serveru
Můžete seznam a stáhněte si – Azure Database pro MariaDB protokolů serveru pomocí webu Azure portal a rozhraní příkazového řádku Azure.

Na webu Azure Portal vyberte váš server Azure Database for MariaDB. V části **monitorování** záhlaví, vyberte **protokoly serveru** stránky.

<!-- For more information on Azure CLI, see [Configure and access server logs using Azure CLI](howto-configure-server-logs-in-cli.md).-->

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

Najdete v článku MariaDB [zpomalit dokumentace ke službě log dotazu](https://mariadb.com/kb/en/library/slow-query-log-overview/) úplný popis parametrů protokol pomalých dotazů.

## <a name="next-steps"></a>Další kroky
- [Jak nakonfigurovat a přístup k protokolům server z webu Azure portal](howto-configure-server-logs-portal.md).
