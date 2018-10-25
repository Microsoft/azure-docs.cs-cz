---
title: Ovladače MySQL a kompatibilita nástroje správy
description: Tento článek popisuje ovladače MySQL a nástroje pro správu, které jsou kompatibilní s využitím Azure Database for MySQL.
services: mysql
author: ajlam
ms.author: andrela
editor: jasonwhowell
manager: kfile
ms.service: mysql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 9e56c2bd65f8a9a517a7cdebe02a1d051c689df6
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2018
ms.locfileid: "49985881"
---
# <a name="mysql-drivers-and-management-tools-compatible-with-azure-database-for-mysql"></a>Ovladače MySQL a nástroje pro správu kompatibilní s využitím Azure Database for MySQL
Tento článek popisuje ovladačů a nástrojů pro správu, které jsou kompatibilní s využitím Azure Database for MySQL.

## <a name="mysql-drivers"></a>Ovladače MySQL
Azure Database for MySQL používá na světě nejoblíbenější community edition databázi MySQL. Proto je kompatibilní s celou řadu programovacích jazyků a ovladače. Cílem je podporovat tři nejnovější verze ovladače MySQL a pokračujte úsilí s tvůrci používající opensourcovou komunitou neustále zlepšit funkčnost a použitelnost ovladače MySQL. Seznam ovladačů, které byly testovány a nenašel se kvůli kompatibilitě s – Azure Database for MySQL 5.6 a 5.7 je uvedený v následující tabulce:

| **Ovladač** | **Odkazy** | **Kompatibilní verze** | **Nekompatibilní verze** | **Poznámky** |
| :-------- | :------------------------ | :----------- | :---------------------- | :--------------------------------------- |
| PHP | https://secure.php.net/downloads.php | 5.5 5.6 7.x | 5.3 | Pro PHP 7.0 připojení pomocí SSL MySQLi přidejte MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT v připojovacím řetězci. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> Sada PDO: ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` možnost na hodnotu false.|
| .Net | [MySqlConnector na Githubu](https://github.com/mysql-net/MySqlConnector) <br> [Instalace balíčku od Nugetu](https://www.nuget.org/packages/MySqlConnector/) | 0.27 a po provedení | 0.26.5 a před | |
| Prostředí Nodejs |  [MySQLjs na Githubu](https://github.com/mysqljs/mysql/releases) <br> Instalační balíček z NPM:<br> Spustit `npm install mysql` z NPM | 2.15 | 2.14.1 a před | |
| PŘEJÍT | https://github.com/go-sql-driver/mysql/releases | 1.3 | 1.2 a před | Použít allowNativePasswords = true v připojovacím řetězci |
| Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2.0, 2.1, 2.2 | 1.2.2 a před | |
| Java | https://downloads.mariadb.org/connector-java/ | 2.1 2.0 1.6 | 1.5.5 a před | |

## <a name="management-tools"></a>Nástroje pro správu
Výhodou kompatibility se rozšiřuje do nástrojů pro správu databází i. Svoje stávající nástroje by měla dál pracovat se službou Azure Database for MySQL, tak dlouho, dokud manipulace s databázemi funguje v rámci uživatelských oprávnění. V následující tabulce jsou uvedeny tři běžné nástroje pro správu databází, které byly testovány a nenašel se kvůli kompatibilitě s – Azure Database for MySQL 5.6 a 5.7:

|                                     | **Aplikace MySQL Workbench 6.x a vyšší** | **Navicat 12** | **Phpmyadmin zobrazuje 4.x a vyšší** |
| :---------------------------------- | :----------------------------- | :------------- | :-------------------------|
| Vytvoření, aktualizaci, čtení, zápisu a odstranění | × | × | × |
| Připojení SSL | × | × | × |
| Automatické dokončování dotazů SQL | × | × |  |
| Import a Export dat | × | × | × |
| Export do několika formátů | × | × | × |
| Zálohování a obnovení |  | × |  |
| Zobrazit parametry serveru | × | × | × |
| Zobrazení připojení klientů | × | × | × |
