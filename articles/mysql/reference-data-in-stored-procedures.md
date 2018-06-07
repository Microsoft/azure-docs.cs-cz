---
title: Databáze pro replikaci dat MySQL v Azure uložené procedury
description: Tento článek představuje všechny uložené procedury používané pro Data v replikaci.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 05/18/2018
ms.openlocfilehash: 8683aacb2ae41cb4c57aa70b375eca9db6106492
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34654872"
---
# <a name="azure-database-for-mysql-data-in-replication-stored-procedures"></a>Databáze pro replikaci dat MySQL v Azure uložené procedury

Replikace dat v umožňuje synchronizovat data z databáze MySQL serveru se systémem na místních počítačích v virtuální počítače, nebo v databázi služby hostované jiných poskytovatelů cloudu do databáze Azure pro službu MySQL.

Následující uložené procedury používají k nastavení nebo odebrání Data replikace mezi primárním serverem a repliky.

|**Název uložené procedury**|**Vstupní parametry**|**Výstupní parametry**|**Poznámka k použití**|
|-----|-----|-----|-----|
|*MySQL.az_replication_change_primary*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|neuvedeno|K přenosu dat pomocí režimu protokolu SSL, předejte do parametru master_ssl_ca v kontextu certifikát certifikační Autority. </br><br>K přenosu dat bez protokolu SSL, předejte do parametru master_ssl_ca v prázdný řetězec.|
|*s_pustit MySQL.az_replication*|neuvedeno|neuvedeno|Spuštění replikace.|
|*MySQL.az_replication _stop*|neuvedeno|neuvedeno|Zastaví replikace.|
|*MySQL.az_replication _remove_primary*|neuvedeno|neuvedeno|Odebere se tak vztah replikace mezi primárním serverem a repliky.|
|*MySQL.az_replication_skip_counter*|neuvedeno|neuvedeno|Přeskočí jednu chybu replikace.|

Nastavení dat v replikace mezi primární a repliky v databázi Azure pro databázi MySQL, najdete v tématu [konfiguraci replikace dat v](howto-data-in-replication.md).