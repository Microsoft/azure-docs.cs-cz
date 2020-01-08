---
title: Podporované verze – Azure Database for MySQL
description: Zjistěte, které verze MySQL serveru jsou podporované ve službě Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 05d4ecd58f6febff75212f1ad88b60be4f23c2a7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454336"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Podporované verze serveru Azure Database for MySQL

Azure Database for MySQL bylo vyvinuto z [verze MySQL Community Edition](https://www.mysql.com/products/community/)pomocí modulu InnoDB.

MySQL používá schéma pojmenování X. Y. Z. X je hlavní verze, Y je dílčí verze a verze Z je oprava chyby. Další informace o schématu najdete v [dokumentaci k MySQL](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).

> [!NOTE]
> V rámci služby se k přesměrování připojení na instance serveru používá brána. Po navázání připojení se v klientovi MySQL zobrazí verze MySQL nastavená v bráně, a ne verze skutečně spuštěná na instanci serveru MySQL. Pokud chcete zjistit verzi instance serveru MySQL, na příkazovém řádku MySQL spusťte příkaz `SELECT VERSION();`.

Azure Database for MySQL aktuálně podporuje následující verze:

## <a name="mysql-version-56"></a>MySQL verze 5,6

Oprava chyby verze: 5.6.45

Další informace o vylepšeních a opravách v této verzi najdete v [poznámkách k verzi](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-45.html) MySQL.

## <a name="mysql-version-57"></a>MySQL verze 5,7

Oprava chyby verze: 5.7.27

Další informace o vylepšeních a opravách v této verzi najdete v [poznámkách k verzi](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-27.html) MySQL.

## <a name="mysql-version-80"></a>MySQL verze 8,0

Oprava chyby verze: 8.0.15

Další informace o vylepšeních a opravách v této verzi najdete v [poznámkách k verzi](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) MySQL.

## <a name="managing-updates-and-upgrades"></a>Správa aktualizací a upgradů
Služba automaticky spravuje opravy pro aktualizace verze opravy chyb. Například 5.7.20 na 5.7.21.  

Upgrady dílčích a hlavních verzí se v současné době nepodporují. Například upgrade z MySQL 5.6 na MySQL 5.7 se nepodporuje. Pokud chcete upgradovat z verze 5.6 na verzi 5.7, pořiďte [výpis paměti a obnovte](./concepts-migrate-dump-restore.md) ji na serveru vytvořeném s novou verzí jádra.

## <a name="next-steps"></a>Další kroky

Informace o konkrétních kvótách prostředků a omezeních založených na vaší **úrovni služby**najdete v tématu [úrovně služeb](./concepts-pricing-tiers.md) .
