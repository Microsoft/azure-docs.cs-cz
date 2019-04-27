---
title: Azure Database pro MariaDB ovladače a kompatibility nástroje pro správu
description: Tento článek popisuje MariaDB ovladačů a nástrojů pro správu, které jsou kompatibilní s využitím Azure Database pro MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/19/2019
ms.openlocfilehash: 7a3d9a5f87a565625052fc54e3ecccc99fd928a7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61386799"
---
# <a name="mariadb-drivers-and-management-tools-compatible-with-azure-database-for-mariadb"></a>MariaDB ovladačů a nástrojů pro správu kompatibilní s využitím Azure Database pro MariaDB

Tento článek popisuje ovladačů a nástrojů pro správu, které jsou kompatibilní s využitím Azure Database pro MariaDB.

## <a name="mariadb-drivers"></a>Ovladače MariaDB

Azure Database pro MariaDB používá edici community MariaDB serveru. Proto je kompatibilní s celou řadu programovacích jazyků a ovladače. Rozhraní API a protokol pro MariaDB jsou kompatibilní s používaným v nástroji MySQL. To znamená, že konektory, které pracují s MySQL by měl fungovat i pro MariaDB.

Cílem je podporovat tři nejnovější verze MariaDB ovladače a úsilí s tvůrci používající opensourcovou komunitou neustále zlepšit funkčnost a použitelnost MariaDB ovladačů pokračovat. Seznam ovladačů, které byly testovány a vyhodnocený jako kompatibilní s využitím Azure Database pro MariaDB 10.2 je uvedený v následující tabulce:

**Ovladač** | **Odkazy** | **Kompatibilní verze** | **Nekompatibilní verze** | **Poznámky**
---|---|---|---|---
PHP | https://secure.php.net/downloads.php | 5.5, 5.6, 7.x | 5.3 | Pro PHP 7.0 připojení pomocí SSL MySQLi přidejte MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT v připojovacím řetězci. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> Sada PDO: ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` možnost na hodnotu false.
.NET | [MySqlConnector on GitHub](https://github.com/mysql-net/MySqlConnector) <br> [Instalace balíčku od Nugetu](https://www.nuget.org/packages/MySqlConnector/) | 0.27 a po provedení | 0.26.5 a před |
MySQL Connector/NET | [MySQL Connector/NET](https://github.com/mysql/mysql-connector-net) | 8.0, 7.0, 6.10 |  | Kódování chyb může způsobit selhání v některých systémech Windows UTF8 připojení.
Node.js |  [MySQLjs na Githubu](https://github.com/mysqljs/mysql/) <br> Instalační balíček z NPM:<br> Spustit `npm install mysql` z NPM | 2.15 | 2.14.1 a před
GO | https://github.com/go-sql-driver/mysql/releases | 1.3, 1.4 | 1.2 a před | Použití `allowNativePasswords=true` v připojovacím řetězci pro verzi 1.3. Verze 1.4 obsahuje opravu a `allowNativePasswords=true` se už nevyžaduje.
Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2.0, 2.1, 2.2 | 1.2.2 a před |
Java | https://downloads.mariadb.org/connector-java/ | 2.1, 2.0, 1.6 | 1.5.5 a před |

## <a name="management-tools"></a>Nástroje pro správu

Výhodou kompatibility se rozšiřuje do nástrojů pro správu databází i. Svoje stávající nástroje by měla fungovat s využitím Azure Database pro MariaDB, tak dlouho, dokud manipulace s databázemi funguje v rámci uživatelských oprávnění. V následující tabulce jsou uvedeny tři běžné nástroje pro správu databází, které byly testovány a vyhodnocený jako kompatibilní s využitím Azure Database pro MariaDB 10.2:

| | **Aplikace MySQL Workbench 6.x a vyšší** | **Navicat 12** | **Phpmyadmin zobrazuje 4.x a vyšší**
---|---|---|---
Vytvoření, aktualizaci, čtení, zápisu a odstranění | X | X | X
Připojení SSL | X | X | X
Automatické dokončování dotazů SQL | X | X |
Import a Export dat | X | X | X
Export do několika formátů | X | X | X
Zálohování a obnovení |  | X |
Zobrazit parametry serveru | X | X | X
Zobrazení připojení klientů | X | X | X

## <a name="next-steps"></a>Další postup

- [Řešení problémů s připojením ke službě Azure Database pro MariaDB](howto-troubleshoot-common-connection-issues.md)