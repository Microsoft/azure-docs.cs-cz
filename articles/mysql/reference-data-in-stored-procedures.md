---
title: Azure Database for MySQL Data replikace uložené procedury
description: Tento článek představuje všechny uložené procedury používané pro Data replikace.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 08/31/2018
ms.openlocfilehash: a3c88953eea95871529e8ab257f52b694db443a9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61244306"
---
# <a name="azure-database-for-mysql-data-in-replication-stored-procedures"></a>Azure Database for MySQL Data replikace uložené procedury

Replikace dat můžete synchronizovat data z na virtual machines nebo databázové služby, které jsou hostitelem jiných poskytovatelů cloudových služeb do Azure Database for MySQL služby spuštěné v místním serveru MySQL.

Následující uložené procedury slouží k nastavení nebo odebrání replikace dat mezi hlavní a repliky.

|**Název uložené procedury**|**Vstupní parametry**|**Výstupní parametry**|**Poznámka k použití**|
|-----|-----|-----|-----|
|*mysql.az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|neuvedeno|Přenos dat v režimu SSL, předejte do parametru master_ssl_ca v kontextu certifikát certifikační Autority. </br><br>Přenos dat bez protokolu SSL, předejte do parametru master_ssl_ca v prázdný řetězec.|
|*mysql.az_replication _start*|neuvedeno|neuvedeno|Spustí replikaci.|
|*mysql.az_replication _stop*|neuvedeno|neuvedeno|Zastaví se replikace.|
|*mysql.az_replication _remove_master*|neuvedeno|neuvedeno|Odebere se vztah replikace mezi hlavním virtuálním počítači a replikou.|
|*mysql.az_replication_skip_counter*|neuvedeno|neuvedeno|Přeskočí jednu replikace – chyba.|

Nastavení data replikace mezi hlavní a repliky ve službě Azure Database for MySQL, najdete v tématu [konfigurace replikace dat](howto-data-in-replication.md).
