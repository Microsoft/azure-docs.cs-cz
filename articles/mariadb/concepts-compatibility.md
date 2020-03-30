---
title: Kompatibilita ovladačů a nástrojů – Azure Database for MariaDB
description: Tento článek popisuje ovladače MariaDB a nástroje pro správu, které jsou kompatibilní s Azure Database for MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: a0cee198f028fd90e04dac15e98d7cd33aee9201
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79532344"
---
# <a name="mariadb-drivers-and-management-tools-compatible-with-azure-database-for-mariadb"></a>Ovladače a nástroje pro správu MariaDB kompatibilní s databází Azure pro MariaDB

Tento článek popisuje ovladače a nástroje pro správu, které jsou kompatibilní s Azure Database pro MariaDB.

## <a name="mariadb-drivers"></a>MariaDB Ovladače

Azure Database for MariaDB používá komunitní edici serveru MariaDB. Proto je kompatibilní s širokou škálou programovacích jazyků a ovladačů. MariaDB API a protokol jsou kompatibilní s těmi, které používá MySQL. To znamená, že konektory, které pracují s MySQL by měl také pracovat s MariaDB.

Cílem je podporovat tři nejnovější verze ovladačů MariaDB a úsilí s autory z open source komunity neustále zlepšovat funkčnost a použitelnost ovladačů MariaDB pokračovat. Seznam ovladačů, které byly testovány a zjistil, že je kompatibilní s Databází Azure pro MariaDB 10.2 je k dispozici v následující tabulce:

**Ovladač** | **Odkazy** | **Kompatibilní verze** | **Nekompatibilní verze** | **Poznámky**
---|---|---|---|---
PHP | https://secure.php.net/downloads.php | 5,5, 5,6, 7,x | 5.3 | Pro připojení PHP 7.0 s SSL MySQLi přidejte MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT do připojovacího řetězce. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> PDO set: ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` možnost false.
.NET | [MySqlConnector na GitHubu](https://github.com/mysql-net/MySqlConnector) <br> [Instalační balíček od Nuget](https://www.nuget.org/packages/MySqlConnector/) | 0,27 a po | 0.26.5 a před |
MySQL konektor/NET | [MySQL konektor/NET](https://github.com/mysql/mysql-connector-net) | 8.0, 7.0, 6.10 |  | Chyba kódování může způsobit selhání připojení v některých systémech Windows bez UTF8.
Node.js |  [MySQLjs na GitHubu](https://github.com/mysqljs/mysql/) <br> Instalační balíček od NPM:<br> Spustit `npm install mysql` z NPM | 2.15 | 2.14.1 a před
GO | https://github.com/go-sql-driver/mysql/releases | 1.3, 1.4 | 1.2 a před | Použití `allowNativePasswords=true` v připojovacím řetězci pro verzi 1.3. Verze 1.4 obsahuje `allowNativePasswords=true` opravu a již není vyžadována.
Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2.0, 2.1, 2.2 | 1.2.2 a před |
Java | https://downloads.mariadb.org/connector-java/ | 2.1, 2.0, 1.6 | 1.5.5 a před |

## <a name="management-tools"></a>Nástroje pro správu

Výhoda kompatibility se vztahuje i na nástroje pro správu databází. Vaše stávající nástroje by měly pokračovat v práci s Azure Database pro MariaDB, tak dlouho, dokud manipulace s databází pracuje v rámci omezení uživatelských oprávnění. Tři běžné nástroje pro správu databází, které byly testovány a shledány kompatibilními s Databází Azure pro MariaDB 10.2, jsou uvedeny v následující tabulce:

| | **MySQL Workbench 6.x a nahoru** | **Navicat 12** | **PHPMyAdmin 4.x a nahoru**
---|---|---|---
Vytvořit, aktualizovat, číst, psát, odstraňovat | × | × | ×
Připojení SSL | × | × | ×
Automatické dokončování dotazu SQL | × | × |
Import a export dat | × | × | ×
Export do více formátů | × | × | ×
Zálohování a obnovení |  | × |
Parametry zobrazovacího serveru | × | × | ×
Zobrazit připojení klientů | × | × | ×

## <a name="next-steps"></a>Další kroky

- [Řešení potíží s připojením ke službě Azure Database for MariaDB](howto-troubleshoot-common-connection-issues.md)