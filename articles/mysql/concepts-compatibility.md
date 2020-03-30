---
title: Kompatibilita ovladačů a nástrojů – Azure Database for MySQL
description: Tento článek popisuje ovladače MySQL a nástroje pro správu, které jsou kompatibilní s Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: e8917a0a5678c4c6b72352a0d4c1523bfea3c96d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537206"
---
# <a name="mysql-drivers-and-management-tools-compatible-with-azure-database-for-mysql"></a>Ovladače MySQL a nástroje pro správu kompatibilní s Databází Azure pro MySQL
Tento článek popisuje ovladače a nástroje pro správu, které jsou kompatibilní s Azure Database for MySQL.

## <a name="mysql-drivers"></a>MySQL ovladače
Azure Database for MySQL využívá nejoblíbenější komunitní edici databáze MySQL na světě. Proto je kompatibilní s širokou škálou programovacích jazyků a ovladačů. Cílem je podporovat tři nejnovější verze mysql ovladače, a úsilí s autory z open source komunity neustále zlepšovat funkčnost a použitelnost ovladačů MySQL pokračovat. Seznam ovladačů, které byly testovány a zjistil, že je kompatibilní s Databází Azure pro MySQL 5.6 a 5.7 je k dispozici v následující tabulce:

| **Programovací jazyk** | **Ovladač** | **Odkazy** | **Kompatibilní verze** | **Nekompatibilní verze** | **Poznámky** |
| :----------------------- | :--------- | :-------- | :---------------------- | :------------------------ | :-------- |
| PHP | mysqli, pdo_mysql, mysqlnd | https://secure.php.net/downloads.php | 5,5, 5,6, 7,x | 5.3 | Pro připojení PHP 7.0 s SSL MySQLi přidejte MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT do připojovacího řetězce. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> PDO set: ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` možnost false.|
| .NET | Asynchronní konektor MySQL pro rozhraní .NET | https://github.com/mysql-net/MySqlConnector <br> [Instalační balíček od Nuget](https://www.nuget.org/packages/MySqlConnector/) | 0,27 a po | 0.26.5 a před | |
| .NET | MySQL konektor/NET | https://github.com/mysql/mysql-connector-net | 6.6.3 ,7.0 ,8.0 |  | Chyba kódování může způsobit selhání připojení v některých systémech Windows bez UTF8. |
| Node.js | mysqljs | https://github.com/mysqljs/mysql/ <br> Instalační balíček od NPM:<br> Spustit `npm install mysql` z NPM | 2.15 | 2.14.1 a před | |
| Node.js | uzel mysql2 | https://github.com/sidorares/node-mysql2 | 1.3.4+ | | |
| Přejít | Přejít MySQL ovladač | https://github.com/go-sql-driver/mysql/releases | 1.3, 1.4 | 1.2 a před | Použití `allowNativePasswords=true` v připojovacím řetězci pro verzi 1.3. Verze 1.4 obsahuje `allowNativePasswords=true` opravu a již není vyžadována. |
| Python | MySQL konektor/Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2.0, 2.1, 2.2, použití 8.0.16+ s MySQL 8.0  | 1.2.2 a před | |
| Python | PyMySQL | https://pypi.org/project/PyMySQL/ | 0.7.11, 0.8.0, 0.8.1, 0.9.3+ | 0.9.0 - 0.9.2 (regrese v web2py) | |
| Java | Konektor MariaDB/J | https://downloads.mariadb.org/connector-java/ | 2.1, 2.0, 1.6 | 1.5.5 a před | | 
| Java | MySQL konektor/J | https://github.com/mysql/mysql-connector-j | 5.1.21+, použijte 8.0.17+ s MySQL 8.0 | 5.1.20 a níže | |
| C | MySQL Konektor/C (libmysqlclient) | https://dev.mysql.com/doc/refman/5.7/en/c-api-implementations.html | 6.0.2+ | | |
| C | MySQL Konektor/ODBC (myodbc) | https://github.com/mysql/mysql-connector-odbc | 3.51.29+ | | |
| C++ | MySQL konektor/C++ | https://github.com/mysql/mysql-connector-cpp | 1.1.9+ | 1.1.3 a níže | | 
| C++ | MySQL++| https://tangentsoft.net/mysql++ | 3.2.3+ | | |
| Ruby | mysql2 | https://github.com/brianmario/mysql2 | 0.4.10+ | | |
| R | RMySQL | https://github.com/rstats-db/RMySQL | 0.10.16+ | | |
| Swift | mysql-swift | https://github.com/novi/mysql-swift | 0.7.2+ | | |
| Swift | pára/mysql | https://github.com/vapor/mysql-kit | 2.0.1+ | | |

## <a name="management-tools"></a>Nástroje pro správu
Výhoda kompatibility se vztahuje i na nástroje pro správu databází. Vaše stávající nástroje by měly pokračovat v práci s Azure Database for MySQL, pokud manipulace s databází funguje v rámci omezení uživatelských oprávnění. Tři běžné nástroje pro správu databází, které byly testovány a shledány kompatibilními s Databází Azure pro MySQL 5.6 a 5.7, jsou uvedeny v následující tabulce:

|                                     | **MySQL Workbench 6.x a nahoru** | **Navicat 12** | **PHPMyAdmin 4.x a nahoru** |
| :---------------------------------- | :----------------------------- | :------------- | :-------------------------|
| Vytvořit, aktualizovat, číst, psát, odstraňovat | × | × | × |
| Připojení SSL | × | × | × |
| Automatické dokončování dotazu SQL | × | × |  |
| Import a export dat | × | × | × | 
| Export do více formátů | × | × | × |
| Zálohování a obnovení |  | × |  |
| Parametry zobrazovacího serveru | × | × | × |
| Zobrazit připojení klientů | × | × | × |

## <a name="next-steps"></a>Další kroky

- [Řešení potíží s připojením ke službě Azure Database for MySQL](howto-troubleshoot-common-connection-issues.md)