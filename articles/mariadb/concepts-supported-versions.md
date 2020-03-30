---
title: Podporované verze – Azure Database for MariaDB
description: Zjistěte, které verze serveru MariaDB jsou podporované ve službě Azure Database for MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 361ba17532d27a7020be1b6874993da999f48604
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79527703"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>Podporovaná databáze Azure pro verze serverů MariaDB

Azure Database for MariaDB byl vyvinut z open source [MariaDB Server](https://downloads.mariadb.org/)pomocí modulu InnoDB.

MariaDB používá schéma pojmenování X.Y.Z. X je hlavní verze, Y je dílčí verze a Z je verze opravy.

> [!NOTE]
> V rámci služby se k přesměrování připojení na instance serveru používá brána. Po navázání připojení klient MySQL zobrazí verzi MariaDB nastavenou v bráně, nikoli skutečnou verzi spuštěnou na instanci serveru MariaDB. Chcete-li určit verzi instance serveru MariaDB, použijte `SELECT VERSION();` příkaz.

Azure Database for MariaDB aktuálně podporuje následující verzi:

## <a name="mariadb-version-102"></a>MariaDB verze 10.2

Verze opravy: 10.2.25

Další informace o vylepšeních a opravách v této verzi naleznete v dokumentaci k [Mariádce](https://mariadb.com/kb/en/library/mariadb-10225-release-notes/) DB.

## <a name="mariadb-version-103"></a>MariaDB verze 10.3

Verze opravy: 10.3.16

Další informace o vylepšeních a opravách v této verzi naleznete v dokumentaci k [Mariádce](https://mariadb.com/kb/en/library/mariadb-10316-release-notes/) DB.

## <a name="managing-updates-and-upgrades"></a>Správa aktualizací a upgradů
Služba automaticky spravuje upgrady aktualizací oprav. Například 10.2.21 až 10.2.23.  

Upgrady dílčích a hlavních verzí se v současné době nepodporují. Například upgrade z MariaDB 10.2 na MariaDB 10.3 se nepodporuje. Pokud chcete upgradovat z 10.2 na 10.3, vezměte [výpis a obnovte](./howto-migrate-dump-restore.md) jej na server, který byl vytvořen s novou verzí motoru.

## <a name="next-steps"></a>Další kroky

- Informace o konkrétních kvótách prostředků a omezeních na základě **vaší úrovně služeb**naleznete v [tématu Úrovně služeb](./concepts-pricing-tiers.md).
