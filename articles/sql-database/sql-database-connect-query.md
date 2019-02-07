---
title: Připojení ke službě Azure SQL Database a její dotazování – Rychlé starty | Microsoft Docs
description: Rychlé starty pro službu Azure SQL Database, které ukazují připojení k databázi SQL Azure a její dotazování.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 12/18/2018
ms.openlocfilehash: c569b7ba6c49a560b4a6a1ecb10a6a61378a1228
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55814874"
---
# <a name="quickstarts-azure-sql-database-connect-and-query"></a>Šablony rychlý start: Azure SQL Database připojení a dotazování

Následující dokument obsahuje odkazy na příklady Azure, které ukazují připojení k databázi SQL Azure a její dotazování. Obsahuje také několik doporučení týkající se protokolu TLS (Transport Level Security).

## <a name="quickstarts"></a>Šablony Rychlý start

| |  |
|---|---|
|[SQL Server Management Studio](sql-database-connect-query-ssms.md)|Tento rychlý start ukazuje použití SSMS k připojení k databázi SQL Azure a následné použití příkazů jazyka Transact-SQL k dotazování, vkládání, aktualizaci a odstraňování dat v databázi.|
|[Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)|Tento rychlý start ukazuje použití sady Azure Data Studio pro připojení k databázi SQL Azure a následné použití příkazů jazyka Transact-SQL (T-SQL) k vytvoření databáze TutorialDB používané v kurzech pro Azure Data Studio.|
|[Azure Portal](sql-database-connect-query-portal.md)|Tento rychlý start ukazuje použití editoru dotazů pro připojení k databázi SQL a následné použití příkazů jazyka Transact-SQL k dotazování, vkládání, aktualizaci a odstraňování dat v databázi.|
|[Visual Studio Code](sql-database-connect-query-vscode.md)|Tento rychlý start ukazuje použití Visual Studio Code pro připojení k databázi SQL Azure a následné použití příkazů jazyka Transact-SQL k dotazování, vkládání, aktualizaci a odstraňování dat v databázi.|
|[.NET se sadou Visual Studio](sql-database-connect-query-dotnet-visual-studio.md)|Tento rychlý start ukazuje použití rozhraní .NET Framework a sady Visual Studio k vytvoření programu v jazyce C# pro připojení k databázi SQL Azure a použití příkazů jazyka Transact-SQL k dotazování dat.|
|[.NET Core](sql-database-connect-query-dotnet-core.md)|Tento rychlý start ukazuje použití .NET Core v systému Windows, Linux nebo macOS k vytvoření programu v jazyce C# pro připojení k databázi SQL Azure a použití příkazů jazyka Transact-SQL k dotazování dat.|
|[Go](sql-database-connect-query-go.md)|Tento rychlý start ukazuje použití jazyka Go pro připojení k databázi SQL Azure. Ukazuje se také použití příkazů jazyka Transact-SQL k dotazování a úpravě dat.|
|[Java](sql-database-connect-query-java.md)|Tento rychlý start ukazuje použití Javy pro připojení k databázi SQL Azure a následné použití příkazů jazyka Transact-SQL k dotazování dat.|
|[Node.js](sql-database-connect-query-nodejs.md)|Tento rychlý start ukazuje použití Node.js k vytvoření programu pro připojení k databázi SQL Azure a použití příkazů jazyka Transact-SQL k dotazování dat.|
|[PHP](sql-database-connect-query-php.md)|Tento rychlý start ukazuje použití PHP k vytvoření programu pro připojení k databázi SQL Azure a použití příkazů jazyka Transact-SQL k dotazování dat.|
|[Python](sql-database-connect-query-python.md)|Tento rychlý start ukazuje použití Pythonu pro připojení k databázi SQL Azure a použití příkazů jazyka Transact-SQL k dotazování dat. |
|[Ruby](sql-database-connect-query-ruby.md)|Tento rychlý start ukazuje použití Ruby k vytvoření programu pro připojení k databázi SQL Azure a použití příkazů jazyka Transact-SQL k dotazování dat.|
|||

## <a name="tls-considerations-for-sql-database-connectivity"></a>Důležité informace o použití protokolu TLS pro připojení k databázi SQL
Protokol TLS (Transport Layer Security) používají všechny ovladače, které Microsoft používá nebo podporuje pro připojení k databázi SQL Azure. Není potřeba žádná zvláštní konfigurace. Pro všechna připojení k serveru SQL Server nebo k databázi SQL Azure doporučujeme nastavit u všech aplikací následující konfigurace nebo jejich ekvivalenty:

 - **Encrypt = On**
 - **TrustServerCertificate = Off**

Některé systémy pro uvedená klíčová slova konfigurací používají rozdílná, ale ekvivalentní klíčová slova. Tyto konfigurace zajistí, že ovladač klienta ověří identitu certifikátu TLS přijatého ze serveru.

Také doporučujeme zakázat v klientovi protokoly TLS 1.1 a 1.0, pokud potřebujete dodržovat standard PCI–DSS (Payment Card Industry – Data Security Standard).

Ovladače od jiných výrobců nemusí ve výchozím nastavení protokol TLS používat. To může při připojování k databázi SQL Azure hrát důležitou roli. Aplikace s integrovanými ovladači vám nemusí umožnit řídit tato nastavení připojení. Doporučujeme, abyste si ověřili zabezpečení takovýchto ovladačů a aplikací, dříve než je použijete v systémech, které pracují s důvěrnými osobními údaji.

## <a name="libraries"></a>Knihovny

Pro připojení ke službě Azure SQL Database můžete použít různé knihoven a architektur. Podívejte se na naše [získat tutoriály Začínáme](https://aka.ms/sqldev) rychle začít s programovacích jazyků, jako je C#, Java, Node.js, PHP a Pythonu. Začnete vytvářejte aplikace s použitím SQL serveru v Linuxu nebo Windows nebo Dockeru v systému macOS.

V následující tabulce jsou uvedeny knihovny pro připojení nebo *ovladače* , klientské aplikace můžou použít z celé řady jazyků k připojení a použití SQL serveru spuštěné místně nebo v cloudu. Můžete je použít v systému Linux, Windows nebo Docker a použít pro připojení k Azure SQL Database a Azure SQL Data Warehouse. 

| Jazyk | Platforma | Další materiály | Ke stažení | Začínáme |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Microsoft ADO.NET for SQL Server](https://docs.microsoft.com/sql/connect/ado-net/microsoft-ado-net-for-sql-server) | [Stáhnout](https://www.microsoft.com/net/download/) | [Začínáme](https://www.microsoft.com/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows, Linux, macOS | [Ovladač Microsoft JDBC pro SQL Server](https://msdn.microsoft.com/library/mt484311.aspx) | [Stáhnout](https://go.microsoft.com/fwlink/?linkid=852460) |  [Začínáme](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows, Linux, macOS| [Ovladač PHP SQL pro SQL Server](https://docs.microsoft.com/sql/connect/php/microsoft-php-driver-for-sql-server) | [Stáhnout](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server) | [Začínáme](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu/)
| Node.js | Windows, Linux, macOS | [Ovladač Node.js pro SQL Server](https://msdn.microsoft.com/library/mt652093.aspx) | [Instalace](https://msdn.microsoft.com/library/mt652094.aspx) |  [Začínáme](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu)
| Python | Windows, Linux, macOS | [Python SQL driver](https://msdn.microsoft.com/library/mt652092.aspx) | Možnosti instalace: <br/> \* [pymssql](https://msdn.microsoft.com/library/mt694094.aspx) <br/> \* [pyodbc](https://msdn.microsoft.com/library/mt763257.aspx) |  [Začínáme](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows, Linux, macOS | [Ovladače Ruby pro SQL Server](https://msdn.microsoft.com/library/mt691981.aspx) | [Instalace](https://msdn.microsoft.com/library/mt711041.aspx) | [Začínáme](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows, Linux, macOS | [Ovladač Microsoft ODBC pro SQL Server](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) | [Stáhnout](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) |  

V následující tabulce jsou uvedeny příklady architektur objektově relační mapování (ORM) a webová rozhraní, které klientské aplikace můžou použít s využitím SQL serveru spuštěné místně nebo v cloudu. Můžete použít rozhraní v systému Linux, Windows nebo Dockeru a použít pro připojení k SQL Database a SQL Data Warehouse. 

| Jazyk | Platforma | ORM(s) |
| :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Entity Framework](https://docs.microsoft.com/ef)<br>[Entity Framework Core](https://docs.microsoft.com/ef/core/index) |
| Java | Windows, Linux, macOS |[Do režimu hibernace ORM](https://hibernate.org/orm)|
| PHP | Windows, Linux, macOS | [Laravel (Eloquent)](https://laravel.com/docs/5.0/eloquent) |
| Node.js | Windows, Linux, macOS | [Sequelize ORM](https://docs.sequelizejs.com) |
| Python | Windows, Linux, macOS |[Django](https://www.djangoproject.com/) |
| Ruby | Windows, Linux, macOS | [Aplikace Ruby on Rails](https://rubyonrails.org/) |
||||

## <a name="next-steps"></a>Další postup

- Informace o připojení k architektuře najdete v článku o [architektuře připojení k databázi SQL Azure](sql-database-connectivity-architecture.md).
- Najít [ovladače systému SQL Server](https://msdn.microsoft.com/library/mt654049.aspx) , který slouží k připojení z klientské aplikace
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