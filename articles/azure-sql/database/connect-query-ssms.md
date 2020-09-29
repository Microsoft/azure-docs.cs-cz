---
title: 'SSMS: připojení a dotazování dat'
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Přečtěte si, jak se připojit k Azure SQL Database nebo ke spravované instanci SQL pomocí SQL Server Management Studio (SSMS). Potom spustíte příkazy jazyka Transact-SQL (T-SQL) k dotazování a úpravě dat.
keywords: Připojení k SQL Database, SQL Server Management Studio
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/28/2020
ms.openlocfilehash: 35a637df85984bcfd20836bcd87aa5ecf1583170
ms.sourcegitcommit: a0c4499034c405ebc576e5e9ebd65084176e51e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91461134"
---
# <a name="quickstart-use-ssms-to-connect-to-and-query-azure-sql-database-or-azure-sql-managed-instance"></a>Rychlý Start: použití SSMS pro připojení k a dotazování Azure SQL Database nebo spravované instance Azure SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

V tomto rychlém startu se dozvíte, jak pomocí SQL Server Management Studio (SSMS) se připojit k Azure SQL Database nebo spravované instanci SQL Azure a spustit některé dotazy.

## <a name="prerequisites"></a>Požadavky

Dokončení tohoto rychlého startu vyžaduje následující položky:

- [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms/).

- Databáze v Azure SQL Database. K vytvoření a konfiguraci databáze v Azure SQL Database můžete použít jeden z těchto rychlých startů:

  | Akce | Databáze SQL | Spravovaná instance SQL | SQL Server na virtuálním počítači Azure |
  |:--- |:--- |:---|:---|
  | Vytvořit| [Azure Portal](single-database-create-quickstart.md) | [Azure Portal](../managed-instance/instance-create-quickstart.md) | [Azure Portal](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  || [Rozhraní příkazového řádku](scripts/create-and-configure-database-cli.md) | [Rozhraní příkazového řádku](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | Konfigurace | [Pravidlo brány firewall protokolu IP na úrovni serveru](firewall-create-server-level-portal-quickstart.md)| [Připojení z virtuálního počítače](../managed-instance/connect-vm-instance-configure.md)|
  |||[Připojení z webu](../managed-instance/point-to-site-p2s-configure.md) | [Připojení k SQL Serveru](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |Načtení dat|Načtený Adventure Works pro každý rychlý Start|[Obnovení celosvětových dovozců](../managed-instance/restore-sample-database-quickstart.md) | [Obnovení celosvětových dovozců](../managed-instance/restore-sample-database-quickstart.md) |
  |||Obnovení nebo import Adventure Works ze souboru [BacPac](database-import.md) z [GitHubu](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)| Obnovení nebo import Adventure Works ze souboru [BacPac](database-import.md) z [GitHubu](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Skripty v tomto článku jsou určeny k používání databáze Adventure Works. Se spravovanou instancí musíte buď importovat databázi Adventure Works do databáze instance, nebo upravit skripty v tomto článku, aby používaly databázi World Importers.

Pokud jednoduše chcete spustit některé dotazy ad-hoc bez instalace SSMS, přečtěte si [rychlý Start: použití Editoru dotazů Azure Portal k dotazování databáze v Azure SQL Database](connect-query-portal.md).

## <a name="get-server-connection-information"></a>Získat informace o připojení k serveru

Získejte informace o připojení, které potřebujete pro připojení k vaší databázi. K dokončení tohoto rychlého startu budete potřebovat plně kvalifikovaný název [serveru](logical-servers.md) nebo název hostitele, název databáze a přihlašovací informace.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

2. Přejděte do **databáze** nebo **spravované instance** , pro kterou chcete zadat dotaz.

3. Na stránce **Přehled** si přečtěte plně kvalifikovaný název serveru vedle **názvu serveru** pro vaši databázi v SQL Database nebo plně kvalifikovaného názvu serveru (nebo IP adresy) vedle **hostitele** vaší spravované instance ve spravované instanci SQL nebo ve vaší instanci SQL Server na vašem virtuálním počítači. Pokud chcete zkopírovat název serveru nebo název hostitele, najeďte na něj ukazatelem myši a vyberte ikonu **kopírování** .

> [!NOTE]
> Informace o připojení pro SQL Server na virtuálním počítači Azure najdete v tématu [připojení k SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server)

## <a name="connect-to-your-database"></a>Připojení k databázi

V SSMS se připojte k vašemu serveru.

> [!IMPORTANT]
> Server naslouchá na portu 1433. Aby bylo možné se připojit k serveru za podnikovou bránou firewall, musí mít brána firewall otevřený tento port.

1. Otevřete aplikaci SSMS.

2. Zobrazí se dialogové okno **Připojení k serveru**. Zadejte následující informace:

   | Nastavení      | Navrhovaná hodnota    | Popis |
   | ------------ | ------------------ | ----------- |
   | **Typ serveru** | Databázový stroj | Požadovaná hodnota. |
   | **Název serveru** | Plně kvalifikovaný název serveru | Něco jako: **servername.Database.Windows.NET**. |
   | **Ověřování** | Ověřování SQL Serveru | V tomto kurzu se používá ověřování SQL. |
   | **Přihlásit** | ID uživatele účtu správce serveru | ID uživatele z účtu správce serveru, který se použil k vytvoření serveru. |
   | **Heslo** | Heslo účtu správce serveru | Heslo z účtu správce serveru, který se použil k vytvoření serveru. |
   ||||

   ![Připojení k serveru](./media/connect-query-ssms/connect.png)  

> [!NOTE]
> V tomto kurzu se používá SQL Server ověřování.  Pokud se ale chcete připojit prostřednictvím Azure Active Directory s MFA, ujistěte se, že využíváte [SSMS 18,6 nebo novější](https://aka.ms/ssms). 

3. V dialogovém okně **připojit k serveru** vyberte **Možnosti** . V rozevírací nabídce **připojit k databázi** vyberte **mySampleDatabase**. Po dokončení rychlého startu v [oddílu předpoklady](#prerequisites) se vytvoří databáze AdventureWorksLT s názvem mySampleDatabase. Pokud vaše pracovní kopie databáze AdventureWorks má jiný název než mySampleDatabase, pak ji místo toho vyberte.

   ![připojení k databázi na serveru](./media/connect-query-ssms/options-connect-to-db.png)  

4. Vyberte **Connect** (Připojit). Otevře se okno Průzkumník objektů.

5. Pokud chcete zobrazit objekty databáze, rozbalte položku **Databases** (Databáze) a poté rozbalte uzel databáze.

   ![objekty mySampleDatabase](./media/connect-query-ssms/connected.png)  

## <a name="query-data"></a>Dotazování dat

Spuštěním tohoto příkazu [Vyberte](/sql/t-sql/queries/select-transact-sql/) kód Transact-SQL pro dotazování na prvních 20 produktů podle kategorie.

1. V Průzkumník objektů klikněte pravým tlačítkem na **mySampleDatabase** a vyberte **Nový dotaz**. Otevře se nové okno dotazu připojené k vaší databázi.

2. Do okna dotazu vložte následující dotaz SQL:

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

3. Na panelu nástrojů vyberte **provést** a spusťte dotaz a načtěte data z `Product` `ProductCategory` tabulek a.

    ![dotaz pro načtení dat z tabulkového produktu a ProductCategory](./media/connect-query-ssms/query2.png)

### <a name="insert-data"></a>Vložení dat

Spuštěním tohoto příkazu [vložte](/sql/t-sql/statements/insert-transact-sql/) kód Transact-SQL k vytvoření nového produktu v `SalesLT.Product` tabulce.

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

2. Vyberte **Spustit**  pro vložení nového řádku do `Product` tabulky. Zobrazí **Messages** se podokno zprávy **(počet ovlivněných řádků: 1)**.

#### <a name="view-the-result"></a>Zobrazit výsledek

1. Nahraďte předchozí dotaz tímto.

   ```sql
   SELECT * FROM [SalesLT].[Product]
   WHERE Name='myNewProduct'
   ```

2. Vyberte **Execute** (Provést). Zobrazí se následující výsledek.

   ![výsledek dotazu na tabulku produktu](./media/connect-query-ssms/result.png)

### <a name="update-data"></a>Aktualizace dat

Spusťte tento [kód](/sql/t-sql/queries/update-transact-sql) Transact-SQL pro úpravu nového produktu.

1. Nahraďte předchozí dotaz tímto výsledkem, který vrátí nový záznam, který jste vytvořili dříve:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Vyberte **provést** a aktualizujte zadaný řádek v `Product` tabulce. Zobrazí **Messages** se podokno zprávy **(počet ovlivněných řádků: 1)**.

### <a name="delete-data"></a>Odstranění dat

Spuštěním tohoto příkazu [odstraňte](/sql/t-sql/statements/delete-transact-sql/) kód Transact-SQL, abyste mohli nový produkt odebrat.

1. Nahraďte předchozí dotaz tímto.

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Zvolením příkazu **Spustit** odstraňte zadaný řádek v `Product` tabulce. Zobrazí **Messages** se podokno zprávy **(počet ovlivněných řádků: 1)**.

## <a name="next-steps"></a>Další kroky

- Informace o SSMS naleznete v tématu [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms/).
- Informace o připojení a dotazování pomocí Azure Portal najdete v tématu [připojení a dotazování pomocí Editoru dotazů SQL Azure Portal](connect-query-portal.md).
- Informace o připojení a dotazování pomocí Visual Studio Code najdete v tématu [Připojení a dotazování pomocí Visual Studio Code](connect-query-vscode.md).
- Informace o připojení a dotazování pomocí .NET najdete v tématu [Připojení a dotazování pomocí .NET](connect-query-dotnet-visual-studio.md).
- Informace o připojení a dotazování pomocí PHP najdete v tématu [Připojení a dotazování pomocí PHP](connect-query-php.md).
- Informace o připojení a dotazování pomocí Node.js najdete v tématu [Připojení a dotazování pomocí Node.js](connect-query-nodejs.md).
- Informace o připojení a dotazování pomocí Javy najdete v tématu [Připojení a dotazování pomocí Javy](connect-query-java.md).
- Informace o připojení a dotazování pomocí Pythonu najdete v tématu [Připojení a dotazování pomocí Pythonu](connect-query-python.md).
- Informace o připojení a dotazování pomocí Ruby najdete v tématu [Připojení a dotazování pomocí Ruby](connect-query-ruby.md).
