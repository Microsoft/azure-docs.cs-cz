---
title: Podporované verze – Azure Database for MySQL
description: Zjistěte, které verze MySQL serveru jsou podporované ve službě Azure Database for MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 6/3/2020
ms.openlocfilehash: 8b85307f01a11366a2147c947f26658f548932e8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103467710"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Podporované verze serveru Azure Database for MySQL

Azure Database for MySQL bylo vyvinuto z [verze MySQL Community Edition](https://www.mysql.com/products/community/)pomocí modulu úložiště InnoDB. Služba podporuje všechny aktuální hlavní verze, které podporuje komunita konkrétně MySQL 5,6, 5,7 a 8,0. MySQL používá schéma pojmenování X. Y. Z, kde X je hlavní verze, Y je podverze a verze Z je oprava chyby. Další informace o schématu najdete v [dokumentaci k MySQL](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).

> [!NOTE]
> V případě možnosti nasazení s jedním serverem slouží brána k přesměrování připojení k instancím serveru. Po navázání připojení se v klientovi MySQL zobrazí verze MySQL nastavená v bráně, a ne verze skutečně spuštěná na instanci serveru MySQL. Pokud chcete zjistit verzi instance serveru MySQL, na příkazovém řádku MySQL spusťte příkaz `SELECT VERSION();`.

Azure Database for MySQL aktuálně podporuje následující hlavní a dílčí verze MySQL:


| Verze | Jeden server <br/> Aktuální dílčí verze |Flexibilní server (Preview) <br/> Aktuální dílčí verze  |
|:-------------------|:-------------------------------------------|:---------------------------------------------|
|MySQL verze 5,6 |  [5.6.47](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-47.html) (vyřazeno) | Nepodporováno|
|MySQL verze 5,7 | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html) | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html)|
|MySQL verze 8,0 | [8.0.15](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) | [8.0.21](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)|

Přečtěte si zásady podpory verzí pro vyřazené verze v [dokumentaci k zásadám podpory verzí.](concepts-version-policy.md#retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql)

## <a name="managing-updates-and-upgrades"></a>Správa aktualizací a upgradů
Služba automaticky spravuje opravy pro aktualizace verze opravy chyb. Například 5.7.20 na 5.7.21.  

Upgrade hlavní verze je aktuálně podporován službou pro upgrady z MySQL v 5.6 až v 5.7. Další podrobnosti najdete v tématu [jak provádět upgrady hlavní verze](how-to-major-version-upgrade.md). Pokud chcete upgradovat z 5,7 na 8,0, doporučujeme, abyste provedli [Výpis a obnovení](./concepts-migrate-dump-restore.md) na server, který byl vytvořen s novou verzí modulu.

## <a name="next-steps"></a>Další kroky

- Podrobnosti o Azure Database for MySQL zásad správy verzí najdete v [tomto dokumentu](concepts-version-policy.md).
- Informace o konkrétních kvótách prostředků a omezeních založených na vaší **úrovni služby** najdete v tématu [úrovně služeb](./concepts-pricing-tiers.md) .
