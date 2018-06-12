---
title: Podporované verze ve službě Azure Database pro databázi MySQL
description: Popisuje podporované verze ve službě Azure Database pro databázi MySQL.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 05/23/2018
ms.openlocfilehash: c9a533ed9b9eb9ac53a02439b98a78954c7aaa11
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35265244"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Podporované Azure databáze MySQL verzí serveru
Azure databáze pro databázi MySQL byla vyvinuta z [MySQL Community Edition](https://www.mysql.com/products/community/), pomocí modulu InnoDB.  Databáze pro databázi MySQL Azure aktuálně podporuje následující verze:

## <a name="mysql-version-5639"></a>Verze databáze MySQL 5.6.39
Odkazovat MySQL [dokumentace](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-39.html) Další informace o vylepšení a opravy v MySQL 5.6.39.

## <a name="mysql-version-5721"></a>Verze databáze MySQL 5.7.21
Odkazovat MySQL [dokumentace](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-21.html) Další informace o vylepšení a opravy v MySQL 5.7.21.

> [!NOTE]
> V rámci služby Brána slouží k přesměrování připojení na instancích serveru. Po navázání připojení zobrazí klient MySQL verzi MySQL nastavit v bráně není ve skutečnosti verze spuštěné v instanci serveru vaší databáze MySQL. Chcete-li určit verzi instance serveru MySQL, použijte `SELECT VERSION();` příkazu příkazového řádku MySQL. 

## <a name="managing-updates-and-upgrades"></a>Správa aktualizací a upgradů
Služba automaticky spravuje opravy pro vedlejší verze aktualizace. Nepodporují se upgrady hlavní verzi (např.) upgrade z databáze MySQL 5.6 na MySQL 5.7).

## <a name="next-steps"></a>Další postup

Informace o konkrétní prostředek kvóty a omezení na základě vaší **vrstvy služby**, najdete v části [úrovních služeb](./concepts-pricing-tiers.md)
