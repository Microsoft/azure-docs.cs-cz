---
title: Dotaz na databázi SQL pomocí editoru dotazů na webu Azure Portal
description: Zjistěte, jak pomocí Editoru dotazů spouštět dotazy Transact-SQL (T-SQL) v databázi Azure SQL.
keywords: připojení k databázi SQL,dotaz sql database, portál Azure Portal, portál, editor dotazů
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
ms.openlocfilehash: 5847ef3033d257faef4831785b8abd864d54e835
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79209604"
---
# <a name="quickstart-use-the-azure-portals-query-editor-to-query-a-sql-database"></a>Úvodní příručka: Pomocí editoru dotazů na portál Azure Portal můžete dotazovat na databázi SQL.

Editor dotazů je nástroj na webu Azure Portal pro spouštění dotazů SQL v databázi Azure SQL nebo datovém skladu Azure SQL. 

V tomto rychlém startu použijete editor dotazů ke spuštění dotazů Transact-SQL (T-SQL) v databázi Azure SQL.


## <a name="prerequisites"></a>Požadavky

Dokončení tohoto rychlého startu vyžaduje ukázkovou databázi AdventureWorksLT. Pokud nemáte pracovní kopii databáze AdventureWorksLT SQL, následující rychlý start rychle vytvoří:

- [Úvodní příručka: Vytvoření jedné databáze Azure SQL pomocí azure portálu, PowerShellu nebo Azure CLI](sql-database-single-database-get-started.md) 

### <a name="configure-network-settings"></a>Konfigurace nastavení sítě

Pokud se v editoru dotazů zobrazí jedna z následujících chyb: *Nastavení místní sítě může bránit Editoru dotazů ve vydávání dotazů. Chcete-li získat pokyny ke konfiguraci nastavení sítě , klepněte sem*nebo *nelze navázat připojení k serveru. To může znamenat problém s místní konfigurací brány firewall nebo s nastavením síťového serveru proxy*, následující důležité informace by měly pomoci vyřešit:

> [!IMPORTANT]
> Editor dotazů používá ke komunikaci porty 443 a 1443. Ujistěte se, že jste na těchto portech povolili odchozí přenoshttps. Pro přístup k databázím a datovým skladům je také nutné [přidat odchozí ADRESU IP do pravidel brány firewall povolené ho serveru.](sql-database-server-level-firewall-rule.md)


## <a name="open-the-sql-database-query-editor"></a>Otevření Editoru dotazů databáze SQL

1. Přihlaste se na [portál Azure a](https://portal.azure.com/) vyberte databázi SQL, na kterou se chcete dotazovat.

2. V nabídce **databáze SQL** vyberte **Editor dotazů (preview).**

    ![vyhledání editoru dotazů](./media/sql-database-connect-query-portal/find-query-editor.PNG)


## <a name="establish-a-connection-to-the-database"></a>Navázání připojení k databázi

I když jste přihlášeni k portálu, stále potřebujete zadat pověření pro přístup k databázi SQL. K databázi se můžete připojit pomocí ověřování SQL nebo služby Azure Active Directory.

### <a name="connect-using-sql-authentication"></a>Připojení s využitím ověřování SQL

1. Na stránce **Přihlášení** zadejte v části **ověřování serveru SQL**přihlašovací **jméno** a **heslo** pro uživatele, který má přístup k databázi. Pokud si nejste jisti, použijte přihlašovací jméno a heslo pro správce serveru databáze.

    ![přihlášení](./media/sql-database-connect-query-portal/login-menu.png)

2. Vyberte **OK**.


### <a name="connect-using-azure-active-directory"></a>Připojení pomocí služby Azure Active Directory

Konfigurace správce služby Azure Active Directory (Azure AD) umožňuje použít jednu identitu k přihlášení k portálu Azure a k databázi SQL. Chcete-li se připojit k databázi pomocí služby Azure AD, postupujte podle následujících kroků a nakonfigurujte správce Azure AD pro váš SQL server.

> [!NOTE]
> * E-mailové účty (například outlook.com, gmail.com, yahoo.com a tak dále) ještě nejsou podporovány jako správci Azure AD. Ujistěte se, že vybrat uživatele vytvořeného nativně ve službě Azure AD nebo federované do Azure AD.
> * Přihlášení správce Azure AD nefunguje s účty, které mají povoleno dvoufaktorové ověřování.

#### <a name="set-an-active-directory-admin-for-the-database-server"></a>Nastavení správce služby Active Directory pro databázový server

1. Na webu Azure Portal vyberte sql server.

2. V nabídce **serveru SQL** vyberte **položku Správce služby Active Directory**.

3. Na panelu nástrojů stránky **správce služby SQL** server Active Directory vyberte **Nastavit správce** a vyberte jako správce Azure AD uživatele nebo skupinu.

    ![Výběr Active Directory](./media/sql-database-connect-query-portal/select-active-directory.png)

4. Na stránce **Přidat správce** zadejte do vyhledávacího pole uživatele nebo skupinu, kterou chcete najít, vyberte ho jako správce a pak zvolte tlačítko **Vybrat.**

5. Na panelu nástrojů pro správce služby SQL Server **active directory** vyberte možnost **Uložit**.

### <a name="connect-to-the-database"></a>Připojte se k databázi.

6. V nabídce **serveru SQL** vyberte **databáze SQL**a pak vyberte databázi SQL.

7. V nabídce **databáze SQL** vyberte **Editor dotazů (preview).** Na stránce **Přihlášení** se pod popiskem **ověřování služby Active Directory** zobrazí zpráva, že jste přihlášení, pokud jste správcem Azure AD. Pak vyberte **tlačítko Pokračovat jako** * \<id uživatele nebo skupiny>.* Pokud stránka označuje, že jste se úspěšně nepřihlásili, bude pravděpodobně nutné stránku aktualizovat.

## <a name="query-a-sql-database"></a>Dotaz na databázi SQL

Následující příklad dotazů by měl úspěšně spustit proti adventureworkslt ukázkové databáze.

### <a name="run-a-select-query"></a>Spuštění dotazu SELECT

1. Vložte do editoru dotazů následující dotaz:

   ```sql
    SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
    FROM SalesLT.ProductCategory pc
    JOIN SalesLT.Product p
    ON pc.productcategoryid = p.productcategoryid;
   ```

2. Vyberte **Spustit** a pak zkontrolujte výstup v podokně **Výsledky.**

   ![výsledky editoru dotazů](./media/sql-database-connect-query-portal/query-editor-results.png)

3. Volitelně můžete dotaz uložit jako soubor .sql nebo exportovat vrácená data jako soubor JSON, CSV nebo XML.

### <a name="run-an-insert-query"></a>Spuštění dotazu INSERT

Spusťte následující příkaz [INSERT](/sql/t-sql/statements/insert-transact-sql/) T-SQL a `SalesLT.Product` přidejte do tabulky nový produkt.

1. Nahraďte předchozí dotaz tímto dotazem.

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


2. Výběrem **možnosti Spustit** vložte do `Product` tabulky nový řádek. Podokno **Zprávy** zobrazuje **dotaz proběhl úspěšně: Ovlivněné řádky: 1**.


### <a name="run-an-update-query"></a>Spuštění dotazu UPDATE

Spusťte následující [příkaz UPDATE](/sql/t-sql/queries/update-transact-sql/) T-SQL a upravte nový produkt.

1. Nahraďte předchozí dotaz tímto dotazem.

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Vyberte **Spustit,** chcete-li `Product` aktualizovat zadaný řádek v tabulce. Podokno **Zprávy** zobrazuje **dotaz proběhl úspěšně: Ovlivněné řádky: 1**.

### <a name="run-a-delete-query"></a>Spuštění dotazu DELETE

Chcete-li odebrat nový produkt, spusťte následující příkaz [DELETE](/sql/t-sql/statements/delete-transact-sql/) T-SQL.

1. Nahraďte předchozí dotaz tímto:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Výběrem **možnosti Spustit** odstraníte zadaný řádek v `Product` tabulce. Podokno **Zprávy** zobrazuje **dotaz proběhl úspěšně: Ovlivněné řádky: 1**.


## <a name="query-editor-considerations"></a>Důležité informace o editoru dotazů

Při práci s editorem dotazů je třeba vědět několik věcí.

* Editor dotazů používá ke komunikaci porty 443 a 1443. Ujistěte se, že jste na těchto portech povolili odchozí přenoshttps. Pro přístup k databázím a datovým skladům budete také muset přidat odchozí IP adresu do pravidel brány firewall na serveru.

* Editor dotazů pracuje s privátním odkazem, aniž by bylo nutné přidávat ip adresu klienta do brány firewall databáze SQL

* Stisknutím klávesy **F5** obnovíte stránku editoru dotazů a veškerý dotaz, na kterém se pracuje, bude ztracen.

* Editor dotazů nepodporuje připojení `master` k databázi.

* Je 5 minut časový limit pro spuštění dotazu.

* Editor dotazů podporuje pouze válcovou projekci pro geografické datové typy.

* Neexistuje žádná podpora pro IntelliSense pro databázové tabulky a zobrazení, ale editor podporuje automatické dokončování na názvy, které již byly zadány.




## <a name="next-steps"></a>Další kroky

Další informace o Transact-SQL (T-SQL) podporované v databázích Azure SQL najdete v [tématu Řešení rozdílů transakt-SQL během migrace do databáze SQL](sql-database-transact-sql-information.md).
