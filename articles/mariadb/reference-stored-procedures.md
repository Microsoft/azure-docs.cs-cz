---
title: Uložené procedury správy – Azure Database for MariaDB
description: Seznamte se s uloženými procedurami v Azure Database for MariaDB, které vám pomůžou při konfiguraci replikace dat, nastavení časového pásma a dezaktivačních dotazů.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 9378f2cc62172043dbcaf13e88e9df4b6e61df9b
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74769944"
---
# <a name="azure-database-for-mariadb-management-stored-procedures"></a>Uložené procedury správy Azure Database for MariaDB

Uložené procedury jsou k dispozici na Azure Database for MariaDB serverech, které vám pomůžou se správou serveru MariaDB. To zahrnuje správu připojení, dotazů a nastavení Replikace vstupních dat serveru.  

## <a name="data-in-replication-stored-procedures"></a>Replikace vstupních dat uložené procedury

Replikace vstupních dat umožňuje synchronizovat data ze serveru MariaDB spuštěného v místním prostředí, na virtuálních počítačích nebo v databázových službách hostovaných jinými poskytovateli cloudových služeb do služby Azure Database for MariaDB.

Následující uložené procedury se používají k nastavení nebo odebrání Replikace vstupních dat mezi hlavním serverem a replikou.

|**Název uložené procedury**|**Vstupní parametry**|**Výstupní parametry**|**Poznámka k použití**|
|-----|-----|-----|-----|
|*MySQL. az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|Nevztahuje se|Chcete-li přenést data s režimem SSL, předejte kontext certifikátu certifikační autority do parametru master_ssl_ca. </br><br>Chcete-li přenést data bez protokolu SSL, předejte do parametru master_ssl_ca prázdný řetězec.|
|*MySQL. az_replication _start*|Nevztahuje se|Nevztahuje se|Spustí replikaci.|
|*MySQL. az_replication _stop*|Nevztahuje se|Nevztahuje se|Zastaví replikaci.|
|*MySQL. az_replication _remove_master*|Nevztahuje se|Nevztahuje se|Odebere vztah replikace mezi hlavním serverem a replikou.|
|*MySQL. az_replication_skip_counter*|Nevztahuje se|Nevztahuje se|Přeskočí jednu chybu replikace.|

Chcete-li nastavit Replikace vstupních dat mezi hlavním serverem a replikou v Azure Database for MariaDB, přečtěte si téma [Postup konfigurace replikace vstupních dat](howto-data-in-replication.md).

## <a name="other-stored-procedures"></a>Jiné uložené procedury

Následující uložené procedury jsou k dispozici v Azure Database for MariaDB ke správě serveru.

|**Název uložené procedury**|**Vstupní parametry**|**Výstupní parametry**|**Poznámka k použití**|
|-----|-----|-----|-----|
|*MySQL. az_kill*|processlist_id|Nevztahuje se|Ekvivalent příkazu [`KILL CONNECTION`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) . Ukončí připojení přidružené k poskytnutému processlist_id po ukončení všech příkazů, které připojení provádí.|
|*MySQL. az_kill_query*|processlist_id|Nevztahuje se|Ekvivalent příkazu [`KILL QUERY`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) . Ukončí příkaz, který připojení právě provádí. Ponechá připojení aktivní.|
|*MySQL. az_load_timezone*|Nevztahuje se|Nevztahuje se|Načte tabulky časového pásma, které umožňují nastavit parametr `time_zone` na pojmenované hodnoty (např. "US/Tichomoří").|

## <a name="next-steps"></a>Další kroky
- Naučte se nastavit [replikace vstupních dat](howto-data-in-replication.md)
- Naučte se používat [tabulky časových pásem](howto-server-parameters.md#working-with-the-time-zone-parameter) .