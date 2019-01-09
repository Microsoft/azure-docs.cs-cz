---
title: Podporované verze ve službě Azure Database for MySQL
description: Popisuje podporované verze ve službě Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 01/07/2019
ms.openlocfilehash: ecd6466d8d7a7e4497d076ced0c9f2375d5dfb7f
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54106031"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Podporované – Azure Database for MySQL server verze
Azure Database for MySQL byla vyvinuta z [MySQL Community Edition](https://www.mysql.com/products/community/), pomocí modul InnoDB.

MySQL používá schéma pojmenování X.Y.Z. Hlavní verze je X, Y je dílčí verzí a Z vydání opravy. Další informace o schématu, najdete v článku [dokumentace ke službě MySQL](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).

Azure Database for MySQL aktuálně podporuje následující verze:

## <a name="mysql-version-56"></a>Verze MySQL 5.6

Oprava chyby verze: 5.6.39

Odkazovat na MySQL [poznámky k verzi](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-39.html) Další informace o vylepšeních a opravách v MySQL 5.6.39.

## <a name="mysql-version-57"></a>Verze MySQL 5.7

Oprava chyby verze: 5.7.21

Odkazovat na MySQL [poznámky k verzi](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-21.html) Další informace o vylepšení a oprav v MySQL 5.7.21.

> [!NOTE]
> Ve službě brány slouží k přesměrování připojení k instancím serveru. Po navázání připojení k MySQL klienta zobrazí verzi nastavit v bráně, nikoli skutečnou verzi běžící na instanci MySQL serveru MySQL. Chcete-li určit verzi instance serveru MySQL, použijte `SELECT VERSION();` zadejte na příkazovém řádku MySQL.

## <a name="managing-updates-and-upgrades"></a>Správa aktualizací a upgradů
Služba automaticky spravuje opravy chyb pro opravu chyby verze aktualizace. Například 5.7.20 k 5.7.21.  

V současné době nepodporují se upgrady vedlejší a hlavní verze. Například upgrade z MySQL 5.6 na MySQL 5.7 není podporována. Pokud chcete upgradovat z verze 5.6 na 5.7, [výpisu a obnovení](./concepts-migrate-dump-restore.md) ho na server, který byl vytvořen v nové verzi modulu.

## <a name="next-steps"></a>Další postup

Informace o konkrétní prostředek kvóty a omezení na základě vašich **úroveň služby**, naleznete v tématu [úrovně služeb](./concepts-pricing-tiers.md)
