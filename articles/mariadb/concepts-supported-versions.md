---
title: Podporované verze ve službě Azure Database pro MariaDB
description: Popisuje podporované verze ve službě Azure Database pro MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 69330e9d5a05fbcc892889f70a04f5eb4a4a2fb9
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "60935547"
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
