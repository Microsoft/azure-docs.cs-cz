---
title: Podporované verze ve službě Azure Database pro MariaDB
description: Popisuje podporované verze ve službě Azure Database pro MariaDB.
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: aa83b9955839aaa03aa2ebf46c9e464dc75c8d3a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46977498"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>Podporované – Azure Database pro MariaDB verze serveru
Azure Database pro MariaDB vyvinula z open source [MariaDB Server](https://downloads.mariadb.org/), pomocí modul InnoDB. Azure Database pro MariaDB aktuálně podporuje následující verze:

## <a name="mariadb-version-10217"></a>Verze 10.2.17 MariaDB
Odkazovat [MariaDB dokumentaci](https://downloads.mariadb.org/mariadb/10.2.17/) Další informace o vylepšeních a opravách v MariaDB 10.2.17.

> [!NOTE]
> Ve službě brány slouží k přesměrování připojení k instancím serveru. Po navázání připojení k MySQL klient se zobrazí verze MariaDB, nastavte v bráně, nikoli skutečnou verzi běžící na instanci serveru MariaDB. Chcete-li určit verzi instance serveru MariaDB, použijte `SELECT VERSION();` zadejte na příkazovém řádku MySQL.

## <a name="managing-updates-and-upgrades"></a>Správa aktualizací a upgradů
Služba automaticky spravuje opravy vedlejší verze aktualizace.

## <a name="next-steps"></a>Další postup
Informace o konkrétní prostředek kvóty a omezení na základě vašich **úroveň služby**, naleznete v tématu [úrovně služeb](./concepts-pricing-tiers.md)