---
title: Připojení knihoven pro službu Azure Database for MySQL
description: Tento článek uvádí každou knihovnu nebo ovladač, klientských programů můžete použít při připojování ke službě Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 6ce0f2c761ede7d326f52f4d93d7f1b0bfa98cb2
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53543052"
---
# <a name="connection-libraries-for-azure-database-for-mysql"></a>Připojení knihoven pro službu Azure Database for MySQL
Tento článek uvádí každou knihovnu nebo ovladač, klientských programů můžete použít při připojování ke službě Azure Database for MySQL.

## <a name="client-interfaces"></a>Klientská rozhraní
MySQL nabízí připojení k ovladači standardní databázi pro MySQL pomocí aplikace a nástroje, které jsou kompatibilní se standardy rozhraní ODBC a JDBC. MySQL můžete použít libovolný systém, který funguje s ODBC nebo JDBC.

| **Jazyk** | **Platforma** | **Další zdroje** | **Stáhnout** |
| :----------- | :------------| :-----------------------| :------------|
| PHP | Windows, Linux | [Nativní ovladače MySQL pro jazyk PHP - mysqlnd](https://dev.mysql.com/downloads/connector/php-mysqlnd/) | [Stáhnout](https://secure.php.net/downloads.php) |
| ODBC | Platformy Windows, Linux, Mac OS X a Unix | [Příručka pro vývojáře MySQL Connector/ODBC](https://dev.mysql.com/doc/connector-odbc/en/) | [Stáhnout](https://dev.mysql.com/downloads/connector/odbc/) |
| ADO.NET | Windows | [Příručka pro vývojáře MySQL Connector/Net](https://dev.mysql.com/doc/connector-net/en/) | [Stáhnout](https://dev.mysql.com/downloads/connector/net/) |
| JDBC | Nezávislé na platformě | [Příručka pro vývojáře MySQL Connector/J 5.1](https://dev.mysql.com/doc/connector-j/5.1/en/) | [Stáhnout](https://dev.mysql.com/downloads/connector/j/) |
| Node.js | Windows, Linux, Mac OS X | [sidorares/node-mysql2](https://github.com/sidorares/node-mysql2/tree/master/documentation) | [Stáhnout](https://github.com/sidorares/node-mysql2) |
| Python | Windows, Linux, Mac OS X | [Příručka pro vývojáře MySQL Connector/Python](https://dev.mysql.com/doc/connector-python/en/) | [Stáhnout](https://dev.mysql.com/downloads/connector/python/) |
| C++ | Windows, Linux, Mac OS X | [Příručka pro vývojáře MySQL Connector/C++](https://dev.mysql.com/doc/connector-cpp/en/) | [Stáhnout](https://dev.mysql.com/downloads/connector/python/) |
| C | Windows, Linux, Mac OS X | [Příručka pro vývojáře MySQL Connector/C](https://dev.mysql.com/doc/connector-c/en/) | [Stáhnout](https://dev.mysql.com/downloads/connector/c/)
| Perl | Platformy Windows, Linux, Mac OS X a Unix | [DBD::MySQL](https://metacpan.org/pod/DBD::mysql) | [Stáhnout](https://metacpan.org/pod/DBD::mysql) |


## <a name="next-steps"></a>Další postup
Přečtěte si v těchto rychlých startech o tom, jak připojení a dotazování – Azure Database for MySQL s použitím svůj jazyk:

[PHP](./connect-php.md) | [Java](./connect-java.md) |  [.NET (C#)](./connect-csharp.md) | [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md)  |  [Ruby](./connect-ruby.md) | [C++](connect-cpp.md) | [přejít](./connect-go.md)

