---
title: Azure Database pro MariaDB Data replikace uložené procedury
description: Tento článek představuje všechny uložené procedury používané pro Data replikace.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: reference
ms.date: 09/24/2018
ms.openlocfilehash: 75dc10ba3d95fd12ea99e10d321237560ee28171
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53535348"
---
# <a name="azure-database-for-mariadb-data-in-replication-stored-procedures"></a>Azure Database pro MariaDB Data replikace uložené procedury

Replikace dat umožňuje synchronizovat data ze serveru MariaDB s místně, v databázi služby hostované jiných poskytovatelů cloudových služeb do služby Azure Database pro MariaDB službu nebo virtuálních počítačích.

Následující uložené procedury slouží k nastavení nebo odebrání replikace dat mezi hlavní a repliky.

|**Název uložené procedury**|**Vstupní parametry**|**Výstupní parametry**|**Poznámka k použití**|
|-----|-----|-----|-----|
|*MySQL.az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|neuvedeno|Přenos dat v režimu SSL, předejte do parametru master_ssl_ca v kontextu certifikát certifikační Autority. </br><br>Přenos dat bez protokolu SSL, předejte do parametru master_ssl_ca v prázdný řetězec.|
|*_spustit MySQL.az_replication*|neuvedeno|neuvedeno|Spustí replikaci.|
|*_zastavit MySQL.az_replication*|neuvedeno|neuvedeno|Zastaví se replikace.|
|*MySQL.az_replication _remove_master*|neuvedeno|neuvedeno|Odebere se vztah replikace mezi hlavním virtuálním počítači a replikou.|
|*MySQL.az_replication_skip_counter*|neuvedeno|neuvedeno|Přeskočí jednu replikace – chyba.|

Nastavení data replikace mezi hlavní a repliky ve službě Azure Database pro MariaDB, najdete v tématu [konfigurace replikace dat](howto-data-in-replication.md).
