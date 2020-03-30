---
title: Podporované verze – Azure Database for MySQL
description: Zjistěte, které verze serveru MySQL jsou podporované ve službě Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 197b3100190711a51cfe125fe1214a59c18e1491
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536968"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Podporovaná databáze Azure pro verze serveru MySQL

Azure Database for MySQL byl vyvinut z [MySQL Community Edition](https://www.mysql.com/products/community/)pomocí modulu InnoDB.

MySQL používá schéma pojmenování X.Y.Z. X je hlavní verze, Y je dílčí verze a Z je vydání opravy chyb. Další informace o schématu naleznete v [dokumentaci MySQL](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).

> [!NOTE]
> V rámci služby se k přesměrování připojení na instance serveru používá brána. Po navázání připojení se v klientovi MySQL zobrazí verze MySQL nastavená v bráně, a ne verze skutečně spuštěná na instanci serveru MySQL. Pokud chcete zjistit verzi instance serveru MySQL, na příkazovém řádku MySQL spusťte příkaz `SELECT VERSION();`.

Azure Database for MySQL aktuálně podporuje následující verze:

## <a name="mysql-version-56"></a>MySQL verze 5.6

Uvolnění opravy chyb: 5.6.45

Další informace o vylepšeních a opravách v této verzi naleznete v [poznámkách](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-45.html) k verzi MySQL.

## <a name="mysql-version-57"></a>MySQL verze 5.7

Uvolnění opravy chyb: 5.7.27

Další informace o vylepšeních a opravách v této verzi naleznete v [poznámkách](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-27.html) k verzi MySQL.

## <a name="mysql-version-80"></a>MySQL verze 8.0

Uvolnění opravy chyb: 8.0.15

Další informace o vylepšeních a opravách v této verzi naleznete v [poznámkách](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) k verzi MySQL.

## <a name="managing-updates-and-upgrades"></a>Správa aktualizací a upgradů
Služba automaticky spravuje opravy aktualizací verzí opravy chyb. Například 5.7.20 až 5.7.21.  

Upgrady dílčích a hlavních verzí se v současné době nepodporují. Například upgrade z MySQL 5.6 na MySQL 5.7 se nepodporuje. Pokud chcete upgradovat z verze 5.6 na verzi 5.7, pořiďte [výpis paměti a obnovte](./concepts-migrate-dump-restore.md) ji na serveru vytvořeném s novou verzí jádra.

## <a name="next-steps"></a>Další kroky

Informace o konkrétních kvótách prostředků a omezeních na základě **vaší úrovně služeb**naleznete v [tématu Úrovně služeb](./concepts-pricing-tiers.md)
