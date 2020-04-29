---
title: Podporované verze – Azure Database for MariaDB
description: Zjistěte, které verze serveru MariaDB se ve službě Azure Database for MariaDB podporují.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 361ba17532d27a7020be1b6874993da999f48604
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79527703"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>Podporované verze serveru Azure Database for MariaDB

Azure Database for MariaDB bylo vyvinuto z open-source [serveru MariaDB](https://downloads.mariadb.org/)pomocí modulu InnoDB.

MariaDB používá schéma pojmenování X. Y. Z. X je hlavní verze, Y je dílčí verze a Z je verze opravy.

> [!NOTE]
> V rámci služby se k přesměrování připojení na instance serveru používá brána. Po navázání připojení zobrazí klient MySQL v bráně verzi MariaDB sady, ne skutečnou verzi spuštěnou v instanci serveru MariaDB. K určení verze instance serveru MariaDB použijte `SELECT VERSION();` příkaz.

Azure Database for MariaDB aktuálně podporuje následující verzi:

## <a name="mariadb-version-102"></a>MariaDB verze 10,2

Verze opravy: 10.2.25

Další informace o vylepšeních a opravách v této verzi najdete v [dokumentaci k MariaDB](https://mariadb.com/kb/en/library/mariadb-10225-release-notes/) .

## <a name="mariadb-version-103"></a>MariaDB verze 10,3

Verze opravy: 10.3.16

Další informace o vylepšeních a opravách v této verzi najdete v [dokumentaci k MariaDB](https://mariadb.com/kb/en/library/mariadb-10316-release-notes/) .

## <a name="managing-updates-and-upgrades"></a>Správa aktualizací a upgradů
Služba automaticky spravuje upgrady pro aktualizace oprav. Například 10.2.21 na 10.2.23.  

Upgrady dílčích a hlavních verzí se v současné době nepodporují. Například upgrade z MariaDB 10.2 na MariaDB 10.3 se nepodporuje. Pokud chcete upgradovat z 10,2 na 10,3, proveďte [Výpis paměti a obnovte](./howto-migrate-dump-restore.md) na server, který byl vytvořen s novou verzí modulu.

## <a name="next-steps"></a>Další kroky

- Informace o konkrétních kvótách prostředků a omezeních založených na vaší **úrovni služby**najdete v tématu [úrovně služeb](./concepts-pricing-tiers.md).
