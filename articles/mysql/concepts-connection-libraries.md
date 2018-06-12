---
title: Knihovny připojení pro databázi Azure pro databázi MySQL
description: Tento článek obsahuje seznam všech knihovny a ovladače, klienta programy můžete používat při připojování k databázi Azure pro databázi MySQL.
services: mysql
author: mswutao
ms.author: wutao
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: c826bf1cf17230563b608e764c443b6166f13924
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35263999"
---
# <a name="connection-libraries-for-azure-database-for-mysql"></a>Knihovny připojení pro databázi Azure pro databázi MySQL
Tento článek obsahuje seznam všech knihovny a ovladače, klienta programy můžete používat při připojování k databázi Azure pro databázi MySQL.

## <a name="client-interfaces"></a>Rozhraní klienta
MySQL nabízí připojení standardní databáze ovladačů pro použití s aplikacemi a nástroje, které jsou kompatibilní se standardy rozhraní ODBC a JDBC MySQL. Všechny systémy, které pracuje s ODBC nebo JDBC můžete použít MySQL.

| **Jazyk** | **Platforma** | **Další zdroje** | **Stáhnout** |
| :----------- | :------------| :-----------------------| :------------|
| PHP | Windows, Linux | [Nativní ovladače MySQL pro jazyk PHP - mysqlnd](https://dev.mysql.com/downloads/connector/php-mysqlnd/) | [Stáhnout](http://php.net/downloads.php) |
| ODBC | Platformy systému Windows, Mac OS X, Linux a Unix | [Příručka vývojáře MySQL konektoru nebo ODBC](https://dev.mysql.com/doc/connector-odbc/en/) | [Stáhnout](https://dev.mysql.com/downloads/connector/odbc/) |
| ADO.NET | Windows | [Příručka vývojáře MySQL Connector/Net](https://dev.mysql.com/doc/connector-net/en/) | [Stáhnout](https://dev.mysql.com/downloads/connector/net/) |
| JDBC | Nezávislé na platformě | [Příručka vývojáře konektor/J MySQL 5.1](https://dev.mysql.com/doc/connector-j/5.1/en/) | [Stáhnout](https://dev.mysql.com/downloads/connector/j/) |
| Node.js | Windows, Linux, Mac OS X | [sidorares/node-mysql2](https://github.com/sidorares/node-mysql2/tree/master/documentation) | [Stáhnout](https://github.com/sidorares/node-mysql2) |
| Python | Windows, Linux, Mac OS X | [Příručka vývojáře konektoru nebo Python MySQL](https://dev.mysql.com/doc/connector-python/en/) | [Stáhnout](https://dev.mysql.com/downloads/connector/python/) |
| C++ | Windows, Linux, Mac OS X | [Příručka vývojáře MySQL konektor/C++](https://dev.mysql.com/doc/connector-cpp/en/) | [Stáhnout](https://dev.mysql.com/downloads/connector/python/) |
| C | Windows, Linux, Mac OS X | [Příručka vývojáře MySQL konektor/C](https://dev.mysql.com/doc/connector-c/en/) | [Stáhnout](https://dev.mysql.com/downloads/connector/c/)
| Perl | Platformy systému Windows, Mac OS X, Linux a Unix | [DBD::MySQL](https://metacpan.org/pod/DBD::mysql) | [Stáhnout](https://metacpan.org/pod/DBD::mysql) |


## <a name="next-steps"></a>Další postup
Přečtěte si tyto elementy QuickStart o tom, jak připojit k a dotazovat Azure Database pro databázi MySQL pomocí vámi zvolený jazyk:

[PHP](./connect-php.md) | [Java](./connect-java.md) |  [.NET (C#)](./connect-csharp.md) | [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md)  |  [Ruby](./connect-ruby.md) | [C++](connect-cpp.md) | [přejděte](./connect-go.md)

