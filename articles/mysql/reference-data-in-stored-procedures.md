---
title: Azure Database for MySQL Data replikace uložené procedury
description: Tento článek představuje všechny uložené procedury používané pro Data replikace.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 08/31/2018
ms.openlocfilehash: a3c88953eea95871529e8ab257f52b694db443a9
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53544851"
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
