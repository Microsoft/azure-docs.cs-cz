---
title: Protokoly serveru Azure Database for MySQL
description: Popisuje protokoly, které jsou k dispozici ve službě Azure Database for MySQL a parametry dostupnými pro povolení úrovní různých protokolování.
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 09/17/2018
ms.openlocfilehash: ac5be20815b552c08e5cd1054bf24d7a10b56498
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46124265"
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

## <a name="next-steps"></a>Další kroky
- [Jak nakonfigurovat a přístup k protokolům server z příkazového řádku Azure](howto-configure-server-logs-in-cli.md).
