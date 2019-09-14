---
title: Podporované verze v Azure Database for MySQL
description: V této části najdete popis podporovaných verzí v Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 09/12/2019
ms.openlocfilehash: 25251b617522840412a4868331e155285f64a18c
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2019
ms.locfileid: "70962591"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Podporované verze serveru Azure Database for MySQL

Azure Database for MySQL bylo vyvinuto z [verze MySQL Community Edition](https://www.mysql.com/products/community/)pomocí modulu InnoDB.

MySQL používá schéma pojmenování X. Y. Z. X je hlavní verze, Y je dílčí verze a verze Z je oprava chyby. Další informace o schématu najdete v [dokumentaci k MySQL](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).

> [!NOTE]
> V rámci služby se k přesměrování připojení na instance serveru používá brána. Po navázání připojení se v klientovi MySQL zobrazí verze MySQL nastavená v bráně, a ne verze skutečně spuštěná na instanci serveru MySQL. Pokud chcete zjistit verzi instance serveru MySQL, na příkazovém řádku MySQL spusťte příkaz `SELECT VERSION();`.

Azure Database for MySQL aktuálně podporuje následující verze:

## <a name="mysql-version-56"></a>MySQL verze 5,6

Oprava chyby verze: 5.6.44

Další informace o vylepšeních a opravách v této verzi najdete v [poznámkách k verzi](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-44.html) MySQL.

## <a name="mysql-version-57"></a>MySQL verze 5,7

Oprava chyby verze: 5.7.26

Další informace o vylepšeních a opravách v této verzi najdete v [poznámkách k verzi](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-26.html) MySQL.

## <a name="mysql-version-80"></a>MySQL verze 8,0

> [!IMPORTANT]
> MySQL 8,0 je aktuálně ve verzi Preview.

Oprava chyby verze: 8.0.15

Další informace o vylepšeních a opravách v této verzi najdete v [poznámkách k verzi](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) MySQL.

## <a name="managing-updates-and-upgrades"></a>Správa aktualizací a upgradů
Služba automaticky spravuje opravy pro aktualizace verze opravy chyb. Například 5.7.20 na 5.7.21.  

V současné době se nepodporují upgrady Poda hlavních verzí. Například upgrade z MySQL 5,6 na MySQL 5,7 není podporován. Pokud chcete upgradovat z 5,6 na 5,7, proveďte [Výpis paměti a obnovte](./concepts-migrate-dump-restore.md) na server, který byl vytvořen s novou verzí modulu.

## <a name="next-steps"></a>Další kroky

Informace o konkrétních kvótách prostředků a omezeních založených na vaší **úrovni služby**najdete v tématu [úrovně služeb](./concepts-pricing-tiers.md) .
