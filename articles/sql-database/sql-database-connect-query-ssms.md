---
title: 'SSMS: Připojení a dotaz dat'
description: Zjistěte, jak se připojit k SQL Database na Azure pomocí služby SQL Server Management Studio (SSMS). Potom spustíte příkazy jazyka Transact-SQL (T-SQL) k dotazování a úpravě dat.
keywords: Připojení k SQL Database, SQL Server Management Studio
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/10/2020
ms.openlocfilehash: 31bd47128a272e75d7021180b536fe6bf7420f55
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79299290"
---
# <a name="quickstart-use-sql-server-management-studio-to-connect-and-query-an-azure-sql-database"></a>Úvodní příručka: Připojení a dotazování na databázi Azure SQL pomocí sady SQL Server Management Studio

V tomto rychlém startu se dozvíte, jak pomocí SQL Server Management Studio (SSMS) připojit k databázi Azure SQL a spustit některé dotazy.

## <a name="prerequisites"></a>Požadavky

Dokončení tohoto rychlého startu vyžaduje následující položky:

- [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms/).
- Ukázková databáze AdventureWorksLT. Pokud potřebujete pracovní kopii databáze AdventureWorksLT, vytvořte ji vyplněním rychlého startu [vytvoření databáze Azure SQL.](sql-database-single-database-get-started.md)
    - Skripty v tomto článku jsou zapsány k použití adventureworkslt databáze. Pokud používáte spravovanou instanci, musíte buď importovat databázi AdventureWorks do databáze instancí, nebo upravit skripty v tomto článku tak, aby používaly databázi Wide World Importers.

Pokud chcete jednoduše spustit některé ad-hoc dotazy bez instalace SSMS, přečtěte si [panel Rychlý start: Pomocí editoru dotazů na webu Azure Portal můžete zadat dotaz na databázi SQL](sql-database-connect-query-portal.md).

## <a name="get-sql-server-connection-information"></a>Získání informací o připojení k serveru SQL

Získejte informace o připojení, které potřebujete k připojení k databázi. K dokončení tohoto rychlého startu budete potřebovat úplný název serveru nebo název hostitele, název databáze a přihlašovací údaje.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

2. Přejděte do **databáze SQL** nebo instance **spravované SQL,** na kterou se chcete dotazovat.

3. Na stránce **Přehled** zkopírujte úplný název serveru. Je vedle **názvu serveru** pro jednu databázi nebo plně kvalifikovaný název serveru vedle **hostitele** pro spravovanou instanci. Plně kvalifikovaný název vypadá takto: *servername.database.windows.net*, s výjimkou, že má skutečný název serveru.

## <a name="connect-to-your-database"></a>Připojení k databázi

V SSMS se připojte k serveru Azure SQL Database.

> [!IMPORTANT]
> Server Azure SQL Database naslouchá na portu 1433. Chcete-li se připojit k serveru SQL Database zpoza podnikové brány firewall, musí mít brána firewall tento port otevřený.

1. Otevřete aplikaci SSMS.

2. Zobrazí se dialogové okno **Připojení k serveru**. Zadejte následující informace:

   | Nastavení      | Navrhovaná hodnota    | Popis |
   | ------------ | ------------------ | ----------- |
   | **Typ serveru** | Databázový stroj | Požadovaná hodnota. |
   | **Název serveru** | Plně kvalifikovaný název serveru | Něco jako: **servername.database.windows.net**. |
   | **Ověřování** | Ověřování SQL Serveru | Tento kurz používá ověřování SQL. |
   | **Přihlášení** | ID uživatele účtu správce serveru | ID uživatele z účtu správce serveru použitého k vytvoření serveru. |
   | **Heslo** | Heslo účtu správce serveru | Heslo z účtu správce serveru použitého k vytvoření serveru. |
   ||||

   ![Připojení k serveru](./media/sql-database-connect-query-ssms/connect.png)  

3. V dialogovém okně **Připojit k serveru** vyberte **Možnosti.** V rozevírací nabídce **Připojit k databázi** vyberte **položku mySampleDatabase**. Dokončení mj. [Prerequisites section](#prerequisites) Pokud vaše pracovní kopie adventureworks databáze má jiný název než mySampleDatabase, vyberte ji místo.

   ![připojení k databázi na serveru](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. Vyberte **Connect** (Připojit). Otevře se okno Průzkumníka objektů.

5. Chcete-li zobrazit objekty databáze, rozbalte **položku Databáze** a potom rozbalte uzel databáze.

   ![objekty mySampleDatabase](./media/sql-database-connect-query-ssms/connected.png)  

## <a name="query-data"></a>Dotazování dat

Spusťte tento kód [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) Transact-SQL pro dotaz na 20 nejlepších produktů podle kategorií.

1. V Průzkumníkovi objektů klepněte pravým tlačítkem myši na **položku mySampleDatabase** a vyberte **nový dotaz**. Otevře se nové okno dotazu připojené k databázi.

2. V okně dotazu vložte následující dotaz SQL:

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

3. Na panelu nástrojů vyberte **Spustit,** chcete-li `Product` `ProductCategory` spustit dotaz a načíst data z tabulek a.

    ![dotaz pro načtení dat z tabulky Product a ProductCategory](./media/sql-database-connect-query-ssms/query2.png)

### <a name="insert-data"></a>Vložení dat

Spusťte tento kód [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) Transact-SQL `SalesLT.Product` a vytvořte v tabulce nový produkt.

1. Nahraďte předchozí dotaz tímto dotazem.

   ```sql
   INSERT INTO [SalesLT].[Product]
           ( [Name]
           , [ProductNumber]
           , [Color]
           , [ProductCategoryID]
           , [StandardCost]
           , [ListPrice]
           , [SellStartDate] )
     VALUES
           ('myNewProduct'
           ,123456789
           ,'NewColor'
           ,1
           ,100
           ,100
           ,GETDATE() );
   ```

2. Vyberte **Spustit,** chcete-li `Product` do tabulky vložit nový řádek. Zobrazí se podokno **Zprávy** **(1 ovlivněný řádek).**

#### <a name="view-the-result"></a>Zobrazit výsledek

1. Nahraďte předchozí dotaz tímto dotazem.

   ```sql
   SELECT * FROM [SalesLT].[Product]
   WHERE Name='myNewProduct'
   ```

2. Vyberte **Provést**. Zobrazí se následující výsledek.

   ![výsledek dotazu na produktové tabulky](./media/sql-database-connect-query-ssms/result.png)

### <a name="update-data"></a>Aktualizace dat

Spusťte tento kód [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL a upravte nový produkt.

1. Nahraďte předchozí dotaz tímto dotazem, který vrátí nový záznam vytvořený dříve:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Vyberte **Spustit,** chcete-li `Product` aktualizovat zadaný řádek v tabulce. Zobrazí se podokno **Zprávy** **(1 ovlivněný řádek).**

### <a name="delete-data"></a>Odstranění dat

Spusťte tento kód [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL a odeberte nový produkt.

1. Nahraďte předchozí dotaz tímto dotazem.

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Vyberte **Spustit,** chcete-li `Product` odstranit zadaný řádek v tabulce. Zobrazí se podokno **Zprávy** **(1 ovlivněný řádek).**

## <a name="next-steps"></a>Další kroky

- Informace o serveru SSMS naleznete v tématu [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx).
- Pokud se chcete připojit a dotazovat se pomocí portálu Azure, přečtěte si informace [o připojení a dotazu pomocí editoru sql query na webu Azure Portal](sql-database-connect-query-portal.md).
- Informace o připojení a dotazování pomocí Visual Studio Code najdete v tématu [Připojení a dotazování pomocí Visual Studio Code](sql-database-connect-query-vscode.md).
- Informace o připojení a dotazování pomocí .NET najdete v tématu [Připojení a dotazování pomocí .NET](sql-database-connect-query-dotnet.md).
- Informace o připojení a dotazování pomocí PHP najdete v tématu [Připojení a dotazování pomocí PHP](sql-database-connect-query-php.md).
- Informace o připojení a dotazování pomocí Node.js najdete v tématu [Připojení a dotazování pomocí Node.js](sql-database-connect-query-nodejs.md).
- Informace o připojení a dotazování pomocí Javy najdete v tématu [Připojení a dotazování pomocí Javy](sql-database-connect-query-java.md).
- Informace o připojení a dotazování pomocí Pythonu najdete v tématu [Připojení a dotazování pomocí Pythonu](sql-database-connect-query-python.md).
- Informace o připojení a dotazování pomocí Ruby najdete v tématu [Připojení a dotazování pomocí Ruby](sql-database-connect-query-ruby.md).
