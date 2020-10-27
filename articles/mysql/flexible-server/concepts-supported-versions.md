---
title: Podporované verze – Azure Database for MySQL flexibilní Server
description: Informace o tom, které verze serveru MySQL se podporují na Azure Database for MySQL flexibilním serveru
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 11cefb1a29f7139f137221ab6fb924cc656da15d
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92547193"
---
# <a name="supported-versions-for-azure-database-for-mysql---flexible-server"></a>Podporované verze pro Azure Database for MySQL – flexibilní Server


> [!IMPORTANT]
> Azure Database for MySQL – flexibilní Server je momentálně ve verzi Public Preview.


Azure Database for MySQL flexibilní Server používá InnoDB modul pro [MySQL Community Edition](https://www.mysql.com/products/community/).

MySQL používá schéma pojmenování X. Y. Z. X je hlavní verze, Y je dílčí verze a verze Z je oprava chyby. Další informace o schématu najdete v [dokumentaci k MySQL](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).

> [!NOTE]
> Pokud chcete zjistit verzi instance serveru MySQL, na příkazovém řádku MySQL spusťte příkaz `SELECT VERSION();`.

Azure Database for MySQL aktuálně podporuje následující verze:

## <a name="mysql-version-57"></a>MySQL verze 5,7

Oprava chyby verze: 5.7.29

Služba provádí automatizované opravy základního hardwaru, operačního systému a databázového stroje. Opravy zahrnují zabezpečení a aktualizace softwaru. Pro modul MySQL jsou jako součást plánované verze údržby zahrnuté i upgrady podverze. Uživatelé můžou nakonfigurovat plán oprav tak, aby byl spravovaný systémem, nebo definovat vlastní plán. Během plánu údržby se oprava použije a server může po dokončení aktualizace vyžadovat restart jako součást procesu oprav. Díky vlastnímu plánu můžou uživatelé provádět předvídatelné cykly oprav a zvolit časové období údržby s minimálním dopadem na firmu. Obecně platí, že služba dodržuje plán měsíčního vydání jako součást průběžné integrace a vydání.

Další informace o vylepšeních a opravách v této verzi najdete v [poznámkách k verzi](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html) MySQL.

## <a name="managing-updates-and-upgrades"></a>Správa aktualizací a upgradů
Služba automaticky spravuje opravy pro aktualizace verze opravy chyb. Například 5.7.29 na 5.7.30.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
>[Vytvoření aplikace v PHP ve Windows pomocí MySQL](../../app-service/tutorial-php-mysql-app.md)<br/>
>[Sestavení aplikace PHP v systému Linux pomocí MySQL](../../app-service/tutorial-php-mysql-app.md?pivots=platform-linux%253fpivots%253dplatform-linux)<br/>
>[Sestavení jarní aplikace založené na jazyce Java pomocí MySQL](/azure/developer/java/spring-framework/spring-app-service-e2e?tabs=bash)<br/>