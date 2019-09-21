---
title: Azure Database for MariaDB uložené procedury
description: Tento článek představuje uložené procedury specifické pro Azure Database for MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: d9daaf619a19c0f4e4a591d4bbb4925679fd1fcb
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/20/2019
ms.locfileid: "71174900"
---
# <a name="azure-database-for-mariadb-stored-procedures"></a>Azure Database for MariaDB uložené procedury

Uložené procedury jsou k dispozici na Azure Database for MariaDB serverech, které vám pomůžou se správou serveru MariaDB. To zahrnuje správu připojení, dotazů a nastavení Replikace vstupních dat serveru.  

## <a name="data-in-replication-stored-procedures"></a>Replikace vstupních dat uložené procedury

Replikace vstupních dat umožňuje synchronizovat data ze serveru MariaDB spuštěného v místním prostředí, na virtuálních počítačích nebo v databázových službách hostovaných jinými poskytovateli cloudových služeb do služby Azure Database for MariaDB.

Následující uložené procedury se používají k nastavení nebo odebrání replikace dat mezi hlavním serverem a replikou.

|**Název uložené procedury**|**Vstupní parametry**|**Výstupní parametry**|**Poznámka k použití**|
|-----|-----|-----|-----|
|*mysql.az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|neuvedeno|Pokud chcete přenést data s režimem SSL, předejte kontext certifikátu certifikační autority do parametru master_ssl_ca. </br><br>Chcete-li přenést data bez protokolu SSL, předejte do parametru master_ssl_ca prázdný řetězec.|
|*MySQL. AZ _replication _start*|neuvedeno|neuvedeno|Spustí replikaci.|
|*mysql.az_replication _stop*|neuvedeno|neuvedeno|Zastaví replikaci.|
|*mysql.az_replication _remove_master*|neuvedeno|neuvedeno|Odebere vztah replikace mezi hlavním serverem a replikou.|
|*mysql.az_replication_skip_counter*|neuvedeno|neuvedeno|Přeskočí jednu chybu replikace.|

Chcete-li nastavit Replikace vstupních dat mezi hlavním serverem a replikou v Azure Database for MariaDB, přečtěte si téma [Postup konfigurace replikace vstupních dat](howto-data-in-replication.md).

## <a name="other-stored-procedures"></a>Jiné uložené procedury

Následující uložené procedury jsou k dispozici v Azure Database for MariaDB ke správě serveru.

|**Název uložené procedury**|**Vstupní parametry**|**Výstupní parametry**|**Poznámka k použití**|
|-----|-----|-----|-----|
|*MySQL. AZ _kill*|processlist_id|neuvedeno|[`KILL CONNECTION`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) Ekvivalent příkazu Ukončí připojení přidružené k poskytnutému processlist_id po ukončení všech příkazů, které připojení provádí.|
|*MySQL. AZ _kill_query*|processlist_id|neuvedeno|[`KILL QUERY`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) Ekvivalent příkazu Ukončí příkaz, který připojení právě provádí. Ponechá připojení aktivní.|
|*MySQL. AZ _load_timezone*|neuvedeno|neuvedeno|Načte tabulky časového pásma, které umožňují `time_zone` nastavit parametr na pojmenované hodnoty (např. "US/Tichomoří").|

## <a name="next-steps"></a>Další kroky
- Naučte se nastavit [replikace vstupních dat](howto-data-in-replication.md)
- Naučte se používat [tabulky časových pásem](howto-server-parameters.md#working-with-the-time-zone-parameter) .