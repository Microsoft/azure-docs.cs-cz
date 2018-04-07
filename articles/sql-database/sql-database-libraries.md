---
title: Knihovny připojení pro databázi SQL. | Microsoft Docs
description: Obsahuje odkazy pro stahování modulů, které umožňují připojení k systému SQL Server a databáze SQL z mnoha rozličných programovacích jazyků klienta.
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: article
ms.date: 04/01/2018
ms.author: genemi
ms.openlocfilehash: a1e74e9fd2f1ed5eec27d6ede9ba51a7ba93263d
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="connectivity-libraries-and-frameworks-for-sql-server"></a>Připojení knihoven a architektur pro SQL Server

Podívejte se na naše [získat kurzy](http://aka.ms/sqldev) rychle začít s programovacích jazyků, například C#, Java, Node.js, PHP a Python. Potom sestavení aplikace pomocí systému SQL Server v systému Linux nebo Windows nebo Docker v systému macOS.

Následující tabulka uvádí připojení knihovny nebo *ovladače* používaný klientských aplikací z různých jazyků pro připojení k a používat SQL Server běžící na místě nebo v cloudu. Můžete je používat na systému Linux, Windows nebo Docker a použít je pro připojení k databázi SQL Azure a Azure SQL Data Warehouse. 

| Jazyk | Platforma | Další zdroje informací: | Ke stažení | Začínáme |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Microsoft ADO.NET pro SQL Server](https://docs.microsoft.com/sql/connect/ado-net/microsoft-ado-net-for-sql-server) | [Stáhnout](https://www.microsoft.com/net/download/) | [Začínáme](https://www.microsoft.com/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows, Linux, macOS | [Ovladač Microsoft JDBC pro systém SQL Server](http://msdn.microsoft.com/library/mt484311.aspx) | [Stáhnout](https://go.microsoft.com/fwlink/?linkid=852460) |  [Začínáme](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows, Linux, macOS| [Ovladač systému PHP SQL pro SQL Server](http://msdn.microsoft.com/library/dn865013.aspx) | Operační systém: <br/> \* [Windows](https://www.microsoft.com/download/details.aspx?id=55642) <br/> \* [Linux](https://github.com/Microsoft/msphpsql/tree/dev#install-unix) <br/> \* [macOS](https://github.com/Microsoft/msphpsql/tree/dev#install-unix) |  [Začínáme](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu)
| Node.js | Windows, Linux, macOS | [Node.js ovladač pro systém SQL Server](http://msdn.microsoft.com/library/mt652093.aspx) | [Instalace](https://msdn.microsoft.com/library/mt652094.aspx) |  [Začínáme](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu)
| Python | Windows, Linux, macOS | [Ovladač systému Python SQL](http://msdn.microsoft.com/library/mt652092.aspx) | Instalace volby: <br/> \* [pymssql](https://msdn.microsoft.com/library/mt694094.aspx) <br/> \* [pyodbc](http://msdn.microsoft.com/library/mt763257.aspx) |  [Začínáme](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows, Linux, macOS | [Poznámky Ruby ovladač pro systém SQL Server](http://msdn.microsoft.com/library/mt691981.aspx) | [Instalace](https://msdn.microsoft.com/library/mt711041.aspx) | [Začínáme](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows, Linux, macOS | [Ovladač Microsoft ODBC pro SQL Server](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) | [Stáhnout](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) |  

V následující tabulce jsou uvedeny příklady architektury relační objekt mapování (ORM) a webové rozhraní, které klientské aplikace můžete použít s místní aplikace SQL Server nebo v cloudu. Můžete použít rozhraní v systému Linux, Windows nebo Docker a použít je pro připojení k SQL Database a SQL Data Warehouse. 

| Jazyk | Platforma | ORM(s) |
| :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Entity Framework](https://docs.microsoft.com/ef)<br>[Základní Entity Framework](https://docs.microsoft.com/ef/core/index) |
| Java | Windows, Linux, macOS |[Hibernace ORM](http://hibernate.org/orm)|
| PHP | Windows, Linux | [Laravel (Eloquent)](https://laravel.com/docs/5.0/eloquent) |
| Node.js | Windows, Linux, macOS | [Sequelize ORM](http://docs.sequelizejs.com) |
| Python | Windows, Linux, macOS |[Django](https://www.djangoproject.com/) |
| Ruby | Windows, Linux, macOS | [Ruby, na které](http://rubyonrails.org/) |
||||

## <a name="related-links"></a>Související odkazy
- [Ovladače systému SQL Server](http://msdn.microsoft.com/library/mt654049.aspx) používané pro připojení z klientské aplikace
- Připojení k databázi SQL:
    - [Připojení k SQL Database s použitím rozhraní .NET (C#)](sql-database-connect-query-dotnet.md)
    - [Připojení k SQL Database s použitím jazyka PHP](sql-database-connect-query-php.md)
    - [Připojení k SQL Database s použitím prostředí Node.js](sql-database-connect-query-nodejs.md)
    - [Připojení k SQL Database s použitím jazyka Java](sql-database-connect-query-java.md)
    - [Připojení k SQL Database s použitím jazyka Python](sql-database-connect-query-python.md)
    - [Připojení k SQL Database s použitím prostředí Ruby](sql-database-connect-query-ruby.md)
- Příklady kódu pro logiku opakování:
    - [Pružným způsobem připojení k serveru SQL s ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
    - [Pružným způsobem připojení k serveru SQL s PHP][step-4-connect-resiliently-to-sql-with-php-p42h]


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php

