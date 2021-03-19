---
title: Dotazování SQL Database pomocí Editoru dotazů v Azure Portal (Preview)
description: Naučte se používat Editor dotazů ke spouštění dotazů jazyka Transact-SQL (T-SQL) pro databázi v Azure SQL Database.
titleSuffix: Azure SQL Database
keywords: připojení k SQL Database, dotazování SQL Database, Azure Portal, Portal, Editor dotazů
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1, contperf-fy21q3-portal
ms.devlang: ''
ms.topic: quickstart
author: Ninarn
ms.author: ninarn
ms.reviewer: sstein
ms.date: 03/01/2021
ms.openlocfilehash: a6f13e27a5aa2684a16565c616d781e3d5c3a750
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594185"
---
# <a name="quickstart-use-the-azure-portals-query-editor-preview-to-query-an-azure-sql-database"></a>Rychlý Start: použití Editoru dotazů Azure Portal (Preview) k dotazování na Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Editor dotazů je nástroj v Azure Portal pro spouštění dotazů SQL pro vaši databázi v Azure SQL Database nebo datového skladu ve službě Azure synapse Analytics.

V tomto rychlém startu použijete Editor dotazů ke spouštění dotazů jazyka Transact-SQL (T-SQL) na databázi.

## <a name="prerequisites"></a>Předpoklady

### <a name="create-a-database-with-sample-data"></a>Vytvoření databáze s ukázkovými daty

Dokončení tohoto rychlého startu vyžaduje ukázkovou databázi AdventureWorksLT. Pokud nemáte pracovní kopii ukázkové databáze AdventureWorksLT v SQL Database, následující rychlý Start vám pomůže ho rychle vytvořit:

[Rychlý Start: vytvoření databáze v Azure SQL Database pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku Azure](single-database-create-quickstart.md)

### <a name="set-an-azure-active-directory-admin-for-the-server-optional"></a>Nastavit správce Azure Active Directory pro server (volitelné)

Konfigurace správce Azure Active Directory (Azure AD) umožňuje použít jedinou identitu pro přihlášení k Azure Portal a databázi. Pokud chcete pro připojení k editoru dotazů použít službu Azure AD, postupujte podle následujících kroků.

Tento proces je nepovinný, můžete místo toho použít ověřování SQL pro připojení k editoru dotazů.

> [!NOTE]
> * E-mailové účty (například outlook.com, gmail.com, yahoo.com atd.) se zatím nepodporují jako správci služby Azure AD. Nezapomeňte vybrat uživatele vytvořeného nativně v Azure AD nebo federovaném do Azure AD.
> * Přihlašování správce Azure AD funguje s účty, které mají povolené dvojúrovňové ověřování, ale Editor dotazů nepodporuje 2-Factor Authentication.

1. V Azure Portal přejděte na server služby SQL Database.

2. V nabídce **SQL Server** vyberte **Správce služby Active Directory**.

3. Na panelu nástrojů SQL Server na stránce pro **správu služby Active Directory** vyberte **nastavit správce**.

    ![Výběr Active Directory](./media/connect-query-portal/select-active-directory.png)

4. Na stránce **přidat správce** zadejte do vyhledávacího pole uživatele nebo skupinu, které chcete najít, vyberte je jako správce a pak klikněte na tlačítko **Vybrat** .

5. Zpět na panelu nástrojů SQL Server **Active Directory Page admin** vyberte **Save (Uložit**).

## <a name="using-sql-query-editor"></a>Použití Editoru dotazů SQL

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) a vyberte databázi, kterou chcete dotazovat.

2. V nabídce **databáze SQL** vyberte **Editor dotazů (Preview)**.

    ![vyhledání editoru dotazů](./media/connect-query-portal/find-query-editor.PNG)

### <a name="establish-a-connection-to-the-database"></a>Navázat připojení k databázi

I když jste se přihlásili k portálu, pořád potřebujete zadat přihlašovací údaje pro přístup k databázi. K připojení k databázi se můžete připojit pomocí ověřování SQL nebo Azure Active Directory.

#### <a name="connect-using-sql-authentication"></a>Připojení s využitím ověřování SQL

1. Na **přihlašovací** stránce v části **ověřování systému SQL Server** zadejte **přihlašovací jméno** a **heslo** uživatele, který má přístup k databázi. Pokud si nejste jistí, použijte přihlašovací jméno a heslo pro správce serveru databáze.

    ![přihlášení](./media/connect-query-portal/login-menu.png)

2. Vyberte **OK**.

#### <a name="connect-using-azure-active-directory"></a>Připojit pomocí Azure Active Directory

V **Editoru dotazů (Preview)** se podívejte na **přihlašovací** stránku v části **ověřování služby Active Directory** . K ověřování dojde automaticky, takže pokud jste správcem služby Azure AD v databázi, zobrazí se zpráva oznamující, že jste se přihlásili. Pak vyberte tlačítko **pokračovat jako** *\<your user or group ID>* . Pokud stránka indikuje, že jste se neúspěšně přihlásili, možná budete muset stránku aktualizovat.

### <a name="query-a-database-in-sql-database"></a>Dotazování databáze v SQL Database

Následující příklady dotazů by měly být úspěšně spuštěny proti ukázkové databázi AdventureWorksLT.

#### <a name="run-a-select-query"></a>Spustit výběrový dotaz

1. Do editoru dotazů vložte následující dotaz:

   ```sql
    SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
    FROM SalesLT.ProductCategory pc
    JOIN SalesLT.Product p
    ON pc.productcategoryid = p.productcategoryid;
   ```

2. Vyberte **Spustit** a potom zkontrolujte výstup v podokně **výsledků** .

   ![výsledky editoru dotazů](./media/connect-query-portal/query-editor-results.png)

3. V případě potřeby můžete dotaz uložit jako soubor. SQL nebo exportovat vracená data jako soubor. JSON,. csv nebo. XML.

#### <a name="run-an-insert-query"></a>Spuštění dotazu pro vložení

Chcete-li přidat nový produkt v tabulce, [Spusťte následující příkaz](/sql/t-sql/statements/insert-transact-sql/) T-SQL `SalesLT.Product` .

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


2. Vyberte možnost **Spustit**  pro vložení nového řádku do `Product` tabulky. V podokně **zprávy** se zobrazí **dotaz uspěl: ovlivněné řádky: 1**.


#### <a name="run-an-update-query"></a>Spustit aktualizační dotaz

Pokud chcete upravit nový produkt, spusťte následující příkaz [Update](/sql/t-sql/queries/update-transact-sql/) jazyka T-SQL.

1. Nahraďte předchozí dotaz tímto.

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Vyberte možnost **Spustit** a aktualizujte zadaný řádek v `Product` tabulce. V podokně **zprávy** se zobrazí **dotaz uspěl: ovlivněné řádky: 1**.

#### <a name="run-a-delete-query"></a>Spustit odstraňovací dotaz

Spuštěním následujícího příkazu [Delete](/sql/t-sql/statements/delete-transact-sql/) jazyka T-SQL odeberte nový produkt.

1. Nahraďte předchozí dotaz jedním z těchto:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Vyberte možnost **Spustit** a odstraňte zadaný řádek v `Product` tabulce. V podokně **zprávy** se zobrazí **dotaz uspěl: ovlivněné řádky: 1**.


## <a name="troubleshooting-and-considerations"></a>Jak řešit problémy a co vzít do úvahy

Při práci s editorem dotazů se dozvíte několik věcí.

### <a name="configure-local-network-settings"></a>Konfigurovat nastavení místní sítě

Pokud se v editoru dotazů zobrazí jedna z následujících chyb:
 - *Je možné, že nastavení místní sítě brání editoru dotazů ve vydávání dotazů. Pokyny ke konfiguraci nastavení sítě získáte kliknutím sem.*
 - *Nepovedlo se navázat připojení k serveru. To může znamenat problém s konfigurací místní brány firewall nebo nastavením síťového proxy serveru.*

Důvodem je to, že Editor dotazů používá ke komunikaci port 443 a 1443. Musíte zajistit, aby na těchto portech byl povolený odchozí provoz HTTPS. Níže uvedené pokyny vás provedou postupem, jak to provést v závislosti na vašem operačním systému. Abyste mohli udělit souhlas s otevřením tohoto připojení v místní síti, možná budete muset pracovat s podnikovým IT oddělením.

#### <a name="steps-for-windows"></a>Postup pro Windows

1. Otevřít **firewall v programu Windows Defender**
2. V nabídce na levé straně vyberte **Upřesnit nastavení** .
3. V **bráně firewall v programu Windows Defender s pokročilým zabezpečením** vyberte v nabídce vlevo možnost **odchozí pravidla** .
4. V nabídce na pravé straně vyberte **nové pravidlo...**

V Průvodci vytvořením **nového odchozího pravidla** proveďte tyto kroky:

1. Jako typ pravidla, které chcete vytvořit, vyberte **port** . Vyberte **Další**.
2. Vybrat **TCP**
3. Vyberte **konkrétní vzdálené porty** a zadejte "443, 1443". Pak vyberte **Další** .
4. Vyberte možnost "povolí připojení, pokud je zabezpečené".
5. Vyberte **Další** a pak znovu vybrat **Další** .
5. Zachovat všechny vybrané možnosti doména, soukromé a veřejné
6. Zadejte název pravidla, například přístup k editoru dotazů Azure SQL a volitelně také popis. Pak vyberte **Dokončit** .

#### <a name="steps-for-mac"></a>Postup pro Mac
1. Otevřete **Předvolby systému** (nabídka Apple > Předvolby systému).
2. Klikněte na **zabezpečení & ochrany osobních údajů**.
3. Klikněte na **Brána firewall**.
4. Pokud je brána firewall vypnutá, vyberte **kliknutím na zámek proveďte změny** dole a vyberte **zapnout bránu firewall** .
4. Klikněte na možnost **Možnosti brány firewall**.
5. V okně **zabezpečení & ochrany osobních údajů** vyberte tuto možnost: "automaticky povolí podepsanému softwaru přijímat příchozí připojení."

#### <a name="steps-for-linux"></a>Kroky pro Linux
Spusťte tyto příkazy a aktualizujte softwaru iptables
  ```
  sudo iptables -A OUTPUT -p tcp --dport 443 -j ACCEPT
  sudo iptables -A OUTPUT -p tcp --dport 1443 -j ACCEPT
  ```

### <a name="connection-considerations"></a>Požadavky na připojení

* Pro veřejná připojení k editoru dotazů je potřeba [Přidat odchozí IP adresu k povoleným pravidlům brány firewall serveru](firewall-create-server-level-portal-quickstart.md) pro přístup k databázím a datovým skladům.

* Pokud máte na serveru nastavené připojení privátního propojení a připojujete se k editoru dotazů z IP adresy v privátním Virtual Network, funguje Editor dotazů bez nutnosti přidat IP adresu klienta do pravidel brány firewall serveru SQL Database.

* Nejvíce základní oprávnění RBAC potřebná k použití Editoru dotazů jsou přístup pro čtení k serveru a databázi. K funkci editoru dotazů může přistupovat kdokoli s touto úrovní přístupu. Pokud chcete omezit přístup ke konkrétním uživatelům, musíte jim zabránit, aby se přihlásili k editoru dotazů pomocí Azure Active Directory nebo přihlašovacích údajů SQL pro ověřování. Pokud se nemohou přiřazovat sami jako správce AAD pro server nebo přístup k účtu správce SQL a přidat k němu, nesmí být schopni použít Editor dotazů.

* Editor dotazů nepodporuje připojení k `master` databázi.

* Editor dotazů se nemůže připojit k databázi repliky pomocí `ApplicationIntent=ReadOnly`

* Pokud se vám zobrazila tato chybová zpráva "záhlaví X-CSRF-Signature nebylo možné ověřit", proveďte následující akci pro vyřešení tohoto problému:

    * Ujistěte se, že hodiny počítače jsou nastavené na správný čas a časové pásmo. Můžete se také pokusit vyhledat časové pásmo počítače pomocí Azure, a to tak, že vyhledáte časové pásmo pro umístění vaší instance, například Východní USA, Tichomoří a tak dále.
    * Pokud se nacházíte v síti proxy, ujistěte se, že se nemění ani nevyřazuje hlavička Request "X-CSRF-Signature".

### <a name="other-considerations"></a>Další důležité informace

* Stisknutím **F5** aktualizujete stránku editoru dotazů a přijdete o všechny dotazy, které jsou právě zpracovávány.

* Pro provádění dotazů existuje časový limit 5 minut.

* Editor dotazů podporuje pouze válcovou projekci pro geografické datové typy.

* Pro tabulky a zobrazení databáze není k dispozici podpora technologie IntelliSense, ale editor podporuje automatické dokončování u názvů, které již byly zadány.

## <a name="next-steps"></a>Další kroky

Další informace o jazyce Transact-SQL (T-SQL) podporovaném v Azure SQL Database najdete v tématu [řešení rozdílů v jazyce Transact-SQL během migrace do SQL Database](transact-sql-tsql-differences-sql-server.md).
