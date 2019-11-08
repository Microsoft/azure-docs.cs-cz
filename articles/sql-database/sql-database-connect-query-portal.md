---
title: 'Azure Portal: dotaz pomocí Editoru dotazů'
description: Zjistěte, jak se připojit k SQL Database na webu Azure Portal pomocí editoru dotazů SQL. Potom spustíte příkazy jazyka Transact-SQL (T-SQL) k dotazování a úpravě dat.
keywords: connect to sql database,azure portal, portal, query editor
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: Ninarn
ms.author: ninarn
ms.reviewer: carlrab
ms.date: 10/24/2019
ms.openlocfilehash: 3990d7ec63c312d38168fe76269e1a920f1a6817
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827118"
---
# <a name="quickstart-use-the-azure-portals-sql-query-editor-to-connect-and-query-data"></a>Rychlý Start: použití Editoru dotazů SQL Azure Portal k připojení a dotazování dat

Editor dotazů SQL je Azure Portal Nástroj pro prohlížeč, který poskytuje snadný způsob, jak spouštět dotazy SQL na Azure SQL Database nebo Azure SQL Data Warehouse. V tomto rychlém startu použijete Editor dotazů pro připojení k databázi SQL a potom spustíte příkazy jazyka Transact-SQL k dotazování, vkládání, aktualizaci a odstraňování dat.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

- Databázi Azure SQL. K vytvoření a konfiguraci databáze v Azure SQL Database můžete použít jeden z těchto rychlých startů:

  || Izolovaná databáze |
  |:--- |:--- |
  | Vytvoření| [Azure Portal](sql-database-single-database-get-started.md) |
  || [Rozhraní příkazového řádku](scripts/sql-database-create-and-configure-database-cli.md) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) |
  | Konfigurace | [Pravidlo brány firewall protokolu IP na úrovni serveru](sql-database-server-level-firewall-rule.md)|
  |||

> [!NOTE]
> Editor dotazů používá ke komunikaci porty 443 a 1443.  Ujistěte se prosím, že jste na těchto portech povolili odchozí přenosy HTTPS. Pro přístup k databázím a datovým skladům budete taky muset přidat odchozí IP adresu k povoleným pravidlům brány firewall serveru.

## <a name="sign-in-the-azure-portal"></a>Přihlášení Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="connect-using-sql-authentication"></a>Připojení pomocí ověřování SQL

1. Pokud se chcete připojit k databázi SQL, otevřete Azure Portal. Vyhledejte a vyberte **databáze SQL**.

    ![Přejděte do seznamu SQL Database, Azure Portal](./media/sql-database-connect-query-portal/search-for-sql-databases.png)

2. Vyberte svou databázi SQL.

    ![Vyberte databázi SQL Azure Portal](./media/sql-database-connect-query-portal/select-a-sql-database.png)

3. V nabídce **databáze SQL** vyberte **Editor dotazů (Preview)** .

    ![vyhledání editoru dotazů](./media/sql-database-connect-query-portal/find-query-editor.PNG)

4. Na **přihlašovací** stránce v popisku **ověřování systému SQL Server** zadejte **přihlašovací** ID a **heslo** účtu správce serveru, který jste použili k vytvoření databáze. Pak vyberte **OK**.

    ![přihlášení](./media/sql-database-connect-query-portal/login-menu.png)

## <a name="connect-using-azure-active-directory"></a>Připojit pomocí Azure Active Directory

Konfigurace správce Azure Active Directory (Azure AD) umožňuje použít jedinou identitu pro přihlášení k Azure Portal a vaší databázi SQL. Použijte následující postup ke konfiguraci správce Azure AD pro SQL Server.

> [!NOTE]
> * E-mailové účty (například outlook.com, gmail.com, yahoo.com atd.) se zatím nepodporují jako správci služby Azure AD. Nezapomeňte zvolit uživatele vytvořeného nativně ve službě Azure AD nebo federovaného do Azure AD.
> * Přihlášení správce Azure AD nefunguje s účty, které mají povolené 2-Factor Authentication.

1. V nabídce Azure Portal nebo na **domovské** stránce vyberte **všechny prostředky**.

2. Vyberte svůj SQL Server.

3. V nabídce **SQL Server** v části **Nastavení**vyberte **Správce služby Active Directory**.

4. Na panelu nástrojů stránky **Správce služby Active Directory** SQL serveru vyberte **nastavit správce** a zvolte uživatele nebo skupinu jako správce Azure AD.

    ![Výběr Active Directory](./media/sql-database-connect-query-portal/select-active-directory.png)

5. Na stránce **přidat správce** zadejte do vyhledávacího pole uživatele nebo skupinu, které chcete najít, vyberte je jako správce a pak klikněte na tlačítko **Vybrat** .

6. Zpátky na panelu nástrojů stránky **Správce služby Active Directory** SQL serveru vyberte **Uložit**.

7. V nabídce **SQL Server** vyberte **databáze SQL**a pak vyberte svou databázi SQL.

8. V nabídce **databáze SQL** vyberte **Editor dotazů (Preview)** . Na **přihlašovací** stránce pod popiskem **ověřování služby Active Directory** se zobrazí zpráva oznamující, že jste přihlášení, pokud jste správcem Azure AD. Pak vyberte možnost **pokračovat jako** *\<ID uživatele nebo skupiny >* tlačítko.

## <a name="view-data"></a>Zobrazení dat

1. Po ověření vložte následující příkaz SQL do editoru dotazů, abyste načetli prvních 20 produktů podle kategorie.

   ```sql
    SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
    FROM SalesLT.ProductCategory pc
    JOIN SalesLT.Product p
    ON pc.productcategoryid = p.productcategoryid;
   ```

2. Na panelu nástrojů vyberte **Spustit** a potom zkontrolujte výstup v podokně **výsledků** .

   ![výsledky editoru dotazů](./media/sql-database-connect-query-portal/query-editor-results.png)

## <a name="insert-data"></a>Vložení dat

Chcete-li přidat nový produkt v tabulce `SalesLT.Product`, [Spusťte následující příkaz](https://msdn.microsoft.com/library/ms174335.aspx) jazyka Transact-SQL.

1. Nahraďte předchozí dotaz tímto.

    ```sql
    INSERT INTO [SalesLT].[Product]
           ( [Name]
           , [ProductNumber]
           , [Color]
           , [ProductCategoryID]
           , [StandardCost]
           , [ListPrice]
           , [SellStartDate]
           )
    VALUES
           ('myNewProduct'
           ,123456789
           ,'NewColor'
           ,1
           ,100
           ,100
           ,GETDATE() );
   ```


2. Vyberte možnost **Spustit** pro vložení nového řádku do tabulky `Product`. V podokně **zprávy** se zobrazí **dotaz uspěl: ovlivněné řádky: 1**.


## <a name="update-data"></a>Aktualizace dat

Spusťte následující příkaz Transact-SQL [Update](https://msdn.microsoft.com/library/ms177523.aspx) pro úpravu nového produktu.

1. Nahraďte předchozí dotaz tímto.

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Vyberte možnost **Spustit** a aktualizujte zadaný řádek v `Product` tabulce. V podokně **zprávy** se zobrazí **dotaz uspěl: ovlivněné řádky: 1**.

## <a name="delete-data"></a>Odstranění dat

Pokud chcete nový produkt odebrat, spusťte následující příkaz Transact-SQL [Delete](https://msdn.microsoft.com/library/ms189835.aspx) .

1. Nahraďte předchozí dotaz jedním z těchto:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Vyberte možnost **Spustit** a odstraňte zadaný řádek v `Product` tabulce. V podokně **zprávy** se zobrazí **dotaz uspěl: ovlivněné řádky: 1**.


## <a name="query-editor-considerations"></a>Otázky editoru dotazů

Při práci s editorem dotazů se dozvíte několik věcí.

* Editor dotazů používá ke komunikaci porty 443 a 1443.  Ujistěte se prosím, že jste na těchto portech povolili odchozí přenosy HTTPS. Pro přístup k databázím a datovým skladům budete taky muset přidat odchozí IP adresu k povoleným pravidlům brány firewall serveru.

* Stisknutím F5 aktualizujete stránku editoru dotazů a přijdete o všechny dotazy, které jsou právě zpracovávány.

* Editor dotazů nepodporuje připojení k `master` databázi.

* Pro provádění dotazů existuje časový limit 5 minut.

* Editor dotazů podporuje pouze válcovou projekci pro geografické datové typy.

* Pro databázové tabulky a zobrazení není k dispozici žádná podpora technologie IntelliSense. Editor však podporuje automatické dokončování u názvů, které již byly zadány.


## <a name="next-steps"></a>Další kroky

Další informace o podporovaných jazycích Transact-SQL v databázích SQL Azure najdete v tématu [řešení rozdílů v jazyce Transact-SQL během migrace na SQL Database](sql-database-transact-sql-information.md).
