---
title: Uložené procedury správy – Azure Database for MySQL
description: Seznamte se s uloženými procedurami v Azure Database for MySQL, které vám pomůžou při konfiguraci replikace dat, nastavení časového pásma a dezaktivačních dotazů.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 7a1aa061bb8c8be3a676e0e5bb690b2a9749b6c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94536128"
---
# <a name="azure-database-for-mysql-management-stored-procedures"></a>Uložené procedury správy Azure Database for MySQL

Uložené procedury jsou k dispozici na Azure Database for MySQL serverech, které vám pomůžou se správou serveru MySQL. To zahrnuje správu připojení, dotazů a nastavení Replikace vstupních dat serveru.  

## <a name="data-in-replication-stored-procedures"></a>Replikace vstupních dat uložené procedury

Replikace vstupních dat umožňuje synchronizovat data ze serveru MySQL spuštěného v místním prostředí, na virtuálních počítačích nebo v databázových službách hostovaných jinými poskytovateli cloudových služeb do služby Azure Database for MySQL.

Následující uložené procedury se používají k nastavení nebo odebrání Replikace vstupních dat mezi zdrojem a replikou.

|**Název uložené procedury**|**Vstupní parametry**|**Výstupní parametry**|**Poznámka k použití**|
|-----|-----|-----|-----|
|*mysql.az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|–|Chcete-li přenést data s režimem SSL, předejte kontext certifikátu certifikační autority do parametru master_ssl_ca. </br><br>Chcete-li přenést data bez protokolu SSL, předejte do parametru master_ssl_ca prázdný řetězec.|
|*mysql.az_replication _start*|N/A|N/A|Spustí replikaci.|
|*mysql.az_replication _stop*|N/A|N/A|Zastaví replikaci.|
|*mysql.az_replication _remove_master*|N/A|N/A|Odebere vztah replikace mezi zdrojem a replikou.|
|*mysql.az_replication_skip_counter*|N/A|N/A|Přeskočí jednu chybu replikace.|

Chcete-li nastavit Replikace vstupních dat mezi zdrojem a replikou v Azure Database for MySQL, přečtěte si téma [konfigurace replikace vstupních dat](howto-data-in-replication.md).

## <a name="other-stored-procedures"></a>Jiné uložené procedury

Následující uložené procedury jsou k dispozici v Azure Database for MySQL ke správě serveru.

|**Název uložené procedury**|**Vstupní parametry**|**Výstupní parametry**|**Poznámka k použití**|
|-----|-----|-----|-----|
|*mysql.az_kill*|processlist_id|–|Ekvivalent [`KILL CONNECTION`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) příkazu Ukončí připojení přidružené k poskytnutému processlist_id po ukončení všech příkazů, které připojení provádí.|
|*mysql.az_kill_query*|processlist_id|–|Ekvivalent [`KILL QUERY`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) příkazu Ukončí příkaz, který připojení právě provádí. Ponechá připojení aktivní.|
|*mysql.az_load_timezone*|N/A|N/A|Načte tabulky časového pásma, které umožňují `time_zone` nastavit parametr na pojmenované hodnoty (např. "US/Tichomoří").|

## <a name="next-steps"></a>Další kroky
- Naučte se nastavit [replikace vstupních dat](howto-data-in-replication.md)
- Naučte se používat [tabulky časových pásem](howto-server-parameters.md#working-with-the-time-zone-parameter) .
