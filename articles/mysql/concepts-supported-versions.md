---
title: Podporované verze – Azure Database for MySQL
description: Zjistěte, které verze MySQL serveru jsou podporované ve službě Azure Database for MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 6/3/2020
ms.openlocfilehash: d84f56e5ae0f3c364a0fd3a08ccb173d7c65a5e2
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2021
ms.locfileid: "106121757"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Podporované verze serveru Azure Database for MySQL

Azure Database for MySQL bylo vyvinuto z [verze MySQL Community Edition](https://www.mysql.com/products/community/)pomocí modulu úložiště InnoDB. Služba podporuje všechny aktuální hlavní verze, které podporuje komunita konkrétně MySQL 5,6, 5,7 a 8,0. MySQL používá schéma pojmenování X. Y. Z, kde X je hlavní verze, Y je podverze a verze Z je oprava chyby. Další informace o schématu najdete v [dokumentaci k MySQL](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).



## <a name="connect-to-a-gateway-node-that-is-running-a-specific-mysql-version"></a>Připojení k uzlu brány, na kterém je spuštěná konkrétní verze MySQL

V případě možnosti nasazení s jedním serverem slouží brána k přesměrování připojení k instancím serveru. Po navázání připojení se v klientovi MySQL zobrazí verze MySQL nastavená v bráně, a ne verze skutečně spuštěná na instanci serveru MySQL. Pokud chcete zjistit verzi instance serveru MySQL, na příkazovém řádku MySQL spusťte příkaz `SELECT VERSION();`. Přečtěte si [architekturu připojení](https://docs.microsoft.com/azure/mysql/concepts-connectivity-architecture#connectivity-architecture) , kde najdete další informace o branách v architektuře Azure Database for MySQL Service.

Jelikož Azure Database for MySQL podporuje hlavní verzi v 5.6, v 5.7 a v 8.0, výchozí port 3306 pro připojení k Azure Database for MySQL spustí klienta MySQL verze 5,6 (nejméně společný jmenovatel), aby podporoval připojení k serverům všech 3 podporovaných hlavních verzí. Pokud však má vaše aplikace požadavek na připojení ke konkrétní hlavní verzi říká v 5.7 nebo v 8.0, můžete to provést změnou portu v připojovacím řetězci serveru.

V Azure Database for MySQL služba naslouchá uzly brány na portu 3308 pro klienty verze 5.7 a port 3309 pro klienty verze 8.0. Jinými slovy, pokud se chcete připojit k klientovi brány v 5.7, měli byste použít plně kvalifikovaný název serveru a port 3308 pro připojení k serveru z klientské aplikace. Podobně pokud se chcete připojit k klientovi brány v 8.0, můžete k připojení k serveru použít plně kvalifikovaný název serveru a port 3309. Další přehlednost najdete v následujícím příkladu.

:::image type="content" source="./media/concepts-supported-versions/concepts-supported-versions-gateway.png" alt-text="Příklad připojení pomocí různých verzí MySQL brány":::


## <a name="azure-database-for-mysql-currently-supports-the-following-major-and-minor-versions-of-mysql"></a>Azure Database for MySQL aktuálně podporuje následující hlavní a dílčí verze MySQL:


| Verze | [Jeden server](overview.md) <br/> Aktuální dílčí verze |[Flexibilní server (Preview)](/../flexible-server/overview.md) <br/> Aktuální dílčí verze  |
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
