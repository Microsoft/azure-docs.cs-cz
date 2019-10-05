---
title: Uložené procedury správy Azure Database for MySQL
description: Seznamte se s uloženými procedurami v Azure Database for MySQL, které vám pomůžou při konfiguraci replikace dat, nastavení časového pásma a dezaktivačních dotazů.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 09/19/2019
ms.openlocfilehash: 34598278ac7f432c5976de86eaf5dcf477c7e81a
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2019
ms.locfileid: "71970344"
---
# <a name="azure-database-for-mysql-management-stored-procedures"></a>Uložené procedury správy Azure Database for MySQL

Uložené procedury jsou k dispozici na Azure Database for MySQL serverech, které vám pomůžou se správou serveru MySQL. To zahrnuje správu připojení, dotazů a nastavení Replikace vstupních dat serveru.  

## <a name="data-in-replication-stored-procedures"></a>Replikace vstupních dat uložené procedury

Replikace vstupních dat umožňuje synchronizovat data ze serveru MySQL spuštěného v místním prostředí, na virtuálních počítačích nebo v databázových službách hostovaných jinými poskytovateli cloudových služeb do služby Azure Database for MySQL.

Následující uložené procedury se používají k nastavení nebo odebrání Replikace vstupních dat mezi hlavním serverem a replikou.

|**Název uložené procedury**|**Vstupní parametry**|**Výstupní parametry**|**Poznámka k použití**|
|-----|-----|-----|-----|
|*MySQL. AZ _replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|Nevztahuje se|Pokud chcete přenést data s režimem SSL, předejte kontext certifikátu certifikační autority do parametru master_ssl_ca. </br><br>Chcete-li přenést data bez protokolu SSL, předejte do parametru master_ssl_ca prázdný řetězec.|
|*MySQL. AZ _replication _start*|Nevztahuje se|Nevztahuje se|Spustí replikaci.|
|*MySQL. AZ _replication _stop*|Nevztahuje se|Nevztahuje se|Zastaví replikaci.|
|*MySQL. AZ _replication _remove_master*|Nevztahuje se|Nevztahuje se|Odebere vztah replikace mezi hlavním serverem a replikou.|
|*MySQL. AZ _replication_skip_counter*|Nevztahuje se|Nevztahuje se|Přeskočí jednu chybu replikace.|

Chcete-li nastavit Replikace vstupních dat mezi hlavním serverem a replikou v Azure Database for MySQL, přečtěte si téma [Postup konfigurace replikace vstupních dat](howto-data-in-replication.md).

## <a name="other-stored-procedures"></a>Jiné uložené procedury

Následující uložené procedury jsou k dispozici v Azure Database for MySQL ke správě serveru.

|**Název uložené procedury**|**Vstupní parametry**|**Výstupní parametry**|**Poznámka k použití**|
|-----|-----|-----|-----|
|*MySQL. AZ _kill*|processlist_id|Nevztahuje se|Ekvivalent příkazu [`KILL CONNECTION`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) . Ukončí připojení přidružené k poskytnutému processlist_id po ukončení všech příkazů, které připojení provádí.|
|*MySQL. AZ _kill_query*|processlist_id|Nevztahuje se|Ekvivalent příkazu [`KILL QUERY`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) . Ukončí příkaz, který připojení právě provádí. Ponechá připojení aktivní.|
|*MySQL. AZ _load_timezone*|Nevztahuje se|Nevztahuje se|Načte tabulky časového pásma, které umožňují nastavit parametr `time_zone` na pojmenované hodnoty (např. "US/Tichomoří").|

## <a name="next-steps"></a>Další kroky
- Naučte se nastavit [replikace vstupních dat](howto-data-in-replication.md)
- Naučte se používat [tabulky časových pásem](howto-server-parameters.md#working-with-the-time-zone-parameter) .
