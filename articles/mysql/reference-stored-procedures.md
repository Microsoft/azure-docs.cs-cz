---
title: Azure Database for MySQL uložené procedury
description: Tento článek představuje uložené procedury specifické pro Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 09/19/2019
ms.openlocfilehash: f01a0bf68e510133058dc0075f27cfcf6241c7a8
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/20/2019
ms.locfileid: "71156173"
---
# <a name="azure-database-for-mysql-stored-procedures"></a>Azure Database for MySQL uložené procedury

Uložené procedury jsou k dispozici na Azure Database for MySQL serverech, které vám pomůžou se správou serveru MySQL. To zahrnuje správu připojení, dotazů a nastavení Replikace vstupních dat serveru.  

## <a name="data-in-replication-stored-procedures"></a>Replikace vstupních dat uložené procedury

Replikace vstupních dat umožňuje synchronizovat data ze serveru MySQL spuštěného v místním prostředí, na virtuálních počítačích nebo v databázových službách hostovaných jinými poskytovateli cloudových služeb do služby Azure Database for MySQL.

Následující uložené procedury se používají k nastavení nebo odebrání replikace dat mezi hlavním serverem a replikou.

|**Název uložené procedury**|**Vstupní parametry**|**Výstupní parametry**|**Poznámka k použití**|
|-----|-----|-----|-----|
|*mysql.az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|neuvedeno|Pokud chcete přenést data s režimem SSL, předejte kontext certifikátu certifikační autority do parametru master_ssl_ca. </br><br>Chcete-li přenést data bez protokolu SSL, předejte do parametru master_ssl_ca prázdný řetězec.|
|*MySQL. AZ _replication _start*|neuvedeno|neuvedeno|Spustí replikaci.|
|*mysql.az_replication _stop*|neuvedeno|neuvedeno|Zastaví replikaci.|
|*mysql.az_replication _remove_master*|neuvedeno|neuvedeno|Odebere vztah replikace mezi hlavním serverem a replikou.|
|*mysql.az_replication_skip_counter*|neuvedeno|neuvedeno|Přeskočí jednu chybu replikace.|

Chcete-li nastavit Replikace vstupních dat mezi hlavním serverem a replikou v Azure Database for MySQL, přečtěte si téma [Postup konfigurace replikace vstupních dat](howto-data-in-replication.md).

## <a name="other-stored-procedures"></a>Jiné uložené procedury

Následující uložené procedury jsou k dispozici v Azure Database for MySQL ke správě serveru.

|**Název uložené procedury**|**Vstupní parametry**|**Výstupní parametry**|**Poznámka k použití**|
|-----|-----|-----|-----|
|*MySQL. AZ _kill*|processlist_id|neuvedeno|[`KILL CONNECTION`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) Ekvivalent příkazu Ukončí připojení přidružené k poskytnutému processlist_id po ukončení všech příkazů, které připojení provádí.|
|*MySQL. AZ _kill_query*|processlist_id|neuvedeno|[`KILL QUERY`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) Ekvivalent příkazu Ukončí příkaz, který připojení právě provádí. Ponechá připojení aktivní.|
|*MySQL. AZ _load_timezone*|neuvedeno|neuvedeno|Načte tabulky časového pásma, které umožňují `time_zone` nastavit parametr na pojmenované hodnoty (např. "US/Tichomoří").|

## <a name="next-steps"></a>Další kroky
- Naučte se nastavit [replikace vstupních dat](howto-data-in-replication.md)
- Naučte se používat [tabulky časových pásem](howto-server-parameters.md#working-with-the-time-zone-parameter) .