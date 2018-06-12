---
title: Protokoly serveru pro databázi Azure pro databázi MySQL
description: Popisuje protokolů dostupných v databázi Azure pro MySQL a dostupné parametry pro povolení protokolování různé úrovně.
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 50e4b9b8b8f9433ec725aaa982e969cec7afb91c
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35265781"
---
# <a name="server-logs-in-azure-database-for-mysql"></a>V protokolech serveru v Azure databáze pro databázi MySQL
V databázi Azure pro databázi MySQL je k dispozici uživatelům protokol pomalé dotazu. Přístup k protokolu transakcí není podporována. Protokol pomalé dotazu slouží k identifikaci kritické body pro řešení potíží. 

Další informace o protokolu pomalé dotazu MySQL, naleznete v příručce odkaz MySQL [zpomalit části protokolu dotazu](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).

## <a name="access-server-logs"></a>Přístup k protokolům serveru
Můžete zobrazit seznam a stáhnout databáze Azure pro protokoly MySQL serveru pomocí portálu Azure a rozhraní příkazového řádku Azure.

Na portálu Azure vyberte svou databázi Azure pro server databáze MySQL. V části **monitorování** záhlaví, vyberte **protokoly serveru** stránky.

Další informace o rozhraní příkazového řádku Azure najdete v tématu [konfigurace a přístup protokolů serveru pomocí rozhraní příkazového řádku Azure](howto-configure-server-logs-in-cli.md).

## <a name="log-retention"></a>Uchování protokolu
Protokoly jsou k dispozici až sedm dní od jejich vytvoření. Pokud celková velikost dostupné protokoly překračuje 7.5 GB, odstraní se nejstarší soubory dokud místa je k dispozici. 

Protokoly otáčejí každých 24 hodin nebo 7.5 GB, co nastane dříve.


## <a name="configure-logging"></a>Konfigurace protokolování 
Ve výchozím nastavení je zakázán protokol pomalé dotazu. Chcete-li ji povolit, nastavte slow_query_log na hodnotu ON.

Další parametry, které můžete upravit patří:

- **long_query_time**: Pokud dotaz trvá déle, než long_query_time (v sekundách) se zaprotokoluje takový dotaz. Výchozí hodnota je 10 sekund.
- **log_slow_admin_statements**: Pokud ON zahrnuje správu příkazy, jako je třeba ALTER_TABLE a ANALYZE_TABLE v příkazech zapsána do slow_query_log.
- **log_queries_not_using_indexes**: Určuje, zda dotazy, které nepoužívají indexy jsou protokolovány slow_query_log
- **log_throttle_queries_not_using_indexes**: Tento parametr omezuje počet jiný index dotazy, které je možné zapsat do protokolu pomalé dotazu. Tento parametr se projeví log_queries_not_using_indexes nastavena na hodnotu ON.

Najdete v článku MySQL [zpomalit dokumentaci protokolu dotazu](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) pro úplný popis pomalé protokolu parametry dotazu.

## <a name="next-steps"></a>Další kroky
- [Postup konfigurace a přístup k protokolům serveru z příkazového řádku Azure](howto-configure-server-logs-in-cli.md).
