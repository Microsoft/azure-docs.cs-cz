---
title: Kompatibilita ovladačů a nástrojů – Azure Database for MariaDB
description: Tento článek popisuje ovladače a nástroje pro správu MariaDB, které jsou kompatibilní s Azure Database for MariaDB.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 49fd2c8da3ba0a44f4901bc6c7a2241e9e20df4d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98659865"
---
# <a name="mariadb-drivers-and-management-tools-compatible-with-azure-database-for-mariadb"></a>Ovladače a nástroje pro správu MariaDB kompatibilní s Azure Database for MariaDB

Tento článek popisuje ovladače a nástroje pro správu, které jsou kompatibilní s Azure Database for MariaDB.

## <a name="mariadb-drivers"></a>Ovladače MariaDB

Azure Database for MariaDB používá komunitní edici MariaDB serveru. Proto je kompatibilní s širokou škálou programovacích jazyků a ovladačů. Rozhraní API a protokol MariaDB jsou kompatibilní s nástroji, které používá MySQL. To znamená, že konektory, které pracují s MySQL, by měly spolupracovat i s MariaDB.

Cílem je podpořit tři nejaktuálnější verze ovladačů MariaDB a snaha s autory z open source komunity, aby neustále vylepšila funkčnost a použitelnost ovladačů MariaDB. V následující tabulce je uveden seznam ovladačů, které byly testovány a zjištěny jako kompatibilní s Azure Database for MariaDB 10,2:

**Ovladač** | **Odkazy** | **Kompatibilní verze** | **Nekompatibilní verze** | **Poznámky**
---|---|---|---|---
PHP | https://secure.php.net/downloads.php | 5,5, 5,6, 7. x | 5.3 | V případě připojení PHP 7,0 k protokolu SSL MySQL přidejte MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT do připojovacího řetězce. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> Množina CHOP: ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` možnost na false.
.NET | [MySqlConnector na GitHubu](https://github.com/mysql-net/MySqlConnector) <br> [Instalační balíček z NuGet](https://www.nuget.org/packages/MySqlConnector/) | 0,27 a za | 0.26.5 a před |
Konektor MySQL/NET | [Konektor MySQL/NET](https://github.com/mysql/mysql-connector-net) | 8,0, 7,0, 6,10 |  | Chyba kódování může způsobit selhání připojení u některých systémů Windows bez kódování UTF8.
Node.js |  [MySQLjs na GitHubu](https://github.com/mysqljs/mysql/) <br> Instalační balíček z NPM:<br> Spustit `npm install mysql` z npm | 2.15 | 2.14.1 a před
GO | https://github.com/go-sql-driver/mysql/releases | 1,3, 1,4 | 1,2 a před | Použijte `allowNativePasswords=true` v připojovacím řetězci pro verzi 1,3. Verze 1,4 obsahuje opravu a `allowNativePasswords=true` již není nutná.
Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2,0, 2,1, 2,2 | 1.2.2 a před |
Java | https://downloads.mariadb.org/connector-java/ | 2,1, 2,0, 1,6 | 1.5.5 a před |

## <a name="management-tools"></a>Nástroje pro správu

Výhoda kompatibility se rozšiřuje i na nástroje pro správu databáze. Stávající nástroje by měly nadále fungovat s Azure Database for MariaDB, pokud manipulace s databází funguje v rámci omezeného počtu uživatelských oprávnění. Tři běžné nástroje pro správu databáze, které byly testovány a shledány jako kompatibilní s Azure Database for MariaDB 10,2, jsou uvedeny v následující tabulce:

| Akce | **MySQL Workbench 6. x a více** | **Navicat 12** | **PHPMyAdmin 4. x a více**
---|---|---|---
Vytvořit, aktualizovat, číst, zapsat, odstranit | × | × | ×
Připojení SSL | × | × | ×
Automatické dokončování dotazů SQL | × | × |
Import a export dat | × | × | ×
Exportovat do více formátů | × | × | ×
Zálohování a obnovení |  | × |
Zobrazit parametry serveru | × | × | ×
Zobrazit klientská připojení | × | × | ×

## <a name="next-steps"></a>Další kroky

- [Řešení potíží s připojením ke službě Azure Database for MariaDB](howto-troubleshoot-common-connection-issues.md)