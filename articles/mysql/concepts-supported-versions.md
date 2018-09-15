---
title: Podporované verze ve službě Azure Database for MySQL
description: Popisuje podporované verze ve službě Azure Database for MySQL.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 05/23/2018
ms.openlocfilehash: 1b6dded1521489353e65b630ef5432ba6ff8f3e8
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2018
ms.locfileid: "45631561"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Podporované – Azure Database for MySQL server verze
Azure Database for MySQL byla vyvinuta z [MySQL Community Edition](https://www.mysql.com/products/community/), pomocí modul InnoDB.  Azure Database for MySQL aktuálně podporuje následující verze:

## <a name="mysql-version-5639"></a>Verze MySQL 5.6.39
Odkazovat na MySQL [dokumentaci](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-39.html) Další informace o vylepšeních a opravách v MySQL 5.6.39.

## <a name="mysql-version-5721"></a>Verze MySQL 5.7.21
Odkazovat na MySQL [dokumentaci](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-21.html) Další informace o vylepšení a oprav v MySQL 5.7.21.

> [!NOTE]
> Ve službě brány slouží k přesměrování připojení k instancím serveru. Po navázání připojení k MySQL klienta zobrazí verzi nastavit v bráně, nikoli skutečnou verzi běžící na instanci MySQL serveru MySQL. Chcete-li určit verzi instance serveru MySQL, použijte `SELECT VERSION();` zadejte na příkazovém řádku MySQL.

## <a name="managing-updates-and-upgrades"></a>Správa aktualizací a upgradů
Služba automaticky spravuje opravy vedlejší verze aktualizace. Upgrade hlavní verze v současné době se nepodporuje. Například upgrade z MySQL 5.6 na MySQL 5.7 není podporována. Pokud chcete upgradovat na novou hlavní verzi, [výpisu a obnovení](./concepts-migrate-dump-restore.md) ho na server, který byl vytvořen v nové verzi modulu.

## <a name="next-steps"></a>Další postup

Informace o konkrétní prostředek kvóty a omezení na základě vašich **úroveň služby**, naleznete v tématu [úrovně služeb](./concepts-pricing-tiers.md)
