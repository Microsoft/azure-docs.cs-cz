---
title: Podporované verze ve službě Azure Database pro databázi MySQL
description: Popisuje podporované verze ve službě Azure Database pro databázi MySQL.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 03/22/2018
ms.openlocfilehash: 53c8d51ddf9b7465a99b8b0685d7f6ce177fc526
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Podporované Azure databáze MySQL verzí serveru
Azure databáze pro databázi MySQL byla vyvinuta z [MySQL Community Edition](https://www.mysql.com/products/community/), pomocí modulu InnoDB.  Databáze pro databázi MySQL Azure aktuálně podporuje následující verze:

## <a name="mysql-version-5638"></a>Verze databáze MySQL 5.6.38
Odkazovat MySQL [dokumentace](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-38.html) Další informace o vylepšení a opravy v MySQL 5.6.38.

## <a name="mysql-version-5720"></a>MySQL Version 5.7.20
Odkazovat MySQL [dokumentace](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-20.html) Další informace o vylepšení a opravy v MySQL 5.7.20.

> [!NOTE]
> V rámci služby Brána slouží k přesměrování připojení na instancích serveru. Po navázání připojení zobrazí klient MySQL verzi MySQL nastavit v bráně není ve skutečnosti verze spuštěné v instanci serveru vaší databáze MySQL. Chcete-li určit verzi instance serveru MySQL, použijte `SELECT VERSION();` příkazu příkazového řádku MySQL. 

## <a name="managing-updates-and-upgrades"></a>Správa aktualizací a upgradů
Služba automaticky spravuje opravy pro vedlejší verze aktualizace. Nepodporují se upgrady hlavní verzi (např.) upgrade z databáze MySQL 5.6 na MySQL 5.7).

## <a name="next-steps"></a>Další postup

Informace o konkrétní prostředek kvóty a omezení na základě vaší **vrstvy služby**, najdete v části [úrovních služeb](./concepts-pricing-tiers.md)
