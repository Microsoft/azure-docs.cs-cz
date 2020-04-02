---
title: Rychlé starty připojení a dotazu
description: Rychlé starty pro službu Azure SQL Database, které ukazují připojení k databázi Azure SQL a její dotazování.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: 05ba54c10cc693f81115f364d7a400e2275a3875
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80528238"
---
# <a name="quickstarts-azure-sql-database-connect-and-query"></a>Rychlé starty: Připojení a dotaz azure SQL database

Následující dokument obsahuje odkazy na příklady Azure, které ukazují připojení k databázi Azure SQL a její dotazování. Obsahuje také několik doporučení týkající se protokolu TLS (Transport Level Security).

## <a name="quickstarts"></a>Rychlý start

| |  |
|---|---|
|[SQL Server Management Studio](sql-database-connect-query-ssms.md)|Tento rychlý start ukazuje použití SSMS k připojení k databázi Azure SQL a následné použití příkazů jazyka Transact-SQL k dotazování, vkládání, aktualizaci a odstraňování dat v databázi.|
|[Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)|Tento rychlý start ukazuje použití sady Azure Data Studio pro připojení k databázi Azure SQL a následné použití příkazů jazyka Transact-SQL (T-SQL) k vytvoření databáze TutorialDB používané v kurzech pro Azure Data Studio.|
|[portál Azure](sql-database-connect-query-portal.md)|Tento rychlý start ukazuje použití editoru dotazů pro připojení k databázi SQL a následné použití příkazů jazyka Transact-SQL k dotazování, vkládání, aktualizaci a odstraňování dat v databázi.|
|[Visual Studio Code](sql-database-connect-query-vscode.md)|Tento rychlý start ukazuje použití Visual Studio Code pro připojení k databázi Azure SQL a následné použití příkazů jazyka Transact-SQL k dotazování, vkládání, aktualizaci a odstraňování dat v databázi.|
|[.NET se sadou Visual Studio](sql-database-connect-query-dotnet-visual-studio.md)|Tento rychlý start ukazuje použití rozhraní .NET Framework a sady Visual Studio k vytvoření programu v jazyce C# pro připojení k databázi Azure SQL a použití příkazů jazyka Transact-SQL k dotazování dat.|
|[Jádro rozhraní .NET](sql-database-connect-query-dotnet-core.md)|Tento rychlý start ukazuje použití .NET Core v systému Windows, Linux nebo macOS k vytvoření programu v jazyce C# pro připojení k databázi Azure SQL a použití příkazů jazyka Transact-SQL k dotazování dat.|
|[Přejít](sql-database-connect-query-go.md)|Tento rychlý start ukazuje použití jazyka Go pro připojení k databázi Azure SQL. Ukazuje se také použití příkazů jazyka Transact-SQL k dotazování a úpravě dat.|
|[Java](sql-database-connect-query-java.md)|Tento rychlý start ukazuje použití Javy pro připojení k databázi Azure SQL a následné použití příkazů jazyka Transact-SQL k dotazování dat.|
|[Node.js](sql-database-connect-query-nodejs.md)|Tento rychlý start ukazuje použití Node.js k vytvoření programu pro připojení k databázi Azure SQL a použití příkazů jazyka Transact-SQL k dotazování dat.|
|[PHP](sql-database-connect-query-php.md)|Tento rychlý start ukazuje použití PHP k vytvoření programu pro připojení k databázi Azure SQL a použití příkazů jazyka Transact-SQL k dotazování dat.|
|[Python](sql-database-connect-query-python.md)|Tento rychlý start ukazuje použití Pythonu pro připojení k databázi Azure SQL a použití příkazů jazyka Transact-SQL k dotazování dat. |
|[Ruby](sql-database-connect-query-ruby.md)|Tento rychlý start ukazuje použití Ruby k vytvoření programu pro připojení k databázi Azure SQL a použití příkazů jazyka Transact-SQL k dotazování dat.|
|[R](sql-database-connect-query-r.md)|Tento rychlý start ukazuje, jak používat R s Azure SQL Database Machine Learning Services k vytvoření programu pro připojení k databázi Azure SQL a použití příkazů Transact-SQL k dotazování dat.|
|||

## <a name="tls-considerations-for-sql-database-connectivity"></a>Důležité informace o použití protokolu TLS pro připojení k databázi SQL
Protokol TLS (Transport Layer Security) používají všechny ovladače, které Microsoft používá nebo podporuje pro připojení k databázi Azure SQL. Není potřeba žádná zvláštní konfigurace. Pro všechna připojení k serveru SQL Server nebo k databázi Azure SQL doporučujeme nastavit u všech aplikací následující konfigurace nebo jejich ekvivalenty:

 - **Encrypt = On**
 - **TrustServerCertificate = Off**

Některé systémy pro uvedená klíčová slova konfigurací používají rozdílná, ale ekvivalentní klíčová slova. Tyto konfigurace zajistí, že ovladač klienta ověří identitu certifikátu TLS přijatého ze serveru.

Také doporučujeme zakázat v klientovi protokoly TLS 1.1 a 1.0, pokud potřebujete dodržovat standard PCI–DSS (Payment Card Industry – Data Security Standard).

Ovladače od jiných výrobců nemusí ve výchozím nastavení protokol TLS používat. To může při připojování k databázi Azure SQL hrát důležitou roli. Aplikace s integrovanými ovladači vám nemusí umožnit řídit tato nastavení připojení. Doporučujeme, abyste si ověřili zabezpečení takovýchto ovladačů a aplikací, dříve než je použijete v systémech, které pracují s důvěrnými osobními údaji.

## <a name="libraries"></a>Knihovny

K připojení k Azure SQL Database můžete použít různé knihovny a architektury. Podívejte se na naše [výukové programy Začínáme,](https://aka.ms/sqldev) abyste mohli rychle začít s programovacími jazyky, jako je C#, Java, Node.js, PHP a Python. Pak vytvořte aplikaci pomocí SQL Serveru v Linuxu nebo Windows nebo Dockeru v macOS.

V následující tabulce jsou uvedeny knihovny připojení nebo *ovladače,* které mohou klientské aplikace používat z různých jazyků pro připojení a používání serveru SQL Server spuštěného místně nebo v cloudu. Můžete je použít na Linuxu, Windows nebo Dockeru a použít je k připojení k Azure SQL Database a Azure SQL Data Warehouse. 

| Jazyk | Platforma | Další zdroje | Stáhnout | Začínáme |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Microsoft ADO.NET pro SQL Server](https://docs.microsoft.com/sql/connect/ado-net/microsoft-ado-net-sql-server) | [Stáhnout](https://www.microsoft.com/net/download/) | [Začínáme](https://www.microsoft.com/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows, Linux, macOS | [Ovladač JDBC společnosti Microsoft pro sql server](https://msdn.microsoft.com/library/mt484311.aspx) | [Stáhnout](https://go.microsoft.com/fwlink/?linkid=852460) |  [Začínáme](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows, Linux, macOS| [PHP SQL ovladač pro SQL Server](https://docs.microsoft.com/sql/connect/php/microsoft-php-driver-for-sql-server) | [Stáhnout](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server) | [Začínáme](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu/)
| Node.js | Windows, Linux, macOS | [Ovladač Node.js pro SQL Server](https://msdn.microsoft.com/library/mt652093.aspx) | [Nainstalovat](https://msdn.microsoft.com/library/mt652094.aspx) |  [Začínáme](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu)
| Python | Windows, Linux, macOS | [Ovladač SQL pythonu](https://msdn.microsoft.com/library/mt652092.aspx) | Volby instalace: <br/> \*[pymssql](https://msdn.microsoft.com/library/mt694094.aspx) <br/> \*[pyodbc řekl:](https://msdn.microsoft.com/library/mt763257.aspx) |  [Začínáme](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows, Linux, macOS | [Ovladač Ruby pro SQL Server](https://msdn.microsoft.com/library/mt691981.aspx) | [Nainstalovat](https://msdn.microsoft.com/library/mt711041.aspx) | [Začínáme](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows, Linux, macOS | [Ovladač Rozhraní Microsoft ODBC pro sql server](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) | [Stáhnout](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) |  

V následující tabulce jsou uvedeny příklady rozhraní ORM (object-relational mapping) a webových architektur, které mohou klientské aplikace používat se serverem SQL Server spuštěným místně nebo v cloudu. Můžete použít architektury na Linuxu, Windows nebo Dockeru a použít je k připojení k databázi SQL a datovému skladu SQL. 

| Jazyk | Platforma | ORM (y) |
| :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Entity Framework](https://docs.microsoft.com/ef)<br>[Entity Framework Core](https://docs.microsoft.com/ef/core/index) |
| Java | Windows, Linux, macOS |[Režim orm hibernace](https://hibernate.org/orm)|
| PHP | Windows, Linux, macOS | [Laravel (Švýc.)](https://laravel.com/docs/eloquent)<br>[Doktrína](https://www.doctrine-project.org/projects/orm.html) |
| Node.js | Windows, Linux, macOS | [Pokračování ORM](https://sequelize.org/) |
| Python | Windows, Linux, macOS |[Django](https://www.djangoproject.com/) |
| Ruby | Windows, Linux, macOS | [Ruby na kolejích](https://rubyonrails.org/) |
||||

## <a name="next-steps"></a>Další kroky

- Informace o připojení k architektuře najdete v článku o [architektuře připojení k databázi Azure SQL](sql-database-connectivity-architecture.md).
- [Vyhledání ovladačů serveru SQL Server,](https://msdn.microsoft.com/library/mt654049.aspx) které se používají k připojení z klientských aplikací
- Připojení k databázi SQL:
  - [Připojení k SQL Database s použitím rozhraní .NET (C#)](sql-database-connect-query-dotnet.md) 
  - [Připojení k SQL Database s použitím jazyka PHP](sql-database-connect-query-php.md) 
  - [Připojení k SQL Database s použitím prostředí Node.js](sql-database-connect-query-nodejs.md) 
  - [Připojení k SQL Database s použitím jazyka Java](sql-database-connect-query-java.md) 
  - [Připojení k SQL Database s použitím jazyka Python](sql-database-connect-query-python.md)
  - [Připojení k SQL Database s použitím prostředí Ruby](sql-database-connect-query-ruby.md)
- Opakování příkladů kódu logiky:
  - [Připojení k SQL pružně pomocí ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
  - [Připojení k SQL s PHP][step-4-connect-resiliently-to-sql-with-php-p42h]

<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php
