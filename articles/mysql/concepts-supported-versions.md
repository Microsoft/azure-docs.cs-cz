---
title: Podporované verze v Azure Database for MySQL
description: V této části najdete popis podporovaných verzí v Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 08/09/2019
ms.openlocfilehash: 28d635dc5042799790d032ef4b46bf28118cb326
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2019
ms.locfileid: "68947174"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Podporované verze serveru Azure Database for MySQL

Azure Database for MySQL bylo vyvinuto z [verze MySQL Community Edition](https://www.mysql.com/products/community/)pomocí modulu InnoDB.

MySQL používá schéma pojmenování X. Y. Z. X je hlavní verze, Y je dílčí verze a verze Z je oprava chyby. Další informace o schématu najdete v [dokumentaci k MySQL](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).

> [!NOTE]
> V rámci služby se k přesměrování připojení k instancím serveru používá brána. Po navázání připojení zobrazí klient MySQL v bráně verzi MySQL nastavenou, ne skutečnou verzi, která běží na instanci serveru MySQL. Pokud chcete zjistit verzi instance serveru MySQL, použijte `SELECT VERSION();` příkaz na příkazovém řádku MySQL.

Azure Database for MySQL aktuálně podporuje následující verze:

## <a name="mysql-version-56"></a>MySQL verze 5,6

Oprava chyby verze: 5.6.42

Další informace o vylepšeních a opravách v MySQL 5.6.42 najdete v poznámkách k [verzi](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-42.html) MySQL.

## <a name="mysql-version-57"></a>MySQL verze 5,7

Oprava chyby verze: 5.7.24

Informace o vylepšeních a opravách v MySQL 5.7.24 najdete v poznámkách k [verzi](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-24.html) MySQL.

## <a name="mysql-version-80"></a>MySQL verze 8,0

> [!IMPORTANT]
> MySQL 8,0 je aktuálně ve verzi Preview.

Oprava chyby verze: 8.0.15

Informace o vylepšeních a opravách v MySQL 8.0.15 najdete v poznámkách k [verzi](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) MySQL.

## <a name="managing-updates-and-upgrades"></a>Správa aktualizací a upgradů
Služba automaticky spravuje opravy pro aktualizace verze opravy chyb. Například 5.7.20 na 5.7.21.  

V současné době se nepodporují upgrady Poda hlavních verzí. Například upgrade z MySQL 5,6 na MySQL 5,7 není podporován. Pokud chcete upgradovat z 5,6 na 5,7, proveďte [Výpis paměti a obnovte](./concepts-migrate-dump-restore.md) na server, který byl vytvořen s novou verzí modulu.

## <a name="next-steps"></a>Další postup

Informace o konkrétních kvótách prostředků a omezeních založených na vaší **úrovni služby**najdete v tématu [úrovně služeb](./concepts-pricing-tiers.md) .
