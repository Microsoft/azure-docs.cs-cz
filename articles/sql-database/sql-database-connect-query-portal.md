---
title: Dotazování SQL Database pomocí Editoru dotazů v Azure Portal
description: Naučte se používat Editor dotazů ke spouštění dotazů jazyka Transact-SQL (T-SQL) na Azure SQL Database.
keywords: připojení k SQL Database, dotazování SQL Database, Azure Portal, Portal, Editor dotazů
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: Ninarn
ms.author: ninarn
ms.reviewer: carlrab
ms.date: 03/12/2020
ms.openlocfilehash: c072161db7a477b7973571a18d4f686b1b9c6202
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "80985673"
---
# <a name="quickstart-use-the-azure-portals-query-editor-to-query-a-sql-database"></a>Rychlý Start: použití Editoru dotazů Azure Portal k dotazování databáze SQL

Editor dotazů je nástroj v Azure Portal pro spouštění dotazů SQL pro Azure SQL Database nebo Azure SQL Data Warehouse. 

V tomto rychlém startu použijete Editor dotazů ke spouštění dotazů jazyka Transact-SQL (T-SQL) v databázi SQL Azure.


## <a name="prerequisites"></a>Požadavky

Dokončení tohoto rychlého startu vyžaduje ukázkovou databázi AdventureWorksLT. Pokud nemáte funkční kopii databáze AdventureWorksLT SQL, následující rychlý Start ho rychle vytvoří:

- [Rychlý Start: vytvoření jedné databáze SQL Azure pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku Azure](sql-database-single-database-get-started.md) 

### <a name="configure-network-settings"></a>Konfigurace nastavení sítě

Pokud se v editoru dotazů zobrazí jedna z následujících chyb: *vaše nastavení místní sítě může zabránit tomu, aby Editor dotazů vydával dotazy. Kliknutím sem zobrazíte pokyny ke konfiguraci nastavení sítě*nebo nelze *navázat připojení k serveru. To může znamenat problém s konfigurací místní brány firewall nebo nastavením síťového proxy serveru*. tyto důležité informace by se měly lépe vyřešit:

> [!IMPORTANT]
> Editor dotazů používá ke komunikaci porty 443 a 1443. Ujistěte se, že jste na těchto portech povolili odchozí přenosy HTTPS. Pro přístup k databázím a datovým skladům je také potřeba [Přidat odchozí IP adresu k povoleným pravidlům brány firewall serveru](sql-database-server-level-firewall-rule.md) .


## <a name="open-the-sql-database-query-editor"></a>Otevřete Editor dotazů SQL Database

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) a vyberte databázi SQL, kterou chcete dotazovat.

2. V nabídce **databáze SQL** vyberte **Editor dotazů (Preview)**.

    ![vyhledání editoru dotazů](./media/sql-database-connect-query-portal/find-query-editor.PNG)


## <a name="establish-a-connection-to-the-database"></a>Navázat připojení k databázi

I když jste se přihlásili k portálu, stále musíte zadat přihlašovací údaje pro přístup k databázi SQL. K připojení k databázi se můžete připojit pomocí ověřování SQL nebo Azure Active Directory.

### <a name="connect-using-sql-authentication"></a>Připojení s využitím ověřování SQL

1. Na **přihlašovací** stránce v části **ověřování systému SQL Server**zadejte **přihlašovací jméno** a **heslo** uživatele, který má přístup k databázi. Pokud si nejste jistí, použijte přihlašovací jméno a heslo pro správce serveru databáze.

    ![přihlášení](./media/sql-database-connect-query-portal/login-menu.png)

2. Vyberte **OK**.


### <a name="connect-using-azure-active-directory"></a>Připojit pomocí Azure Active Directory

Konfigurace správce Azure Active Directory (Azure AD) umožňuje použít jedinou identitu pro přihlášení k Azure Portal a vaší databázi SQL. Pokud se chcete připojit k databázi pomocí Azure AD, postupujte podle následujících kroků a nakonfigurujte správce Azure AD pro SQL Server.

> [!NOTE]
> * E-mailové účty (například outlook.com, gmail.com, yahoo.com atd.) se zatím nepodporují jako správci služby Azure AD. Nezapomeňte zvolit uživatele vytvořeného nativně ve službě Azure AD nebo federovaného do Azure AD.
> * Přihlášení správce Azure AD nefunguje s účty, které mají povolené 2-Factor Authentication.

#### <a name="set-an-active-directory-admin-for-the-database-server"></a>Nastavení správce služby Active Directory pro databázový server

1. V Azure Portal vyberte svůj SQL Server.

2. V nabídce **SQL Server** vyberte **Správce služby Active Directory**.

3. Na panelu nástrojů stránky **Správce služby Active Directory** SQL serveru vyberte **nastavit správce** a zvolte uživatele nebo skupinu jako správce Azure AD.

    ![Výběr Active Directory](./media/sql-database-connect-query-portal/select-active-directory.png)

4. Na stránce **přidat správce** zadejte do vyhledávacího pole uživatele nebo skupinu, které chcete najít, vyberte je jako správce a pak klikněte na tlačítko **Vybrat** .

5. Zpátky na panelu nástrojů stránky **Správce služby Active Directory** SQL serveru vyberte **Uložit**.

### <a name="connect-to-the-database"></a>Připojte se k databázi.

6. V nabídce **SQL Server** vyberte **databáze SQL**a pak vyberte svou databázi SQL.

7. V nabídce **databáze SQL** vyberte **Editor dotazů (Preview)**. Na **přihlašovací** stránce pod popiskem **ověřování služby Active Directory** se zobrazí zpráva oznamující, že jste přihlášení, pokud jste správcem Azure AD. Pak vyberte tlačítko **pokračovat jako** * \<uživatel nebo ID skupiny>* . Pokud stránka indikuje, že jste se neúspěšně přihlásili, možná budete muset stránku aktualizovat.

## <a name="query-a-sql-database"></a>Dotazování databáze SQL

Následující příklady dotazů by měly být úspěšně spuštěny proti ukázkové databázi AdventureWorksLT.

### <a name="run-a-select-query"></a>Spustit výběrový dotaz

1. Do editoru dotazů vložte následující dotaz:

   ```sql
    SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
    FROM SalesLT.ProductCategory pc
    JOIN SalesLT.Product p
    ON pc.productcategoryid = p.productcategoryid;
   ```

2. Vyberte **Spustit** a potom zkontrolujte výstup v podokně **výsledků** .

   ![výsledky editoru dotazů](./media/sql-database-connect-query-portal/query-editor-results.png)

3. V případě potřeby můžete dotaz uložit jako soubor. SQL nebo exportovat vracená data jako soubor. JSON,. csv nebo. XML.

### <a name="run-an-insert-query"></a>Spuštění dotazu pro vložení

Chcete-li přidat nový produkt v `SalesLT.Product` tabulce, spusťte následující příkaz T-SQL. [INSERT](/sql/t-sql/statements/insert-transact-sql/)

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


2. Vyberte možnost **Spustit** pro vložení nového řádku do `Product` tabulky. V podokně **zprávy** se zobrazí **dotaz uspěl: ovlivněné řádky: 1**.


### <a name="run-an-update-query"></a>Spustit aktualizační dotaz

Pokud chcete upravit nový produkt, spusťte následující příkaz [Update](/sql/t-sql/queries/update-transact-sql/) jazyka T-SQL.

1. Nahraďte předchozí dotaz tímto.

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Vyberte možnost **Spustit** a aktualizujte zadaný řádek v `Product` tabulce. V podokně **zprávy** se zobrazí **dotaz uspěl: ovlivněné řádky: 1**.

### <a name="run-a-delete-query"></a>Spustit odstraňovací dotaz

Spuštěním následujícího příkazu [Delete](/sql/t-sql/statements/delete-transact-sql/) jazyka T-SQL odeberte nový produkt.

1. Nahraďte předchozí dotaz jedním z těchto:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Vyberte možnost **Spustit** a odstraňte zadaný řádek v `Product` tabulce. V podokně **zprávy** se zobrazí **dotaz uspěl: ovlivněné řádky: 1**.


## <a name="query-editor-considerations"></a>Otázky editoru dotazů

Při práci s editorem dotazů se dozvíte několik věcí.

* Editor dotazů používá ke komunikaci porty 443 a 1443. Ujistěte se, že jste na těchto portech povolili odchozí přenosy HTTPS. Pro přístup k databázím a datovým skladům budete taky muset přidat odchozí IP adresu k povoleným pravidlům brány firewall serveru.

* Pokud máte připojení privátního propojení, Editor dotazů funguje bez nutnosti přidat IP adresu klienta do brány SQL Database firewall.

* Stisknutím **F5** aktualizujete stránku editoru dotazů a přijdete o všechny dotazy, které jsou právě zpracovávány.

* Editor dotazů nepodporuje připojení k `master` databázi.

* Pro provádění dotazů existuje časový limit 5 minut.

* Editor dotazů podporuje pouze válcovou projekci pro geografické datové typy.

* Pro tabulky a zobrazení databáze není k dispozici podpora technologie IntelliSense, ale editor podporuje automatické dokončování u názvů, které již byly zadány.




## <a name="next-steps"></a>Další kroky

Další informace o jazyce Transact-SQL (T-SQL) podporovaném v databázích Azure SQL najdete v tématu [řešení rozdílů v jazyce Transact-SQL během migrace na SQL Database](sql-database-transact-sql-information.md).
