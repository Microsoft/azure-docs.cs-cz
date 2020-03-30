---
title: Knihovny připojení – Azure Database for MySQL
description: Tento článek uvádí každou knihovnu nebo ovladač, který mohou klientské programy použít při připojování k Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 5f83f937b8d9ec50ec7dc6ec781bff1f435eb45a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537189"
---
# <a name="connection-libraries-for-azure-database-for-mysql"></a>Knihovny připojení pro Azure Database for MySQL
Tento článek uvádí každou knihovnu nebo ovladač, který mohou klientské programy použít při připojování k Azure Database for MySQL.

## <a name="client-interfaces"></a>Klientská rozhraní
MySQL nabízí standardní připojení databázového ovladače pro použití MySQL s aplikacemi a nástroji, které jsou kompatibilní s průmyslovými standardy ODBC a JDBC. MySQL může používat jakýkoli systém, který pracuje s rozhraním ODBC nebo JDBC.

| **Jazyk** | **Platforma** | **Další zdroj** | **Stáhnout** |
| :----------- | :------------| :-----------------------| :------------|
| PHP | Windows, Linux | [MySQL nativní ovladač pro PHP - mysqlnd](https://dev.mysql.com/downloads/connector/php-mysqlnd/) | [Stáhnout](https://secure.php.net/downloads.php) |
| ODBC | Platformy Windows, Linux, Mac OS X a Unix | [MySQL Konektor /ODBC Průvodce pro vývojáře](https://dev.mysql.com/doc/connector-odbc/en/) | [Stáhnout](https://dev.mysql.com/downloads/connector/odbc/) |
| ADO.NET | Windows | [MySQL Konektor / Net Developer Guide](https://dev.mysql.com/doc/connector-net/en/) | [Stáhnout](https://dev.mysql.com/downloads/connector/net/) |
| JDBC | Nezávislé na platformě | [MySQL Connector/J 5.1 Průvodce pro vývojáře](https://dev.mysql.com/doc/connector-j/5.1/en/) | [Stáhnout](https://dev.mysql.com/downloads/connector/j/) |
| Node.js | Windows, Linux, Mac OS X | [sidorares/uzel-mysql2](https://github.com/sidorares/node-mysql2/tree/master/documentation) | [Stáhnout](https://github.com/sidorares/node-mysql2) |
| Python | Windows, Linux, Mac OS X | [MySQL Konektor / Python Developer Guide](https://dev.mysql.com/doc/connector-python/en/) | [Stáhnout](https://dev.mysql.com/downloads/connector/python/) |
| C++ | Windows, Linux, Mac OS X | [MySQL Connector/C++ Průvodce pro vývojáře](https://dev.mysql.com/doc/connector-cpp/en/) | [Stáhnout](https://dev.mysql.com/downloads/connector/python/) |
| C | Windows, Linux, Mac OS X | [MySQL Konektor / C Průvodce pro vývojáře](https://dev.mysql.com/doc/refman/8.0/en/c-api.html) | [Stáhnout](https://dev.mysql.com/downloads/connector/c/)
| Perl | Platformy Windows, Linux, Mac OS X a Unix | [DBD::MySQL](https://metacpan.org/pod/DBD::mysql) | [Stáhnout](https://metacpan.org/pod/DBD::mysql) |


## <a name="next-steps"></a>Další kroky
Přečtěte si tyto rychlé starty o tom, jak se připojit k Azure Database pro MySQL a dotazovat se na ně pomocí zvoleného jazyka:

[PHP](./connect-php.md) | [Java](./connect-java.md) |  [.NET (C#)](./connect-csharp.md) | [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Ruby](./connect-ruby.md) | [C++](connect-cpp.md) | [Go](./connect-go.md)

