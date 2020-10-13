---
title: Kompatibilita ovladačů a nástrojů – Azure Database for MySQL
description: Tento článek popisuje ovladače a nástroje pro správu MySQL, které jsou kompatibilní s Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 8d222eca76f020703f89dd1bdd5848cec4bc52ca
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89006529"
---
# <a name="mysql-drivers-and-management-tools-compatible-with-azure-database-for-mysql"></a>Ovladače a nástroje pro správu MySQL kompatibilní s Azure Database for MySQL
Tento článek popisuje ovladače a nástroje pro správu, které jsou kompatibilní s Azure Database for MySQL.

## <a name="mysql-drivers"></a>Ovladače MySQL
Azure Database for MySQL používá nejoblíbenější komunitní edici Community databáze MySQL. Proto je kompatibilní s širokou škálou programovacích jazyků a ovladačů. Cílem je podpořit tři nejnovější verze ovladačů MySQL a snahy s autory z komunity Open sources, aby neustále vylepšily funkčnost a použitelnost ovladačů MySQL. V následující tabulce je uveden seznam ovladačů, které byly testovány a zjištěny jako kompatibilní s Azure Database for MySQL 5,6 a 5,7:

| **Programovací jazyk** | **Ovladač** | **Odkazy** | **Kompatibilní verze** | **Nekompatibilní verze** | **Poznámky** |
| :----------------------- | :--------- | :-------- | :---------------------- | :------------------------ | :-------- |
| PHP | MySQL, pdo_mysql, mysqlnd | https://secure.php.net/downloads.php | 5,5, 5,6, 7. x | 5.3 | V případě připojení PHP 7,0 k protokolu SSL MySQL přidejte MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT do připojovacího řetězce. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> Množina CHOP: ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` možnost na false.|
| .NET | Async MySQL Connector pro .NET | https://github.com/mysql-net/MySqlConnector <br> [Instalační balíček z NuGet](https://www.nuget.org/packages/MySqlConnector/) | 0,27 a za | 0.26.5 a před | |
| .NET | Konektor MySQL/NET | https://github.com/mysql/mysql-connector-net | 6.6.3, 7,0, 8,0 |  | Chyba kódování může způsobit selhání připojení u některých systémů Windows bez kódování UTF8. |
| Node.js | mysqljs | https://github.com/mysqljs/mysql/ <br> Instalační balíček z NPM:<br> Spustit `npm install mysql` z npm | 2.15 | 2.14.1 a před | |
| Node.js | uzel – mysql2 | https://github.com/sidorares/node-mysql2 | 1.3.4 + | | |
| Go | Přejít na ovladač MySQL | https://github.com/go-sql-driver/mysql/releases | 1,3, 1,4 | 1,2 a před | Použijte `allowNativePasswords=true` v připojovacím řetězci pro verzi 1,3. Verze 1,4 obsahuje opravu a `allowNativePasswords=true` již není nutná. |
| Python | Konektor MySQL/Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2,0, 2,1, 2,2, použití 8.0.16 + s MySQL 8,0  | 1.2.2 a před | |
| Python | PyMySQL | https://pypi.org/project/PyMySQL/ | 0.7.11, 0.8.0, 0.8.1, 0.9.3 + | 0.9.0-0.9.2 (regrese v web2py) | |
| Java | Konektor MariaDB/J | https://downloads.mariadb.org/connector-java/ | 2,1, 2,0, 1,6 | 1.5.5 a před | | 
| Java | Konektor MySQL/J | https://github.com/mysql/mysql-connector-j | 5.1.21 +, použití 8.0.17 + s MySQL 8,0 | 5.1.20 a nižší | |
| C | Konektor MySQL/C (libmysqlclient) | https://dev.mysql.com/doc/refman/5.7/en/c-api-implementations.html | 6.0.2 + | | |
| C | Konektor MySQL/rozhraní ODBC (MyODBC) | https://github.com/mysql/mysql-connector-odbc | 3.51.29 + | | |
| C++ | Konektor MySQL/C++ | https://github.com/mysql/mysql-connector-cpp | 1.1.9 + | 1.1.3 a níže | | 
| C++ | MySQL + +| https://github.com/tangentsoft/mysqlpp | 3.2.3 + | | |
| Ruby | mysql2 | https://github.com/brianmario/mysql2 | 0.4.10 + | | |
| R | RMySQL | https://github.com/rstats-db/RMySQL | 0.10.16 + | | |
| Swift | MySQL – SWIFT | https://github.com/novi/mysql-swift | 0.7.2 + | | |
| Swift | Vapor/MySQL | https://github.com/vapor/mysql-kit | 2.0.1 + | | |

## <a name="management-tools"></a>Nástroje pro správu
Výhoda kompatibility se rozšiřuje i na nástroje pro správu databáze. Stávající nástroje by měly nadále fungovat s Azure Database for MySQL, pokud manipulace s databází funguje v rámci omezeného počtu uživatelských oprávnění. Existují tři běžné nástroje pro správu databáze, které byly testovány a shledány jako kompatibilní s Azure Database for MySQL 5,6 a 5,7 jsou uvedeny v následující tabulce:

|                                     | **MySQL Workbench 6. x a více** | **Navicat 12** | **PHPMyAdmin 4. x a více** |
| :---------------------------------- | :----------------------------- | :------------- | :-------------------------|
| **Vytvořit, aktualizovat, číst, zapsat, odstranit** | X | X | X |
| **Připojení SSL** | X | X | X |
| **Automatické dokončování dotazů SQL** | X | X |  |
| **Import a export dat** | X | X | X |
| **Exportovat do více formátů** | X | X | X |
| **Zálohování a obnovení** |  | × |  |
| **Zobrazit parametry serveru** | X | X | X |
| **Zobrazit klientská připojení** | X | X | X |

## <a name="next-steps"></a>Další kroky

- [Řešení potíží s připojením ke službě Azure Database for MySQL](howto-troubleshoot-common-connection-issues.md)