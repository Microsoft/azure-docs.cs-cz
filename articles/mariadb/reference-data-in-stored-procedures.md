---
title: Azure Database pro MariaDB Data replikace uložené procedury
description: Tento článek představuje všechny uložené procedury používané pro Data replikace.
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: reference
ms.date: 09/24/2018
ms.openlocfilehash: 87c6fa783964c019841810ec38f342a5a44c0ee3
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46959083"
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