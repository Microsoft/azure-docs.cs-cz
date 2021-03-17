---
title: Podporované verze – Azure Database for MariaDB
description: Zjistěte, které verze serveru MariaDB se ve službě Azure Database for MariaDB podporují.
author: savjani
ms.author: pariks
ms.service: jroth
ms.topic: conceptual
ms.date: 7/20/2020
ms.openlocfilehash: 61add327852471932d75c746127c64b47bb4f3b2
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2021
ms.locfileid: "98662436"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>Podporované verze serveru Azure Database for MariaDB

Azure Database for MariaDB bylo vyvinuto z open-source [serveru MariaDB](https://downloads.mariadb.org/)pomocí modulu InnoDB.

MariaDB používá schéma pojmenování X. Y. Z. X je hlavní verze, Y je dílčí verze a Z je verze opravy.

> [!NOTE]
> V rámci služby se k přesměrování připojení na instance serveru používá brána. Po navázání připojení zobrazí klient MySQL v bráně verzi MariaDB sady, ne skutečnou verzi spuštěnou v instanci serveru MariaDB. K určení verze instance serveru MariaDB použijte `SELECT VERSION();` příkaz.

Azure Database for MariaDB aktuálně podporuje následující verzi:

## <a name="mariadb-version-102"></a>MariaDB verze 10,2

Verze opravy: 10.2.32

Další informace o vylepšeních a opravách v této verzi najdete v [dokumentaci k MariaDB](https://mariadb.com/kb/en/mariadb-10232-release-notes/) .

## <a name="mariadb-version-103"></a>MariaDB verze 10,3

Verze opravy: 10.3.23

Další informace o vylepšeních a opravách v této verzi najdete v [dokumentaci k MariaDB](https://mariadb.com/kb/en/mariadb-10323-release-notes/) .

## <a name="managing-updates-and-upgrades"></a>Správa aktualizací a upgradů
Služba automaticky spravuje upgrady pro aktualizace oprav. Například 10.2.21 na 10.2.23.  

Upgrady dílčích a hlavních verzí se v současné době nepodporují. Například upgrade z MariaDB 10.2 na MariaDB 10.3 se nepodporuje. Pokud chcete upgradovat z 10,2 na 10,3, proveďte [Výpis paměti a obnovte](./howto-migrate-dump-restore.md) na server, který byl vytvořen s novou verzí modulu.

## <a name="next-steps"></a>Další kroky

- Informace o konkrétních kvótách prostředků a omezeních založených na vaší **úrovni služby** najdete v tématu [úrovně služeb](./concepts-pricing-tiers.md).
