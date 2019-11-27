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
ms.date: 03/25/2019
ms.openlocfilehash: ed33d50da84347f55d355802e7767c8477c30e87
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482157"
---
# <a name="quickstart-use-sql-server-management-studio-to-connect-and-query-an-azure-sql-database"></a>Rychlý start: Použití SQL Server Management Studio k připojení a dotazování Azure SQL database

V tomto rychlém startu použijete [SQL Server Management Studio][ssms-install-latest-84g] (SSMS) pro připojení k databázi SQL Azure. Potom spustíte příkazy jazyka Transact-SQL k dotazování, vkládání, aktualizaci a odstraňování dat. SSMS můžete použít ke správě jakékoli infrastruktury SQL, od SQL Server k SQL Database pro Microsoft Windows.  

## <a name="prerequisites"></a>Požadavky

Databázi Azure SQL. K vytvoření a konfiguraci databáze v Azure SQL Database můžete použít jeden z těchto rychlých startů:

  || Izolovaná databáze | Spravovaná instance |
  |:--- |:--- |:---|
  | Vytvoření| [Azure Portal](sql-database-single-database-get-started.md) | [Azure Portal](sql-database-managed-instance-get-started.md) |
  || [Rozhraní příkazového řádku](scripts/sql-database-create-and-configure-database-cli.md) | [Rozhraní příkazového řádku](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Konfigurace | [Pravidlo brány firewall protokolu IP na úrovni serveru](sql-database-server-level-firewall-rule.md)| [Připojení z virtuálního počítače](sql-database-managed-instance-configure-vm.md)|
  |||[Připojení z webu](sql-database-managed-instance-configure-p2s.md)
  |Načtení dat|Načtený Adventure Works pro každý rychlý Start|[Obnovení celosvětových dovozců](sql-database-managed-instance-get-started-restore.md)
  |||Obnovení nebo import Adventure Works ze souboru [BacPac](sql-database-import.md) z [GitHubu](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|

  > [!IMPORTANT]
  > Skripty v tomto článku jsou určeny k používání databáze Adventure Works. Se spravovanou instancí musíte buď importovat databázi Adventure Works do databáze instance, nebo upravit skripty v tomto článku, aby používaly databázi World Importers.

## <a name="install-the-latest-ssms"></a>Instalace nejnovější verze aplikace SSMS

Než začnete, ujistěte se, že máte nainstalovanou nejnovější verzi [SSMS][ssms-install-latest-84g].

## <a name="get-sql-server-connection-information"></a>Získat informace o připojení k SQL serveru

Získejte informace o připojení, které potřebujete pro připojení ke službě Azure SQL Database. Pro nadcházející postupy budete potřebovat plně kvalifikovaný název serveru nebo název hostitele, název databáze a přihlašovací údaje.

1. Přihlaste se na web [Azure Portal ](https://portal.azure.com/).

2. Přejděte na stránku **databáze SQL** nebo **spravované instance SQL** .

3. Na stránce **Přehled** zkontrolujte plně kvalifikovaný název serveru vedle **názvu serveru** pro izolovanou databázi nebo plně kvalifikovaný název serveru vedle možnost **hostitel** pro spravovanou instanci. Pokud chcete zkopírovat název serveru nebo název hostitele, najeďte na něj ukazatelem myši a vyberte ikonu **kopírování** .

## <a name="connect-to-your-database"></a>Připojení k databázi

V SMSS připojení k vašemu serveru Azure SQL Database.

> [!IMPORTANT]
> Azure SQL Database Server naslouchá na portu 1433. Aby bylo možné připojit se k serveru SQL Database za podnikovou bránou firewall, musí mít brána firewall otevřený tento port.
>

1. Otevřete aplikaci SSMS. Zobrazí se dialogové okno **Připojení k serveru**.

2. Zadejte následující informace:

   | Nastavení      | Navrhovaná hodnota    | Popis |
   | ------------ | ------------------ | ----------- |
   | **Typ serveru** | Databázový stroj | Povinná hodnota. |
   | **Název serveru** | Plně kvalifikovaný název serveru | Něco jako: **mynewserver20170313.Database.Windows.NET**. |
   | **Ověřování** | Ověřování SQL Serveru | Tento kurz používá ověřování SQL. |
   | **Přihlášení** | ID uživatele účtu správce serveru | ID uživatele z účtu správce serveru, který se používá k vytvoření serveru. |
   | **Heslo** | Heslo účtu správce serveru | Heslo účtu správce serveru, který se používá k vytvoření serveru. |
   ||||

   ![Připojení k serveru](./media/sql-database-connect-query-ssms/connect.png)  

3. V dialogovém okně **připojit k serveru** vyberte **Možnosti** . V rozevírací nabídce **připojit k databázi** vyberte **mySampleDatabase**. Pokud necháte rozevírací nabídku nastavenou na výchozí hodnotu, připojení se provede v **Hlavní** databázi.

   ![připojení k databázi na serveru](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. Vyberte **Connect** (Připojit). Otevře se okno Průzkumníka objektů.

5. Chcete-li zobrazit objekty databáze, rozbalte položku **databáze** a potom rozbalte **mySampleDatabase**.

   ![objekty mySampleDatabase](./media/sql-database-connect-query-ssms/connected.png)  

## <a name="query-data"></a>Dotazování dat

Spuštěním tohoto příkazu [Vyberte](https://msdn.microsoft.com/library/ms189499.aspx) kód Transact-SQL pro dotazování na prvních 20 produktů podle kategorie.

1. V Průzkumník objektů klikněte pravým tlačítkem na **mySampleDatabase** a vyberte **Nový dotaz**. Otevře se nové okno dotazu připojené k vaší databázi.

2. V okně dotazu vložte tento dotaz SQL.

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

3. Na panelu nástrojů vyberte **provést** a načtěte data z tabulek `Product` a `ProductCategory`.

    ![dotaz pro načtení dat z tabulkového produktu a ProductCategory](./media/sql-database-connect-query-ssms/query2.png)

## <a name="insert-data"></a>Vložení dat

Spuštěním tohoto příkazu [vložte](https://msdn.microsoft.com/library/ms174335.aspx) kód Transact-SQL a vytvořte nový produkt v tabulce `SalesLT.Product`.

1. Nahraďte předchozí dotaz s touto položkou.

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

2. Výběrem příkazu **Execute (spustit** ) vložte nový řádek do tabulky `Product`. Zobrazí se podokno zprávy **(počet ovlivněných řádků: 1)** .

## <a name="view-the-result"></a>Zobrazení výsledku

1. Nahraďte předchozí dotaz s touto položkou.

   ```sql
   SELECT * FROM [SalesLT].[Product]
   WHERE Name='myNewProduct'
   ```

2. Vyberte **Provést**. Zobrazí se následující výsledek.

   ![výsledek dotazu na tabulku produktu](./media/sql-database-connect-query-ssms/result.png)

## <a name="update-data"></a>Aktualizace dat

Spusťte tento [kód](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL pro úpravu nového produktu.

1. Nahraďte předchozí dotaz s touto položkou.

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Vyberte **provést** a aktualizujte zadaný řádek v `Product` tabulce. Zobrazí se podokno zprávy **(počet ovlivněných řádků: 1)** .

## <a name="delete-data"></a>Odstranění dat

Spuštěním tohoto příkazu [odstraňte](https://msdn.microsoft.com/library/ms189835.aspx) kód Transact-SQL, abyste mohli nový produkt odebrat.

1. Nahraďte předchozí dotaz s touto položkou.

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Výběrem příkazu **Spustit** odstraňte zadaný řádek v `Product` tabulce. Zobrazí se podokno zprávy **(počet ovlivněných řádků: 1)** .

## <a name="next-steps"></a>Další kroky

- Informace o SSMS naleznete v tématu [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx).
- Informace o připojení a dotazování pomocí webu Azure Portal najdete v tématu[Připojení a dotazování pomocí editoru dotazů SQL webu Azure Portal](sql-database-connect-query-portal.md).
- Informace o připojení a dotazování pomocí Visual Studio Code najdete v tématu [Připojení a dotazování pomocí Visual Studio Code](sql-database-connect-query-vscode.md).
- Informace o připojení a dotazování pomocí .NET najdete v tématu [Připojení a dotazování pomocí .NET](sql-database-connect-query-dotnet.md).
- Informace o připojení a dotazování pomocí PHP najdete v tématu [Připojení a dotazování pomocí PHP](sql-database-connect-query-php.md).
- Informace o připojení a dotazování pomocí Node.js najdete v tématu [Připojení a dotazování pomocí Node.js](sql-database-connect-query-nodejs.md).
- Informace o připojení a dotazování pomocí Javy najdete v tématu [Připojení a dotazování pomocí Javy](sql-database-connect-query-java.md).
- Informace o připojení a dotazování pomocí Pythonu najdete v tématu [Připojení a dotazování pomocí Pythonu](sql-database-connect-query-python.md).
- Informace o připojení a dotazování pomocí Ruby najdete v tématu [Připojení a dotazování pomocí Ruby](sql-database-connect-query-ruby.md).

<!-- Article link references. -->

[ssms-install-latest-84g]: https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms
