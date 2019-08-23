---
title: Podporované verze v Azure Database for MariaDB
description: V této části najdete popis podporovaných verzí v Azure Database for MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: 950294094584958e83f6a16630a6e1f897785e46
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69897291"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>Podporované verze serveru Azure Database for MariaDB

Azure Database for MariaDB bylo vyvinuto z open-source [serveru MariaDB](https://downloads.mariadb.org/)pomocí modulu InnoDB. 

MariaDB používá schéma pojmenování X. Y. Z. X je hlavní verze, Y je dílčí verze a Z je verze opravy.

> [!NOTE]
> V rámci služby se k přesměrování připojení k instancím serveru používá brána. Po navázání připojení zobrazí klient MySQL v bráně verzi MariaDB sady, ne skutečnou verzi spuštěnou v instanci serveru MariaDB. K určení verze instance serveru MariaDB použijte `SELECT VERSION();` příkaz.

Azure Database for MariaDB aktuálně podporuje následující verzi:

## <a name="mariadb-version-102"></a>MariaDB verze 10,2

Verze opravy: 10.2.23

Další informace o vylepšeních a opravách v MariaDB 10.2.23 najdete v [dokumentaci k MariaDB](https://mariadb.com/kb/en/library/mariadb-10223-release-notes/) .

## <a name="mariadb-version-103"></a>MariaDB verze 10,3

Verze opravy: 10.3.14

Další informace o vylepšeních a opravách v MariaDB 10.3.14 najdete v [dokumentaci k MariaDB](https://mariadb.com/kb/en/library/mariadb-10314-release-notes/) .

## <a name="managing-updates-and-upgrades"></a>Správa aktualizací a upgradů
Služba automaticky spravuje upgrady pro aktualizace oprav. Například 10.2.21 na 10.2.23.  

V současné době se nepodporují upgrady Poda hlavních verzí. Například upgrade z MariaDB 10,2 na MariaDB 10,3 není podporován. Pokud chcete upgradovat z 10,2 na 10,3, proveďte [Výpis paměti a obnovte](./howto-migrate-dump-restore.md) na server, který byl vytvořen s novou verzí modulu.

## <a name="next-steps"></a>Další postup

- Informace o konkrétních kvótách prostředků a omezeních založených na vaší **úrovni služby**najdete v tématu [úrovně služeb](./concepts-pricing-tiers.md).
