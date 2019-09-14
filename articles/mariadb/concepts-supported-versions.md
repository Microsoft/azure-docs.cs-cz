---
title: Podporované verze v Azure Database for MariaDB
description: V této části najdete popis podporovaných verzí v Azure Database for MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/12/2019
ms.openlocfilehash: e5d1dbc8c212d4cdefb12fb740a454324d3adfa1
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2019
ms.locfileid: "70962924"
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

V současné době se nepodporují upgrady Poda hlavních verzí. Například upgrade z MariaDB 10,2 na MariaDB 10,3 není podporován. Pokud chcete upgradovat z 10,2 na 10,3, proveďte [Výpis paměti a obnovte](./howto-migrate-dump-restore.md) na server, který byl vytvořen s novou verzí modulu.

## <a name="next-steps"></a>Další postup

- Informace o konkrétních kvótách prostředků a omezeních založených na vaší **úrovni služby**najdete v tématu [úrovně služeb](./concepts-pricing-tiers.md).
