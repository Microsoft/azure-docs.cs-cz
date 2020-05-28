---
title: Připojení a odkaz na obsah dotazu
description: Odkaz na Azure SQL Database rychlý Start ukazuje, jak se připojit k Azure SQL Database a dotazovat se na ně.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: ea4b201cd9ad6fa295bbccafe445733aadcf31c1
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "84054315"
---
# <a name="quickstarts-azure-sql-database-connect-and-query"></a>Rychlé starty: Azure SQL Database připojení a dotazování
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Následující dokument obsahuje odkazy na příklady Azure, které ukazují, jak se připojit a dotazovat Azure SQL Database. Obsahuje také několik doporučení týkající se protokolu TLS (Transport Level Security).

## <a name="quickstarts"></a>Rychlé starty

| |  |
|---|---|
|[SQL Server Management Studio](connect-query-ssms.md)|Tento rychlý Start ukazuje použití SSMS pro připojení k Azure SQL Database a následné použití příkazů jazyka Transact-SQL k dotazování, vkládání, aktualizaci a odstraňování dat v databázi.|
|[Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)|V tomto rychlém startu se dozvíte, jak použít Azure Data Studio pro připojení k Azure SQL Database a následné použití příkazů jazyka Transact-SQL (T-SQL) k vytvoření databáze tutorialdb používaného v Azure Data Studioch kurzech.|
|[portál Azure](connect-query-portal.md)|Tento rychlý start ukazuje použití editoru dotazů pro připojení k databázi SQL a následné použití příkazů jazyka Transact-SQL k dotazování, vkládání, aktualizaci a odstraňování dat v databázi.|
|[Visual Studio Code](connect-query-vscode.md)|Tento rychlý Start ukazuje, jak použít Visual Studio Code pro připojení k Azure SQL Database a následné použití příkazů jazyka Transact-SQL k dotazování, vkládání, aktualizaci a odstraňování dat v databázi.|
|[.NET se sadou Visual Studio](connect-query-dotnet-visual-studio.md)|Tento rychlý Start ukazuje použití rozhraní .NET Framework k vytvoření programu v jazyce C# se sadou Visual Studio pro připojení k Azure SQL Database a použití příkazů jazyka Transact-SQL k dotazování dat.|
|[.NET Core](connect-query-dotnet-core.md)|Tento rychlý Start ukazuje použití .NET Core v systému Windows, Linux nebo macOS k vytvoření programu v jazyce C# pro připojení k Azure SQL Database a použití příkazů jazyka Transact-SQL k dotazování dat.|
|[Přejít](connect-query-go.md)|V tomto rychlém startu se dozvíte, jak pomocí jazyka přejít k Azure SQL Database připojit. Ukazuje se také použití příkazů jazyka Transact-SQL k dotazování a úpravě dat.|
|[Java](connect-query-java.md)|Tento rychlý Start ukazuje použití jazyka Java pro připojení k Azure SQL Database a následné použití příkazů jazyka Transact-SQL k dotazování dat.|
|[Node. js](connect-query-nodejs.md)|Tento rychlý Start ukazuje použití Node. js k vytvoření programu pro připojení k Azure SQL Database a použití příkazů jazyka Transact-SQL k dotazování dat.|
|[PHP](connect-query-php.md)|Tento rychlý Start ukazuje použití PHP k vytvoření programu pro připojení k Azure SQL Database a použití příkazů jazyka Transact-SQL k dotazování dat.|
|[Python](connect-query-python.md)|Tento rychlý Start ukazuje použití Pythonu pro připojení k Azure SQL Database a použití příkazů jazyka Transact-SQL k dotazování dat. |
|[Ruby](connect-query-ruby.md)|Tento rychlý Start ukazuje použití Ruby k vytvoření programu pro připojení k Azure SQL Database a použití příkazů jazyka Transact-SQL k dotazování dat.|
|[R](connect-query-r.md)|Tento rychlý Start ukazuje použití jazyka R s Azure SQL Database Machine Learning Services k vytvoření programu pro připojení k Azure SQL Database a použití příkazů jazyka Transact-SQL k dotazování dat.|
|||

## <a name="tls-considerations-for-sql-database-connectivity"></a>Důležité informace o použití protokolu TLS pro připojení k databázi SQL

Protokol TLS (Transport Layer Security) používají všechny ovladače, které Microsoft používá nebo podporuje pro připojení k databázi Azure SQL. Není potřeba žádná zvláštní konfigurace. Pro všechna připojení k SQL Server, Azure SQL Database nebo Azure SQL Managed instance doporučujeme, aby všechny aplikace nastavily následující konfigurace, nebo jejich ekvivalenty:

- **Encrypt = On**
- **TrustServerCertificate = Off**

Některé systémy pro uvedená klíčová slova konfigurací používají rozdílná, ale ekvivalentní klíčová slova. Tyto konfigurace zajistí, že ovladač klienta ověří identitu certifikátu TLS přijatého ze serveru.

Také doporučujeme zakázat v klientovi protokoly TLS 1.1 a 1.0, pokud potřebujete dodržovat standard PCI–DSS (Payment Card Industry – Data Security Standard).

Ovladače od jiných výrobců nemusí ve výchozím nastavení protokol TLS používat. To může při připojování k databázi Azure SQL hrát důležitou roli. Aplikace s integrovanými ovladači vám nemusí umožnit řídit tato nastavení připojení. Doporučujeme, abyste si ověřili zabezpečení takovýchto ovladačů a aplikací, dříve než je použijete v systémech, které pracují s důvěrnými osobními údaji.

## <a name="libraries"></a>Knihovny

K připojení k Azure SQL Database nebo spravované instanci SQL Azure můžete použít různé knihovny a architektury. [Podrobné kurzy](https://aka.ms/sqldev) Začínáme vám pomohou rychle začít s programovacími jazyky, jako je C#, Java, Node. js, php a Python. Pak sestavte aplikaci pomocí SQL Server on Linux nebo Windows nebo Docker v macOS.

Následující tabulka obsahuje seznam knihoven nebo *ovladačů* připojení, které klientské aplikace mohou používat z různých jazyků pro připojení a používání SQL Server v místním prostředí nebo v cloudu. Můžete je používat v systémech Linux, Windows nebo Docker a používat je k připojení k Azure SQL Database a Azure SQL Data Warehouse.

| Jazyk | Platforma | Další zdroje | Stáhnout | Začínáme |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Microsoft ADO.NET pro SQL Server](https://docs.microsoft.com/sql/connect/ado-net/microsoft-ado-net-sql-server) | [Stáhnout](https://www.microsoft.com/net/download/) | [Začínáme](https://www.microsoft.com/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows, Linux, macOS | [Ovladač Microsoft JDBC pro SQL Server](https://msdn.microsoft.com/library/mt484311.aspx) | [Stáhnout](https://go.microsoft.com/fwlink/?linkid=852460) |  [Začínáme](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows, Linux, macOS| [Ovladač PHP SQL pro SQL Server](https://docs.microsoft.com/sql/connect/php/microsoft-php-driver-for-sql-server) | [Stáhnout](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server) | [Začínáme](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu/)
| Node.js | Windows, Linux, macOS | [Ovladač Node. js pro SQL Server](https://msdn.microsoft.com/library/mt652093.aspx) | [Instalace](https://msdn.microsoft.com/library/mt652094.aspx) |  [Začínáme](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu)
| Python | Windows, Linux, macOS | [Ovladač Python SQL](https://msdn.microsoft.com/library/mt652092.aspx) | Možnosti instalace: <br/> \*[pymssql](https://msdn.microsoft.com/library/mt694094.aspx) <br/> \*[pyodbc](https://msdn.microsoft.com/library/mt763257.aspx) |  [Začínáme](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows, Linux, macOS | [Ovladač Ruby pro SQL Server](https://msdn.microsoft.com/library/mt691981.aspx) | [Instalace](https://msdn.microsoft.com/library/mt711041.aspx) | [Začínáme](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows, Linux, macOS | [Ovladač Microsoft ODBC pro SQL Server](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) | [Stáhnout](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) |  

V následující tabulce jsou uvedeny příklady rozhraní a webové architektury pro mapování relačních objektů (ORM), které klientské aplikace mohou používat s SQL Server, Azure SQL Database, spravované instance Azure SQL nebo Azure synapse Analytics. Můžete použít rozhraní v systémech Linux, Windows nebo Docker.

| Jazyk | Platforma | ORM (y) |
| :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Entity Framework](https://docs.microsoft.com/ef)<br>[Entity Framework Core](https://docs.microsoft.com/ef/core/index) |
| Java | Windows, Linux, macOS |[ORM – hibernace](https://hibernate.org/orm)|
| PHP | Windows, Linux, macOS | [Laravel (Eloquent)](https://laravel.com/docs/eloquent)<br>[Doctrine](https://www.doctrine-project.org/projects/orm.html) |
| Node.js | Windows, Linux, macOS | [Sequelize ORM](https://sequelize.org/) |
| Python | Windows, Linux, macOS |[Django](https://www.djangoproject.com/) |
| Ruby | Windows, Linux, macOS | [Ruby na železnici](https://rubyonrails.org/) |
||||

## <a name="next-steps"></a>Další kroky

- Informace o připojení k architektuře najdete v článku o [architektuře připojení k databázi Azure SQL](connectivity-architecture.md).
- Vyhledat [ovladače SQL Server](https://msdn.microsoft.com/library/mt654049.aspx) používané pro připojení z klientských aplikací
- Připojit k SQL Database:
  - [Připojení k SQL Database s použitím rozhraní .NET (C#)](connect-query-dotnet-core.md)
  - [Připojení k SQL Database s použitím jazyka PHP](connect-query-php.md)
  - [Připojení k SQL Database s použitím prostředí Node.js](connect-query-nodejs.md)
  - [Připojení k SQL Database s použitím jazyka Java](connect-query-java.md)
  - [Připojení k SQL Database s použitím jazyka Python](connect-query-python.md)
  - [Připojení k SQL Database s použitím prostředí Ruby](connect-query-ruby.md)
- Příklady kódu logiky opakování:
  - [Odolné připojení k SQL pomocí ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
  - [Odolné připojení k SQL pomocí PHP][step-4-connect-resiliently-to-sql-with-php-p42h]

<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php
