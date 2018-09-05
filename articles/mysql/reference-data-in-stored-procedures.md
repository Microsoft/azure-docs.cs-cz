---
title: Azure Database for MySQL Data replikace uložené procedury
description: Tento článek představuje všechny uložené procedury používané pro Data replikace.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 08/31/2018
ms.openlocfilehash: fb1a1b31d90df0022e5973de3ae2f55fb4c36701
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2018
ms.locfileid: "43665942"
---
# <a name="azure-database-for-mysql-data-in-replication-stored-procedures"></a>Azure Database for MySQL Data replikace uložené procedury

Replikace dat můžete synchronizovat data z na virtual machines nebo databázové služby, které jsou hostitelem jiných poskytovatelů cloudových služeb do Azure Database for MySQL služby spuštěné v místním serveru MySQL.

Následující uložené procedury slouží k nastavení nebo odebrání replikace dat mezi hlavní a repliky.

|**Název uložené procedury**|**Vstupní parametry**|**Výstupní parametry**|**Poznámka k použití**|
|-----|-----|-----|-----|
|*MySQL.az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|neuvedeno|Přenos dat v režimu SSL, předejte do parametru master_ssl_ca v kontextu certifikát certifikační Autority. </br><br>Přenos dat bez protokolu SSL, předejte do parametru master_ssl_ca v prázdný řetězec.|
|*_spustit MySQL.az_replication*|neuvedeno|neuvedeno|Spustí replikaci.|
|*_zastavit MySQL.az_replication*|neuvedeno|neuvedeno|Zastaví se replikace.|
|*MySQL.az_replication _remove_master*|neuvedeno|neuvedeno|Odebere se vztah replikace mezi hlavním virtuálním počítači a replikou.|
|*MySQL.az_replication_skip_counter*|neuvedeno|neuvedeno|Přeskočí jednu replikace – chyba.|

Nastavení data replikace mezi hlavní a repliky ve službě Azure Database for MySQL, najdete v tématu [konfigurace replikace dat](howto-data-in-replication.md).
