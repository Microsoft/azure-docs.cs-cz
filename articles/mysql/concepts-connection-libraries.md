---
title: Knihovny připojení – Azure Database for MySQL
description: V tomto článku jsou uvedeny všechny knihovny nebo ovladače, které mohou klientské programy použít při připojování k Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 5f83f937b8d9ec50ec7dc6ec781bff1f435eb45a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "79537189"
---
# <a name="connection-libraries-for-azure-database-for-mysql"></a>Knihovny připojení pro Azure Database for MySQL
V tomto článku jsou uvedeny všechny knihovny nebo ovladače, které mohou klientské programy použít při připojování k Azure Database for MySQL.

## <a name="client-interfaces"></a>Klientská rozhraní
MySQL nabízí standardní připojení ovladače databáze pro použití MySQL s aplikacemi a nástroji, které jsou kompatibilní s oborovými standardy ODBC a JDBC. Pro všechny systémy, které pracují s rozhraním ODBC nebo JDBC, může použít MySQL.

| **Jazyk** | **Platforma** | **Další prostředek** | **Stáhnout** |
| :----------- | :------------| :-----------------------| :------------|
| PHP | Windows, Linux | [Nativní ovladač MySQL pro PHP – mysqlnd](https://dev.mysql.com/downloads/connector/php-mysqlnd/) | [Stáhnout](https://secure.php.net/downloads.php) |
| ODBC | Platformy Windows, Linux, Mac OS X a UNIX | [Konektor MySQL/příručka pro vývojáře rozhraní ODBC](https://dev.mysql.com/doc/connector-odbc/en/) | [Stáhnout](https://dev.mysql.com/downloads/connector/odbc/) |
| ADO.NET | Windows | [Konektor MySQL/příručka pro vývojáře v síti](https://dev.mysql.com/doc/connector-net/en/) | [Stáhnout](https://dev.mysql.com/downloads/connector/net/) |
| JDBC | Nezávislá platforma | [Příručka pro vývojáře MySQL Connector/J 5,1](https://dev.mysql.com/doc/connector-j/5.1/en/) | [Stáhnout](https://dev.mysql.com/downloads/connector/j/) |
| Node.js | Windows, Linux, Mac OS X | [sidorares/Node – mysql2](https://github.com/sidorares/node-mysql2/tree/master/documentation) | [Stáhnout](https://github.com/sidorares/node-mysql2) |
| Python | Windows, Linux, Mac OS X | [Konektor MySQL/příručka pro vývojáře v Pythonu](https://dev.mysql.com/doc/connector-python/en/) | [Stáhnout](https://dev.mysql.com/downloads/connector/python/) |
| C++ | Windows, Linux, Mac OS X | [Konektor MySQL/příručka pro vývojáře C++](https://dev.mysql.com/doc/connector-cpp/en/) | [Stáhnout](https://dev.mysql.com/downloads/connector/python/) |
| C | Windows, Linux, Mac OS X | [Konektor MySQL/příručka pro vývojáře v jazyce C](https://dev.mysql.com/doc/refman/8.0/en/c-api.html) | [Stáhnout](https://dev.mysql.com/downloads/connector/c/)
| Perl | Platformy Windows, Linux, Mac OS X a UNIX | [DBD:: MySQL](https://metacpan.org/pod/DBD::mysql) | [Stáhnout](https://metacpan.org/pod/DBD::mysql) |


## <a name="next-steps"></a>Další kroky
Přečtěte si tyto rychlé starty, jak se připojit k a dotazovat Azure Database for MySQL pomocí vašeho jazyka podle vlastního výběru:

[Php](./connect-php.md)  |  [Jazyk Java](./connect-java.md)  |   [.NET (C#)](./connect-csharp.md)  |  [Python](./connect-python.md)  |  [Node.JS](./connect-nodejs.md)  |  [Ruby](./connect-ruby.md)  |  [Jazyk C++](connect-cpp.md)  |  [Přejít](./connect-go.md)

