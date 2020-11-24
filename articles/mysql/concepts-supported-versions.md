---
title: Podporované verze – Azure Database for MySQL
description: Zjistěte, které verze MySQL serveru jsou podporované ve službě Azure Database for MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 6/3/2020
ms.openlocfilehash: 8033117d9e3c31f8aa9bba06afb7c3b1b7bba67f
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "95751024"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Podporované verze serveru Azure Database for MySQL

Azure Database for MySQL bylo vyvinuto z [verze MySQL Community Edition](https://www.mysql.com/products/community/)pomocí modulu InnoDB.

MySQL používá schéma pojmenování X. Y. Z. X je hlavní verze, Y je dílčí verze a verze Z je oprava chyby. Další informace o schématu najdete v [dokumentaci k MySQL](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).


> [!NOTE]
> V rámci služby se k přesměrování připojení na instance serveru používá brána. Po navázání připojení se v klientovi MySQL zobrazí verze MySQL nastavená v bráně, a ne verze skutečně spuštěná na instanci serveru MySQL. Pokud chcete zjistit verzi instance serveru MySQL, na příkazovém řádku MySQL spusťte příkaz `SELECT VERSION();`.

Azure Database for MySQL aktuálně podporuje následující verze:

## <a name="mysql-version-56"></a>MySQL verze 5,6

Oprava chyby verze: 5.6.47

Další informace o vylepšeních a opravách v této verzi najdete v [poznámkách k verzi](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-47.html) MySQL.

## <a name="mysql-version-57"></a>MySQL verze 5,7

Oprava chyby verze: 5.7.29

Další informace o vylepšeních a opravách v této verzi najdete v [poznámkách k verzi](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html) MySQL.

## <a name="mysql-version-80"></a>MySQL verze 8,0

Oprava chyby verze: 8.0.15

Další informace o vylepšeních a opravách v této verzi najdete v [poznámkách k verzi](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) MySQL.

## <a name="managing-updates-and-upgrades"></a>Správa aktualizací a upgradů
Služba automaticky spravuje opravy pro aktualizace verze opravy chyb. Například 5.7.20 na 5.7.21.  

Upgrade hlavní verze je aktuálně podporován službou pro upgrady z MySQL v 5.6 až v 5.7. Další podrobnosti najdete v tématu [jak provádět upgrady hlavní verze](how-to-major-version-upgrade.md). Pokud chcete upgradovat z 5,7 na 8,0, doporučujeme, abyste provedli [Výpis a obnovení](./concepts-migrate-dump-restore.md) na server, který byl vytvořen s novou verzí modulu.

## <a name="next-steps"></a>Další kroky

- Podrobnosti o Azure Database for MySQL zásad správy verzí najdete v [tomto dokumentu](concepts-version-policy.md).
- Informace o konkrétních kvótách prostředků a omezeních založených na vaší **úrovni služby** najdete v tématu [úrovně služeb](./concepts-pricing-tiers.md) .
