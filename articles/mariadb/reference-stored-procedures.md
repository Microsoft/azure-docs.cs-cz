---
title: Uložené procedury správy – databáze Azure pro MariaDB
description: Zjistěte, které uložené procedury v Azure Database for MariaDB jsou užitečné, které vám pomůžou nakonfigurovat replikaci datového připojení, nastavit časové pásmo a zabít dotazy.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 2f6d1e20db64cb0c2a64771ea26b971b22031fd9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79529986"
---
# <a name="azure-database-for-mariadb-management-stored-procedures"></a>Uložené procedury azure databáze pro správu MariaDB

Uložené procedury jsou k dispozici na azure database pro mariadb servery pomoci spravovat server MariaDB. To zahrnuje správu připojení serveru, dotazy a nastavení replikace dat.  

## <a name="data-in-replication-stored-procedures"></a>Uložené procedury replikace dat

Replikace vstupních dat umožňuje synchronizovat data ze serveru MariaDB spuštěného v místním prostředí, na virtuálních počítačích nebo v databázových službách hostovaných jinými poskytovateli cloudových služeb do služby Azure Database for MariaDB.

Následující uložené procedury se používají k nastavení nebo odebrání replikace dat mezi předlohou a replikou.

|**Název uložené procedury**|**Vstupní parametry**|**Výstupní parametry**|**Poznámka k použití**|
|-----|-----|-----|-----|
|*mysql.az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|Není dostupné.|Chcete-li přenášet data v režimu SSL, přejděte v kontextu certifikátu certifikační autority do parametru master_ssl_ca. </br><br>Chcete-li přenést data bez protokolu SSL, přejděte prázdný řetězec do parametru master_ssl_ca.|
|*mysql.az_replication _start*|Není dostupné.|Není dostupné.|Spustí replikaci.|
|*mysql.az_replication _stop*|Není dostupné.|Není dostupné.|Zastaví replikaci.|
|*mysql.az_replication _remove_master*|Není dostupné.|Není dostupné.|Odebere vztah replikace mezi předlohou a replikou.|
|*mysql.az_replication_skip_counter*|Není dostupné.|Není dostupné.|Přeskočí jednu chybu replikace.|

Pokud chcete nastavit replikaci dat mezi hlavním a replikou v databázi Azure pro MariaDB, přečtěte [si, jak nakonfigurovat replikaci dat](howto-data-in-replication.md).

## <a name="other-stored-procedures"></a>Další uložené procedury

Následující uložené procedury jsou k dispozici v Azure Database pro MariaDB pro správu serveru.

|**Název uložené procedury**|**Vstupní parametry**|**Výstupní parametry**|**Poznámka k použití**|
|-----|-----|-----|-----|
|*mysql.az_kill*|processlist_id|Není dostupné.|Odpovídá [`KILL CONNECTION`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) příkazu. Ukončí připojení spojené s poskytnutým processlist_id po ukončení libovolného příkazu, který je spuštěn.|
|*mysql.az_kill_query*|processlist_id|Není dostupné.|Odpovídá [`KILL QUERY`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) příkazu. Ukončí příkaz, který právě provádí připojení. Nechává spojení živé.|
|*mysql.az_load_timezone*|Není dostupné.|Není dostupné.|Načte tabulky časových `time_zone` pásem, aby parametr mohl být nastaven na pojmenované hodnoty (např. "US/Pacifik").|

## <a name="next-steps"></a>Další kroky
- Přečtěte si, jak nastavit [replikaci dat](howto-data-in-replication.md)
- Naučte se používat [tabulky časových pásem](howto-server-parameters.md#working-with-the-time-zone-parameter)