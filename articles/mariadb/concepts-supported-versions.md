---
title: Podporované verze ve službě Azure Database pro MariaDB
description: Popisuje podporované verze ve službě Azure Database pro MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 760cb39ea1f3980faba348c7aa1de68a66b20a8d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67065726"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>Podporované – Azure Database pro MariaDB verze serveru

Azure Database pro MariaDB vyvinula z open source [MariaDB Server](https://downloads.mariadb.org/), pomocí modul InnoDB. Azure Database pro MariaDB aktuálně podporuje následující verze:

## <a name="mariadb-version-102"></a>MariaDB Version 10.2

Odkazovat [MariaDB dokumentaci](https://mariadb.com/kb/en/library/mariadb-10223-release-notes/) Další informace o vylepšeních a opravách v MariaDB 10.2.23.

## <a name="mariadb-version-103"></a>MariaDB Version 10.3

Odkazovat [MariaDB dokumentaci](https://mariadb.com/kb/en/library/mariadb-10314-release-notes/) Další informace o vylepšeních a opravách v MariaDB 10.3.14.

> [!NOTE]
> Ve službě brány slouží k přesměrování připojení k instancím serveru. Po navázání připojení k MySQL klient se zobrazí verze MariaDB, nastavte v bráně, nikoli skutečnou verzi běžící na instanci serveru MariaDB. Chcete-li určit verzi instance serveru MariaDB, použijte `SELECT VERSION();` zadejte na příkazovém řádku MySQL.

## <a name="managing-updates-and-upgrades"></a>Správa aktualizací a upgradů

Služba automaticky spravuje opravy vedlejší verze aktualizace.

## <a name="next-steps"></a>Další postup

- Informace o konkrétní prostředek kvóty a omezení na základě vašich **úroveň služby**, naleznete v tématu [úrovně služeb](./concepts-pricing-tiers.md).
