---
title: 'SSMS: připojení a dotazování dat'
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "79299290"
---
# <a name="quickstart-use-sql-server-management-studio-to-connect-and-query-an-azure-sql-database"></a>Rychlý Start: použití SQL Server Management Studio k připojení a dotazování databáze SQL Azure

V tomto rychlém startu se dozvíte, jak pomocí SQL Server Management Studio (SSMS) se připojit k databázi SQL Azure a spustit některé dotazy.

## <a name="prerequisites"></a>Požadavky

Dokončení tohoto rychlého startu vyžaduje následující položky:

- [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms/).
- Ukázková databáze AdventureWorksLT. Pokud potřebujete pracovní kopii databáze AdventureWorksLT, vytvořte ji pomocí průvodce [vytvořením rychlé zprovoznění databáze SQL Azure](sql-database-single-database-get-started.md) .
    - Skripty v tomto článku jsou vytvořeny pro použití databáze AdventureWorksLT. Pokud používáte spravovanou instanci, musíte buď importovat databázi AdventureWorks do databáze instance, nebo upravit skripty v tomto článku, aby používaly databázi World Importers.

Pokud chcete jenom spustit některé dotazy ad-hoc bez instalace SSMS, přečtěte si [rychlý Start: použití Editoru dotazů Azure Portal k dotazování databáze SQL](sql-database-connect-query-portal.md).

## <a name="get-sql-server-connection-information"></a>Získat informace o připojení k SQL serveru

Získejte informace o připojení, které potřebujete pro připojení k vaší databázi. K dokončení tohoto rychlého startu budete potřebovat plně kvalifikovaný název serveru nebo název hostitele, název databáze a přihlašovací informace.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

2. Přejděte do **databáze SQL** nebo **spravované instance SQL** , pro kterou chcete zadat dotaz.

3. Na stránce **Přehled** zkopírujte plně kvalifikovaný název serveru. Je vedle **názvu serveru** pro izolovanou databázi nebo plně kvalifikovaného názvu serveru vedle **hostitele** pro spravovanou instanci. Plně kvalifikovaný název vypadá takto: *servername.Database.Windows.NET*, s výjimkou toho, že má skutečný název serveru.

## <a name="connect-to-your-database"></a>Připojení k databázi

V SSMS se připojte k serveru Azure SQL Database.

> [!IMPORTANT]
> Azure SQL Database Server naslouchá na portu 1433. Aby bylo možné připojit se k serveru SQL Database za podnikovou bránou firewall, musí mít brána firewall otevřený tento port.

1. Otevřete aplikaci SSMS.

2. Zobrazí se dialogové okno **Připojení k serveru**. Zadejte následující informace:

   | Nastavení      | Navrhovaná hodnota    | Popis |
   | ------------ | ------------------ | ----------- |
   | **Typ serveru** | Databázový stroj | Požadovaná hodnota. |
   | **Název serveru** | Plně kvalifikovaný název serveru | Něco jako: **servername.Database.Windows.NET**. |
   | **Authentication** | Ověřování SQL Serveru | V tomto kurzu se používá ověřování SQL. |
   | **Hlas** | ID uživatele účtu správce serveru | ID uživatele z účtu správce serveru, který se použil k vytvoření serveru. |
   | **Heslo** | Heslo účtu správce serveru | Heslo z účtu správce serveru, který se použil k vytvoření serveru. |
   ||||

   ![Připojení k serveru](./media/sql-database-connect-query-ssms/connect.png)  

3. V dialogovém okně **připojit k serveru** vyberte **Možnosti** . V rozevírací nabídce **připojit k databázi** vyberte **mySampleDatabase**. Po dokončení rychlého startu v [oddílu předpoklady](#prerequisites) se vytvoří databáze AdventureWorksLT s názvem mySampleDatabase. Pokud vaše pracovní kopie databáze AdventureWorks má jiný název než mySampleDatabase, pak ji místo toho vyberte.

   ![připojení k databázi na serveru](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. Vyberte **Connect** (Připojit). Otevře se okno Průzkumník objektů.

5. Chcete-li zobrazit objekty databáze, rozbalte položku **databáze** a poté rozbalte uzel databáze.

   ![objekty mySampleDatabase](./media/sql-database-connect-query-ssms/connected.png)  

## <a name="query-data"></a>Dotazování dat

Spuštěním tohoto příkazu [Vyberte](https://msdn.microsoft.com/library/ms189499.aspx) kód Transact-SQL pro dotazování na prvních 20 produktů podle kategorie.

1. V Průzkumník objektů klikněte pravým tlačítkem na **mySampleDatabase** a vyberte **Nový dotaz**. Otevře se nové okno dotazu připojené k vaší databázi.

2. Do okna dotazu vložte následující dotaz SQL:

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

3. Na panelu nástrojů vyberte **provést** a spusťte dotaz a načtěte data z tabulek `Product` a. `ProductCategory`

    ![dotaz pro načtení dat z tabulkového produktu a ProductCategory](./media/sql-database-connect-query-ssms/query2.png)

### <a name="insert-data"></a>Vložení dat

Spuštěním tohoto příkazu [vložte](https://msdn.microsoft.com/library/ms174335.aspx) kód Transact-SQL k vytvoření nového produktu v `SalesLT.Product` tabulce.

1. Nahraďte předchozí dotaz tímto.

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

2. Vyberte **Spustit** pro vložení nového řádku do `Product` tabulky. Zobrazí **Messages** se podokno zprávy **(počet ovlivněných řádků: 1)**.

#### <a name="view-the-result"></a>Zobrazit výsledek

1. Nahraďte předchozí dotaz tímto.

   ```sql
   SELECT * FROM [SalesLT].[Product]
   WHERE Name='myNewProduct'
   ```

2. Vyberte **Provést**. Zobrazí se následující výsledek.

   ![výsledek dotazu na tabulku produktu](./media/sql-database-connect-query-ssms/result.png)

### <a name="update-data"></a>Aktualizace dat

Spusťte tento [kód](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL pro úpravu nového produktu.

1. Nahraďte předchozí dotaz tímto výsledkem, který vrátí nový záznam, který jste vytvořili dříve:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Vyberte **provést** a aktualizujte zadaný řádek v `Product` tabulce. Zobrazí **Messages** se podokno zprávy **(počet ovlivněných řádků: 1)**.

### <a name="delete-data"></a>Odstranění dat

Spuštěním tohoto příkazu [odstraňte](https://msdn.microsoft.com/library/ms189835.aspx) kód Transact-SQL, abyste mohli nový produkt odebrat.

1. Nahraďte předchozí dotaz tímto.

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Zvolením příkazu **Spustit** odstraňte zadaný řádek v `Product` tabulce. Zobrazí **Messages** se podokno zprávy **(počet ovlivněných řádků: 1)**.

## <a name="next-steps"></a>Další kroky

- Informace o SSMS naleznete v tématu [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx).
- Informace o připojení a dotazování pomocí Azure Portal najdete v tématu [připojení a dotazování pomocí Editoru dotazů SQL Azure Portal](sql-database-connect-query-portal.md).
- Informace o připojení a dotazování pomocí Visual Studio Code najdete v tématu [Připojení a dotazování pomocí Visual Studio Code](sql-database-connect-query-vscode.md).
- Informace o připojení a dotazování pomocí .NET najdete v tématu [Připojení a dotazování pomocí .NET](sql-database-connect-query-dotnet.md).
- Informace o připojení a dotazování pomocí PHP najdete v tématu [Připojení a dotazování pomocí PHP](sql-database-connect-query-php.md).
- Informace o připojení a dotazování pomocí Node.js najdete v tématu [Připojení a dotazování pomocí Node.js](sql-database-connect-query-nodejs.md).
- Informace o připojení a dotazování pomocí Javy najdete v tématu [Připojení a dotazování pomocí Javy](sql-database-connect-query-java.md).
- Informace o připojení a dotazování pomocí Pythonu najdete v tématu [Připojení a dotazování pomocí Pythonu](sql-database-connect-query-python.md).
- Informace o připojení a dotazování pomocí Ruby najdete v tématu [Připojení a dotazování pomocí Ruby](sql-database-connect-query-ruby.md).
