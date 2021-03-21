---
title: Připojení a dotazování
description: Odkazy na Azure SQL Database rychlý Start ukazuje, jak se připojit k a dotazovat se na Azure SQL Database a Azure SQL Managed instance.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: guide
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/17/2021
ms.openlocfilehash: 96f40f1abd14987b7c404f5a4210a2cab1fa9143
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594168"
---
# <a name="azure-sql-database-and-azure-sql-managed-instance-connect-and-query-articles"></a>Články Azure SQL Database a Azure SQL Managed instance Connect a dotazy
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Následující dokument obsahuje odkazy na příklady Azure, které ukazují, jak se připojit a dotazovat Azure SQL Database a Azure SQL Managed instance. Některá související doporučení pro zabezpečení úrovně transportu najdete v tématu [požadavky TLS pro připojení k databázi](#tls-considerations-for-database-connectivity).

## <a name="quickstarts"></a>Rychlé starty

| Rychlé zprovoznění | Description |
|---|---|
|[SQL Server Management Studio](connect-query-ssms.md)|Tento rychlý Start ukazuje použití SSMS pro připojení k databázi a následné použití příkazů jazyka Transact-SQL k dotazování, vkládání, aktualizaci a odstraňování dat v databázi.|
|[Azure Data Studio](/sql/azure-data-studio/quickstart-sql-database?toc=%2fazure%2fsql-database%2ftoc.json)|Tento rychlý Start ukazuje, jak použít Azure Data Studio pro připojení k databázi a následné použití příkazů jazyka Transact-SQL (T-SQL) k vytvoření databáze tutorialdb používaného v Azure Data Studioch kurzech.|
|[Azure Portal](connect-query-portal.md)|Tento rychlý Start ukazuje použití Editoru dotazů pro připojení k databázi (pouze Azure SQL Database) a následné použití příkazů jazyka Transact-SQL k dotazování, vkládání, aktualizaci a odstraňování dat v databázi.|
|[Visual Studio Code](connect-query-vscode.md)|Tento rychlý Start ukazuje, jak použít Visual Studio Code pro připojení k databázi a následné použití příkazů jazyka Transact-SQL k dotazování, vkládání, aktualizaci a odstraňování dat v databázi.|
|[.NET se sadou Visual Studio](connect-query-dotnet-visual-studio.md)|Tento rychlý Start ukazuje použití rozhraní .NET Framework k vytvoření programu v jazyce C# se sadou Visual Studio pro připojení k databázi a použití příkazů jazyka Transact-SQL k dotazování dat.|
|[.NET Core](connect-query-dotnet-core.md)|Tento rychlý Start ukazuje použití .NET Core v systému Windows, Linux nebo macOS k vytvoření programu v jazyce C# pro připojení k databázi a použití příkazů jazyka Transact-SQL k dotazování dat.|
|[Přejít](connect-query-go.md)|V tomto rychlém startu se dozvíte, jak použít Přejít k připojení k databázi. Ukazuje se také použití příkazů jazyka Transact-SQL k dotazování a úpravě dat.|
|[Java](connect-query-java.md)|Tento rychlý Start ukazuje použití jazyka Java pro připojení k databázi a následné použití příkazů jazyka Transact-SQL k dotazování dat.|
|[Node.js](connect-query-nodejs.md)|Tento rychlý Start ukazuje, jak použít Node.js k vytvoření programu pro připojení k databázi a použití příkazů jazyka Transact-SQL k dotazování dat.|
|[PHP](connect-query-php.md)|Tento rychlý Start ukazuje použití PHP k vytvoření programu pro připojení k databázi a použití příkazů jazyka Transact-SQL k dotazování dat.|
|[Python](connect-query-python.md)|Tento rychlý Start ukazuje použití Pythonu pro připojení k databázi a použití příkazů jazyka Transact-SQL k dotazování dat. |
|[Ruby](connect-query-ruby.md)|Tento rychlý Start ukazuje použití Ruby k vytvoření programu pro připojení k databázi a použití příkazů jazyka Transact-SQL k dotazování dat.|
|[R](connect-query-r.md)|Tento rychlý Start ukazuje použití jazyka R s Azure SQL Database Machine Learning Services k vytvoření programu pro připojení k databázi v Azure SQL Database a použití příkazů jazyka Transact-SQL k dotazování dat.|
|||

## <a name="get-server-connection-information"></a>Získat informace o připojení k serveru

Získejte informace o připojení, které potřebujete pro připojení k databázi v Azure SQL Database. Pro nadcházející postupy budete potřebovat plně kvalifikovaný název serveru nebo název hostitele, název databáze a přihlašovací údaje.

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

2. Přejděte na stránku **databáze SQL**  nebo **spravované instance SQL** .

3. Na stránce **Přehled** zkontrolujte plně kvalifikovaný název serveru vedle **názvu serveru** pro databázi v Azure SQL Database nebo plně kvalifikovaného názvu serveru (nebo IP adresy) vedle **hostitele** spravované instance Azure SQL nebo SQL Server na virtuálním počítači Azure. Pokud chcete zkopírovat název serveru nebo název hostitele, najeďte na něj ukazatelem myši a vyberte ikonu **kopírování** .

> [!NOTE]
> Informace o připojení pro SQL Server na virtuálním počítači Azure najdete v tématu [připojení k instanci SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server).

## <a name="get-adonet-connection-information-optional---sql-database-only"></a>Získat informace o připojení ADO.NET (jenom volitelné SQL Database)

1. V Azure Portal přejděte do okna databáze a v části **Nastavení** vyberte **připojovací řetězce**.

2. Zkontrolujte úplný připojovací řetězec **ADO.NET**.

    ![Připojovací řetězec pro ADO.NET](./media/connect-query-dotnet-core/adonet-connection-string2.png)

3. Pokud ho máte v úmyslu používat, zkopírujte připojovací řetězec **ADO.NET** .

## <a name="tls-considerations-for-database-connectivity"></a>Požadavky TLS pro připojení databáze

Protokol TLS (Transport Layer Security) se používá u všech ovladačů, které Microsoft poskytuje nebo podporuje pro připojení k databázím v Azure SQL Database nebo v Azure SQL Managed instance. Není potřeba žádná zvláštní konfigurace. Pro všechna připojení k instanci SQL Server, databázi v Azure SQL Database nebo instanci spravované instance Azure SQL doporučujeme, aby všechny aplikace nastavily následující konfigurace, nebo jejich ekvivalenty:

- **Encrypt = On**
- **TrustServerCertificate = Off**

Některé systémy pro uvedená klíčová slova konfigurací používají rozdílná, ale ekvivalentní klíčová slova. Tyto konfigurace zajistí, že ovladač klienta ověří identitu certifikátu TLS přijatého ze serveru.

Také doporučujeme zakázat v klientovi protokoly TLS 1.1 a 1.0, pokud potřebujete dodržovat standard PCI–DSS (Payment Card Industry – Data Security Standard).

Ovladače od jiných výrobců nemusí ve výchozím nastavení protokol TLS používat. Může to být faktor při připojování k Azure SQL Database nebo spravované instanci SQL Azure. Aplikace s integrovanými ovladači vám nemusí umožnit řídit tato nastavení připojení. Doporučujeme, abyste si ověřili zabezpečení takovýchto ovladačů a aplikací, dříve než je použijete v systémech, které pracují s důvěrnými osobními údaji.

## <a name="libraries"></a>Knihovny

K připojení k Azure SQL Database nebo spravované instanci SQL Azure můžete použít různé knihovny a architektury. [Výukové kurzy](https://aka.ms/sqldev) Začínáme vám pomohou rychle začít s programovacími jazyky, jako je C#, Java, Node.js, php a Python. Pak sestavte aplikaci pomocí SQL Server on Linux nebo Windows nebo Docker v macOS.

Následující tabulka obsahuje seznam knihoven nebo *ovladačů* připojení, které klientské aplikace mohou používat z různých jazyků pro připojení a používání SQL Server v místním prostředí nebo v cloudu. Můžete je používat v systémech Linux, Windows nebo Docker a používat je k připojení k Azure SQL Database, spravované instanci SQL Azure a analýzám Azure synapse.

| Jazyk | Platforma | Další zdroje informací | Stáhnout | Začínáme |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Microsoft ADO.NET pro SQL Server](/sql/connect/ado-net/microsoft-ado-net-sql-server) | [Stáhnout](https://www.microsoft.com/net/download/) | [Začínáme](https://www.microsoft.com/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows, Linux, macOS | [Ovladač Microsoft JDBC pro SQL Server](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server/) | [Stáhnout](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server) |  [Začínáme](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows, Linux, macOS| [Ovladač PHP SQL pro SQL Server](/sql/connect/php/microsoft-php-driver-for-sql-server) | [Stáhnout](/sql/connect/php/download-drivers-php-sql-server) | [Začínáme](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu/)
| Node.js | Windows, Linux, macOS | [Node.js ovladače pro SQL Server](/sql/connect/node-js/node-js-driver-for-sql-server/) | [Instalace](/sql/connect/node-js/step-1-configure-development-environment-for-node-js-development/) |  [Začínáme](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu)
| Python | Windows, Linux, macOS | [Ovladač Python SQL](/sql/connect/python/python-driver-for-sql-server/) | Možnosti instalace: <br/> \*[pymssql](/sql/connect/python/pymssql/step-1-configure-development-environment-for-pymssql-python-development/) <br/> \*[pyodbc](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development/) |  [Začínáme](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows, Linux, macOS | [Ovladač Ruby pro SQL Server](/sql/connect/ruby/ruby-driver-for-sql-server/) | [Instalace](/sql/connect/ruby/step-1-configure-development-environment-for-ruby-development/) | [Začínáme](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows, Linux, macOS | [Ovladač Microsoft ODBC pro SQL Server](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server/) | [Stáhnout](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server/) |  

V následující tabulce jsou uvedeny příklady rozhraní a webové architektury pro mapování relačních objektů (ORM), které klientské aplikace mohou používat s SQL Server, Azure SQL Database, spravované instance Azure SQL nebo Azure synapse Analytics. Můžete použít rozhraní v systémech Linux, Windows nebo Docker.

| Jazyk | Platforma | ORM (y) |
| :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Entity Framework](/ef)<br>[Entity Framework Core](/ef/core/index) |
| Java | Windows, Linux, macOS |[ORM – hibernace](https://hibernate.org/orm)|
| PHP | Windows, Linux, macOS | [Laravel (Eloquent)](https://laravel.com/docs/eloquent)<br>[Doctrine](https://www.doctrine-project.org/projects/orm.html) |
| Node.js | Windows, Linux, macOS | [Sequelize ORM](https://sequelize.org/) |
| Python | Windows, Linux, macOS |[Django](https://www.djangoproject.com/) |
| Ruby | Windows, Linux, macOS | [Ruby na železnici](https://rubyonrails.org/) |
||||

## <a name="next-steps"></a>Další kroky

- Informace o připojení k architektuře najdete v článku o [architektuře připojení k databázi Azure SQL](connectivity-architecture.md).
- Najděte [SQL Server ovladače](/sql/connect/sql-connection-libraries/) , které se používají pro připojení z klientských aplikací.
- Připojení k Azure SQL Database nebo spravované instanci Azure SQL:
  - [Připojení a dotazování pomocí .NET (C#)](connect-query-dotnet-core.md)
  - [Připojení a dotazování pomocí PHP](connect-query-php.md)
  - [Připojení a dotazování pomocí Node.js](connect-query-nodejs.md)
  - [Připojení a dotazování pomocí jazyka Java](connect-query-java.md)
  - [Připojení a dotazování pomocí Pythonu](connect-query-python.md)
  - [Připojení a dotazování pomocí Ruby](connect-query-ruby.md)
  - [Nainstalujte Sqlcmd a bcp SQL Server nástroje příkazového řádku](/sql/linux/sql-server-linux-setup-tools) pro uživatele se systémem Linux – pro uživatele se systémem Linux se pokuste připojit k Azure SQL Database nebo spravované instanci Azure SQL pomocí nástroje [Sqlcmd](/sql/ssms/scripting/sqlcmd-use-the-utility).
- Příklady kódu logiky opakování:
  - [Odolné připojení pomocí ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
  - [Odolné připojení pomocí PHP][step-4-connect-resiliently-to-sql-with-php-p42h]

<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: /sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: /sql/connect/php/step-4-connect-resiliently-to-sql-with-php
