---
title: 'Azure Portal: Dotazování Azure SQL Database pomocí Editoru dotazů | Microsoft Docs'
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
ms.date: 06/28/2019
ms.openlocfilehash: 3702c88d0a5cdc7aa1f854f71e3aee8a42d9c22c
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569174"
---
# <a name="quickstart-use-the-azure-portals-sql-query-editor-to-connect-and-query-data"></a>Rychlý start: Připojení a dotazování dat pomocí Editoru dotazů SQL Azure Portal

Editor dotazů SQL je nástroj Prohlížeč Azure portal poskytuje snadný způsob, jak spouštět dotazy SQL v Azure SQL Database nebo Azure SQL Data Warehouse. V tomto rychlém startu použijete Editor dotazů pro připojení k databázi SQL a potom spustíte příkazy jazyka Transact-SQL k dotazování, vkládání, aktualizaci a odstraňování dat.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

- Databázi Azure SQL. K vytvoření a konfiguraci databáze v Azure SQL Database můžete použít jeden z těchto rychlých startů:

  || Izolovaná databáze |
  |:--- |:--- |
  | Create| [Azure Portal](sql-database-single-database-get-started.md) |
  || [Rozhraní příkazového řádku](scripts/sql-database-create-and-configure-database-cli.md) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) |
  | Konfigurace | [Pravidlo brány firewall protokolu IP na úrovni serveru](sql-database-server-level-firewall-rule.md)|
  |||

> [!NOTE]
> Editor dotazů používá ke komunikaci porty 443 a 1443.  Ujistěte se prosím, že jste na těchto portech povolili odchozí přenosy HTTPS. Pro přístup k databázím a datovým skladům budete taky muset přidat odchozí IP adresu k povoleným pravidlům brány firewall serveru.

## <a name="sign-in-the-azure-portal"></a>Přihlaste se na webu Azure portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="connect-using-sql-authentication"></a>Připojení pomocí ověřování SQL

1. Vyberte **databází SQL** z nabídky na levé straně a pak vyberte **mySampleDatabase**.

2. V nabídce vlevo najděte a vyberte **editor dotazů (preview)** . **Přihlášení** se zobrazí stránka.

    ![vyhledání editoru dotazů](./media/sql-database-connect-query-portal/find-query-editor.PNG)

3. Z **typ autorizace** rozevírací nabídky vyberte **ověřování serveru SQL Server** a zadejte ID uživatele a heslo účtu správce serveru, který se používá k vytvoření databáze.

    ![Přihlášení](./media/sql-database-connect-query-portal/login-menu.png)

4. Vyberte **OK**.


## <a name="connect-using-azure-active-directory"></a>Připojte se pomocí Azure Active Directory

Konfigurace správce Active Directory (AD) umožňuje používat jednu identitu pro přihlášení k webu Azure portal a SQL database. Podle následujících pokynů ke konfiguraci správce AD pro SQL server.

> [!NOTE]
> * Jako správci AD zatím nepodporují e-mailové účty (například outlook.com, gmail.com, yahoo.com a tak dále). Nezapomeňte vybrat uživatele vytvořené nativně v Azure AD nebo Federovaná do služby Azure AD.
> * Přihlášení správce Azure AD nefunguje s účty, které mají 2-factor authentication povolena.

1. Vyberte **všechny prostředky** z nabídky na levé straně a pak vyberte váš server SQL.

2. V nabídce **Nastavení** serveru SQL vyberte možnost **Správce služby Active Directory**.

3. Vyberte z panelu nástrojů stránky Správce AD **nastavit správce** a zvolte uživatele nebo skupiny jako správce AD.

    ![Výběr Active Directory](./media/sql-database-connect-query-portal/select-active-directory.png)

4. Vyberte z panelu nástrojů stránky Správce AD **Uložit**.

5. Přejděte **mySampleDatabase** databáze a v nabídce vlevo vyberte **editor dotazů (preview)** . **Přihlášení** se zobrazí stránka. Pokud jste správce služby AD, zobrazí se na pravé straně v části **jednotné přihlašování služby Active Directory**zpráva oznamující, že jste se přihlásili.

6. Vyberte **OK**.


## <a name="view-data"></a>Zobrazení dat

1. Poté, co je ověřen, vložte následující příkaz SQL v editoru dotazů pro získání nejlepších 20 produktů podle kategorie.

   ```sql
    SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
    FROM SalesLT.ProductCategory pc
    JOIN SalesLT.Product p
    ON pc.productcategoryid = p.productcategoryid;
   ```

2. Na panelu nástrojů vyberte **spustit** a pak zkontrolujte výstup v **výsledky** podokně.

![výsledky editoru dotazů](./media/sql-database-connect-query-portal/query-editor-results.png)

## <a name="insert-data"></a>Vložení dat

Chcete-li přidat nový produkt v `SalesLT.Product` tabulce, spusťte následující příkaz jazyka Transact-SQL. [](https://msdn.microsoft.com/library/ms174335.aspx)

1. Nahraďte předchozí dotaz s touto položkou.

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


2. Vyberte možnost **Spustit** pro vložení nového řádku do `Product` tabulky. V podokně **zprávy** se **zobrazí dotaz úspěšně: Ovlivněné řádky: 1**.


## <a name="update-data"></a>Aktualizace dat

Spusťte následující příkaz Transact-SQL [Update](https://msdn.microsoft.com/library/ms177523.aspx) pro úpravu nového produktu.

1. Nahraďte předchozí dotaz s touto položkou.

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Vyberte možnost **Spustit** a aktualizujte zadaný řádek v `Product` tabulce. V podokně **zprávy** se **zobrazí dotaz úspěšně: Ovlivněné řádky: 1**.

## <a name="delete-data"></a>Odstranění dat

Pokud chcete nový produkt odebrat, spusťte následující příkaz Transact-SQL [Delete](https://msdn.microsoft.com/library/ms189835.aspx) .

1. Nahraďte předchozí dotaz s touto položkou:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Vyberte možnost **Spustit** a odstraňte zadaný řádek v `Product` tabulce. V podokně **zprávy** se **zobrazí dotaz úspěšně: Ovlivněné řádky: 1**.


## <a name="query-editor-considerations"></a>Požadavky editoru dotazů

Existuje několik věcí, které při práci s editorem dotazů.

* Editor dotazů používá ke komunikaci porty 443 a 1443.  Ujistěte se prosím, že jste na těchto portech povolili odchozí přenosy HTTPS. Pro přístup k databázím a datovým skladům budete taky muset přidat odchozí IP adresu k povoleným pravidlům brány firewall serveru.

* Stisknutím klávesy F5 aktualizuje stránku editoru dotazů a dojde ke ztrátě všech dotazů se pracuje.

* Editor dotazů nepodporuje připojení k `master` databázi.

* Časový limit 5 minut pro provádění dotazů není k dispozici.

* Editor dotazů podporuje pouze válcové geografických datových typů.

* Není dostupná podpora technologie IntelliSense pro databázové tabulky a zobrazení. Editor ale podporuje automatické dokončování v názvy, které jste už zadali.


## <a name="next-steps"></a>Další postup

Další informace o jazyku Transact-SQL podporované v Azure SQL Database najdete v tématu [rozdíly řešení příkazů jazyka Transact-SQL během migrace do služby SQL Database](sql-database-transact-sql-information.md).
