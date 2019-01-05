---
title: Připojení knihoven pro službu SQL Database | Dokumentace Microsoftu
description: Obsahuje odkazy pro stahování modulů, které umožňují připojení k serveru SQL Server a SQL Database z množství různých programovacích jazyků klienta.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: ''
manager: craigg
ms.date: 01/03/2019
ms.openlocfilehash: 29a6c7107a57babb5dc81fa22146897d3958f0f0
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54036337"
---
# <a name="connectivity-libraries-and-frameworks-for-sql-server"></a>Připojení knihoven a architektur pro SQL Server

Podívejte se na naše [získat tutoriály Začínáme](https://aka.ms/sqldev) rychle začít s programovacích jazyků, jako je C#, Java, Node.js, PHP a Pythonu. Začnete vytvářejte aplikace s použitím SQL serveru v Linuxu nebo Windows nebo Dockeru v systému macOS.

V následující tabulce jsou uvedeny knihovny pro připojení nebo *ovladače* , klientské aplikace můžou použít z celé řady jazyků k připojení a použití SQL serveru spuštěné místně nebo v cloudu. Můžete je použít v systému Linux, Windows nebo Docker a použít pro připojení k Azure SQL Database a Azure SQL Data Warehouse. 

| Jazyk | Platforma | Další materiály | Ke stažení | Začínáme |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Microsoft ADO.NET pro SQL Server](https://docs.microsoft.com/sql/connect/ado-net/microsoft-ado-net-for-sql-server) | [Stáhnout](https://www.microsoft.com/net/download/) | [Začínáme](https://www.microsoft.com/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows, Linux, macOS | [Ovladač Microsoft JDBC pro SQL Server](https://msdn.microsoft.com/library/mt484311.aspx) | [Stáhnout](https://go.microsoft.com/fwlink/?linkid=852460) |  [Začínáme](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows, Linux, macOS| [Ovladač PHP SQL pro SQL Server](https://docs.microsoft.com/sql/connect/php/microsoft-php-driver-for-sql-server) | [Stáhnout](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server) | [Začínáme](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu/)
| Node.js | Windows, Linux, macOS | [Ovladač Node.js pro SQL Server](https://msdn.microsoft.com/library/mt652093.aspx) | [Instalace](https://msdn.microsoft.com/library/mt652094.aspx) |  [Začínáme](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu)
| Python | Windows, Linux, macOS | [Ovladače Python SQL](https://msdn.microsoft.com/library/mt652092.aspx) | Možnosti instalace: <br/> \* [pymssql](https://msdn.microsoft.com/library/mt694094.aspx) <br/> \* [modul pyodbc](https://msdn.microsoft.com/library/mt763257.aspx) |  [Začínáme](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows, Linux, macOS | [Ovladače Ruby pro SQL Server](https://msdn.microsoft.com/library/mt691981.aspx) | [Instalace](https://msdn.microsoft.com/library/mt711041.aspx) | [Začínáme](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows, Linux, macOS | [Ovladač Microsoft ODBC pro SQL Server](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) | [Stáhnout](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) |  

V následující tabulce jsou uvedeny příklady architektur objektově relační mapování (ORM) a webová rozhraní, které klientské aplikace můžou použít s využitím SQL serveru spuštěné místně nebo v cloudu. Můžete použít rozhraní v systému Linux, Windows nebo Dockeru a použít pro připojení k SQL Database a SQL Data Warehouse. 

| Jazyk | Platforma | ORM(s) |
| :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Entity Framework](https://docs.microsoft.com/ef)<br>[Entity Framework Core](https://docs.microsoft.com/ef/core/index) |
| Java | Windows, Linux, macOS |[Do režimu hibernace ORM](http://hibernate.org/orm)|
| PHP | Windows, Linux, macOS | [Laravel (Eloquent)](https://laravel.com/docs/5.0/eloquent) |
| Node.js | Windows, Linux, macOS | [Sequelize ORM](http://docs.sequelizejs.com) |
| Python | Windows, Linux, macOS |[Django](https://www.djangoproject.com/) |
| Ruby | Windows, Linux, macOS | [Aplikace Ruby on Rails](http://rubyonrails.org/) |
||||

## <a name="related-links"></a>Související odkazy
- [Ovladače SQL serveru](https://msdn.microsoft.com/library/mt654049.aspx) , který slouží k připojení z klientské aplikace
- Připojení k SQL Database:
    - [Připojení k SQL Database s použitím rozhraní .NET (C#)](sql-database-connect-query-dotnet.md)
    - [Připojení k SQL Database s použitím jazyka PHP](sql-database-connect-query-php.md)
    - [Připojení k SQL Database s použitím prostředí Node.js](sql-database-connect-query-nodejs.md)
    - [Připojení k SQL Database s použitím jazyka Java](sql-database-connect-query-java.md)
    - [Připojení k SQL Database s použitím jazyka Python](sql-database-connect-query-python.md)
    - [Připojení k SQL Database s použitím prostředí Ruby](sql-database-connect-query-ruby.md)
- Příklady kódu logiku pro opakování:
    - [Odolné připojení k SQL pomocí ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
    - [Odolné připojení k SQL pomocí PHP][step-4-connect-resiliently-to-sql-with-php-p42h]


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php

