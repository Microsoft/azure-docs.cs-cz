---
title: "Konfigurace ověřování Azure Active Directory - SQL | Microsoft Docs"
description: "Zjistěte, jak se připojit k databázi SQL, spravované Instance a SQL Data Warehouse pomocí Azure Active Directory Authentication – po dokončení konfigurace Azure AD."
services: sql-database
author: GithubMirek
manager: johammer
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 03/07/2018
ms.author: mireks
ms.openlocfilehash: 00b5be9863e2bff9e5b82845f99d6829e1bcdf13
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="configure-and-manage-azure-active-directory-authentication-with-sql-database-managed-instance-or-sql-data-warehouse"></a>Konfigurovat a spravovat ověřování Azure Active Directory s databází SQL, spravované Instance nebo SQL Data Warehouse

Tento článek ukazuje, jak vytvořit a naplnit Azure AD a pak použít Azure AD s Azure SQL Database a SQL Data Warehouse. Přehled najdete v tématu [ověřování Azure Active Directory](sql-database-aad-authentication.md).

>  [!NOTE]  
>  Nepodporuje připojení k SQL Server běžící na virtuálním počítači Azure pomocí účtu Azure Active Directory. Místo toho používejte doménu účtu služby Active Directory.

## <a name="create-and-populate-an-azure-ad"></a>Vytvořit a naplnit Azure AD
Vytvoření Azure AD a jeho naplnění uživatelů a skupin. Azure AD může být počáteční Azure AD spravované domény. Azure AD může být také místní Active Directory Domain Services, je sdružených se službou Azure AD.

Další informace najdete v tématech [Integrování místních identit do služby Azure Active Directory](../active-directory/active-directory-aadconnect.md), [Přidání vlastního názvu domény do Azure AD](../active-directory/active-directory-domains-add-azure-portal.md), [Microsoft Azure podporuje federaci s Windows Server Active Directory](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), [Správa adresáře služby Azure AD](../active-directory/active-directory-administer.md), [Správa služby Azure AD pomocí rozhraní Windows PowerShell](/powershell/azure/overview?view=azureadps-2.0) a [Porty a protokoly, které vyžaduje hybridní identita](..//active-directory/connect/active-directory-aadconnect-ports.md).

## <a name="associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Přidružení nebo přidat předplatné Azure do Azure Active Directory

1. Přidružte vašeho předplatného Azure do Azure Active Directory tím, že adresář důvěryhodné adresáře pro předplatné Azure, který je hostitelem databáze. Podrobnosti najdete v tématu [asociování předplatných Azure se službou Azure AD](../active-directory/active-directory-how-subscriptions-associated-directory.md).
2. Použijte přepínači directory na portálu Azure přejděte na předplatné spojené s doménou.

   **Další informace:** předplatné každých Azure má vztah důvěryhodnosti s instancí Azure AD. To znamená, že tomuto adresáři svěřuje ověřování uživatelů, služeb i zařízení. Několik předplatných může důvěřovat stejnému adresáři, ale jedno předplatné důvěřuje pouze jednomu adresáři. Vztah důvěryhodnosti, který má předplatné s adresářem, se liší od vztahu, který má předplatné se všemi ostatními prostředky ve službě Azure (webové stránky, databáze apod.), což jsou pro předplatné spíše podřízené prostředky. Pokud platnost předplatného vyprší, zastaví se i přístup k těmto dalším prostředkům přidruženým k předplatnému. Adresář však ve službě Azure zůstane a vy k němu můžete přidružit jiné předplatné a pokračovat ve správě uživatelů adresáře. Další informace o prostředcích najdete v tématu [Principy přístupu k prostředkům v Azure](../active-directory/active-directory-b2b-admin-add-users.md). Další informace o tom důvěryhodný vztah najdete [přidružit nebo přidání předplatného Azure do Azure Active Directory](../active-directory/active-directory-how-subscriptions-associated-directory.md).

## <a name="create-an-azure-ad-administrator-for-azure-sql-server"></a>Vytvoření správce Azure AD pro server Azure SQL
Každý server Azure SQL (který je hostitelem SQL Database nebo SQL Data Warehouse) spustí pomocí účtu správce jeden server, který je správcem celý server Azure SQL. Druhý správce systému SQL Server musí být vytvořeny, který je účet Azure AD. Tento objekt se vytvoří jako databáze s omezením uživatel v hlavní databázi. Jako správci, účty správce serveru, jsou členy **db_owner** role v každého uživatele databáze a potom zadejte každou databázi uživatele, jako **dbo** uživatele. Další informace o účtech správce serveru najdete v tématu [Správa databází a přihlašovacích údajů ve službě Azure SQL Database](sql-database-manage-logins.md).

Při použití služby Azure Active Directory s geografickou replikací, správce Azure Active Directory musí být nakonfigurované pro primární a sekundární servery. Pokud server nemá správce Azure Active Directory, pak přihlášení Azure Active Directory a uživatelé zobrazí "nelze se připojit" k chybě serveru.

> [!NOTE]
> Uživatelé, které nejsou založené na účet služby Azure AD (včetně správce serveru SQL Azure), Azure AD na základě uživatele, nelze vytvořit, protože nemají oprávnění k ověření uživatelů navrhované databáze s Azure AD.
> 

## <a name="provision-an-azure-active-directory-administrator-for-your-managed-instance"></a>Zřízení správcem služby Azure Active Directory vaší spravované Instance

> [!IMPORTANT]
> Pokud zřizujete instanci a spravovat pouze podle těchto kroků. Tuto operaci může provést pouze správce globální/společnosti ve službě Azure AD. Následující kroky popisují proces udělení oprávnění pro uživatele s jinou oprávnění v adresáři.

Vaše spravované Instance potřebuje oprávnění ke čtení úspěšně provést úkoly, jako je například ověřování uživatelů pomocí členství ve skupině zabezpečení nebo vytváření nových uživatelů Azure AD. Tento postup budete muset udělení oprávnění pro spravované Instance číst Azure AD. Chcete-li to provést dvěma způsoby: z portálu a prostředí PowerShell. Následující kroky obě metody.

1. Na portálu Azure v pravém horním rohu klikněte na připojení k rozevírací seznam možných Active Directory. 
2. Vyberte správný služby Active Directory jako výchozí Azure AD. 

   Tento krok odkazy předplatné spojené se službou Active Directory s instancí spravované a ujistěte se, že stejného předplatného. používá se pro Azure AD a spravované Instance.
3. Přejděte do spravované Instance a vyberte ten, který chcete použít pro integraci služby Azure AD.

   ![aad](./media/sql-database-aad-authentication/aad.png)

4.  Klikněte na hlavičku v horní části stránky pro správu služby Active Directory. Pokud jste přihlášeni jako správce globální/společnosti ve službě Azure AD, můžete provést z portálu Azure nebo pomocí prostředí PowerShell.

    ![udělení oprávnění portálu](./media/sql-database-aad-authentication/grant-permissions.png)

    ![udělení oprávnění powershell](./media/sql-database-aad-authentication/grant-permissions-powershell.png)
    
    Pokud jste přihlášeni jako správce globální/společnosti ve službě Azure AD, můžete provést z portálu Azure nebo spustit skript prostředí PowerShell.

5. Po úspěšném dokončení operace následující oznámení se zobrazí v pravém horním rohu:

    ![úspěch](./media/sql-database-aad-authentication/success.png)

6.  Nyní můžete správce Azure AD vaší spravované Instance. K tomu, na stránky pro správu služby Active Directory, klikněte na **nastavit správce** příkaz.

    ![set-admin](./media/sql-database-aad-authentication/set-admin.png)

7. Na stránce přidat správce, vyhledejte uživatele, vyberte uživatele nebo skupinu, správce a pak klikněte na tlačítko **vyberte**. 

   Stránky pro správu služby Active Directory zobrazuje všechny členy a skupin služby Active Directory. Uživatelé nebo skupiny, které jsou zobrazena šedě nelze vybrat, protože nejsou podporovány jako správci služby Azure AD. Zobrazit seznam podporovaných admins v [funkce Azure AD a omezení](sql-database-aad-authentication.md#azure-ad-features-and-limitations). Řízení přístupu na základě role (RBAC) se vztahuje pouze na portálu Azure a není rozšíří do systému SQL Server.

    ![přidat správce.](./media/sql-database-aad-authentication/add-admin.png)

8. V horní části stránky pro správu služby Active Directory, klikněte na tlačítko **Uložit**.

    ![Uložit](./media/sql-database-aad-authentication/save.png)

    Proces změny správce může trvat několik minut. Nový správce se pak objeví v poli Správce služby Active Directory.

> [!IMPORTANT]
> Při nastavování správce Azure AD, nový název správce (uživatele či skupinu) nemůže být již přítomny v hlavní databázi virtuální jako uživatel ověřování systému SQL Server. Pokud je k dispozici, se nezdaří instalace správce Azure AD a vrácení zpět jeho vytváření, což indikuje, že takový správce (název) již existuje. Vzhledem k tomu, že takové systému SQL Server ověřování uživatele není součástí Azure AD, všechny úsilí pro připojení k serveru pomocí ověřování Azure AD se nezdaří.

> [!TIP]
> Chcete-li později odebrat správce, v horní části stránky pro správu služby Active Directory, klikněte na tlačítko **odebrat správce**a potom klikněte na **Uložit**.
 
## <a name="provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server"></a>Zřízení správcem služby Azure Active Directory pro server Azure SQL Database

> [!IMPORTANT]
> Podle těchto kroků, pouze pokud zřizujete na serveru Azure SQL Database nebo datového skladu.

Tyto dva postupy ukazují, jak zřídit správce Azure Active Directory pro server Azure SQL na portálu Azure a pomocí prostředí PowerShell.

### <a name="azure-portal"></a>Azure Portal
1. V [portál Azure](https://portal.azure.com/), v pravém horním rohu klikněte na připojení k rozevírací seznam možných Active Directory. Vyberte správný služby Active Directory jako výchozí Azure AD. Tento krok odkazy přidružení předplatné služby Active Directory s Azure SQL serveru a ujistěte se, že stejného předplatného. používá se pro Azure AD a SQL Server. (Server Azure SQL můžete hostování, Azure SQL Database nebo Azure SQL Data Warehouse.)   
    ![choose-ad][8]   
    
2. V levém banner vyberte **servery SQL**, vyberte vaše **systému SQL server**a potom v **systému SQL Server** klikněte na tlačítko **správce Active Directory**.   
3. V **správce Active Directory** klikněte na tlačítko **nastavit správce**.   
    ![Vyberte možnost active directory](./media/sql-database-aad-authentication/select-active-directory.png)  
    
4. V **přidat správce** stránky, vyhledejte uživatele, vyberte uživatele nebo skupiny se správce a klikněte **vyberte**. (Stránky pro správu služby Active Directory zobrazí všechny členy a skupin služby Active Directory. Uživatelé nebo skupiny, které jsou zobrazena šedě nelze vybrat, protože nejsou podporovány jako správci služby Azure AD. (Zobrazit seznam podporovaných admins v **funkce Azure AD a omezení** části [použití Azure ověřování služby Active Directory k ověřování připojení k SQL Database nebo SQL Data Warehouse](sql-database-aad-authentication.md).) Řízení přístupu na základě role (RBAC) se vztahuje pouze na portálu a není rozšíří do systému SQL Server.   
    ![Vyberte správce](./media/sql-database-aad-authentication/select-admin.png)  
    
5. V horní části **správce Active Directory** klikněte na tlačítko **Uložit**.   
    ![Uložit správce](./media/sql-database-aad-authentication/save-admin.png)   

Proces změny správce může trvat několik minut. Potom nový správce se zobrazí v **správce Active Directory** pole.

   > [!NOTE]
   > Při nastavování správce Azure AD, nový název správce (uživatele či skupinu) nemůže být již přítomny v hlavní databázi virtuální jako uživatel ověřování systému SQL Server. Pokud je k dispozici, se nezdaří instalace správce Azure AD; vrácení zpět jeho vytvoření a označující, že takový správce (název) již existuje. Vzhledem k tomu, že takové systému SQL Server ověřování uživatele není součástí Azure AD, všechny úsilí pro připojení k serveru pomocí ověřování Azure AD se nezdaří.
   > 


Později odebrat správce, v horní části **správce Active Directory** klikněte na tlačítko **odebrat správce**a potom klikněte na **Uložit**.

### <a name="powershell"></a>PowerShell
Pokud chcete spustit rutiny prostředí PowerShell, musíte mít prostředí Azure PowerShell nainstalovaná a spuštěná. Podrobné informace najdete v tématu [Instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview).

Pokud chcete zřídit správce Azure AD, spusťte následující příkazy prostředí Azure PowerShell:

* Add-AzureRmAccount
* Select-AzureRmSubscription

Rutiny používají k zřizovat a spravovat správce Azure AD:

| Název rutiny | Popis |
| --- | --- |
| [Set-AzureRmSqlServerActiveDirectoryAdministrator](/powershell/module/azurerm.sql/set-azurermsqlserveractivedirectoryadministrator) |Zřídí správce Azure Active Directory pro server Azure SQL nebo Azure SQL Data Warehouse. (Musí být z aktuálního předplatného.) |
| [Remove-AzureRmSqlServerActiveDirectoryAdministrator](/powershell/module/azurerm.sql/remove-azurermsqlserveractivedirectoryadministrator) |Odebere správce Azure Active Directory pro server Azure SQL nebo Azure SQL Data Warehouse. |
| [Get-AzureRmSqlServerActiveDirectoryAdministrator](/powershell/module/azurerm.sql/get-azurermsqlserveractivedirectoryadministrator) |Vrací informace o aktuálně nakonfigurován pro server Azure SQL nebo Azure SQL Data Warehouse správce Azure Active Directory. |

Použijte příkaz prostředí PowerShell get-help zobrazíte další informace pro každý z těchto příkazů, například ``get-help Set-AzureRmSqlServerActiveDirectoryAdministrator``.

Tento skript je zřizuje skupině Správce služby Azure AD s názvem **DBA_Group** (id objektu `40b79501-b343-44ed-9ce7-da4c8cc7353f`) pro **demo_server** serveru ve skupině prostředků s názvem **skupiny – 23**:

```
Set-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23"
-ServerName "demo_server" -DisplayName "DBA_Group"
```

**DisplayName** vstupní parametr přijímá zobrazovaný název služby Azure AD nebo hlavní název uživatele. Například ``DisplayName="John Smith"`` a ``DisplayName="johns@contoso.com"``. Zobrazovaný název skupiny Azure AD, Azure AD je podporováno.

> [!NOTE]
> Příkaz prostředí Azure PowerShell ```Set-AzureRmSqlServerActiveDirectoryAdministrator``` nezabrání zřizování správci Azure AD pro nepodporovaný uživatele. Nepodporované uživatele může být zřízen, ale nemůže připojit k databázi. 
> 

Následující příklad používá nepovinný **ObjectID**:

```
Set-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23"
-ServerName "demo_server" -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> Azure AD **ObjectID** je nutné, když **DisplayName** není jedinečný. Načíst **ObjectID** a **DisplayName** hodnot, pomocí informací v části služby Active Directory z portálu Azure Classic a zobrazení vlastností uživatele nebo skupinu.
> 

Následující příklad vrací informace o aktuální správce Azure AD pro server Azure SQL:

```
Get-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" | Format-List
```

Následující příklad odebere správce Azure AD:

```
Remove-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server"
```

Můžete také vytvářet správce Azure Active Directory pomocí rozhraní REST API. Další informace najdete v tématu [odkaz na službu správy REST API a operace pro operace databáze SQL Azure pro databáze SQL Azure](https://msdn.microsoft.com/library/azure/dn505719.aspx)

### <a name="cli"></a>Rozhraní příkazového řádku  
Správce služby Azure AD můžete také vytvářet voláním rozhraní příkazového řádku následující příkazy:
| Příkaz | Popis |
| --- | --- |
|[vytvoření ad správce az sql serveru](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_create) |Zřídí správce Azure Active Directory pro server Azure SQL nebo Azure SQL Data Warehouse. (Musí být z aktuálního předplatného.) |
|[az sql server ad-admin delete](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_delete) |Odebere správce Azure Active Directory pro server Azure SQL nebo Azure SQL Data Warehouse. |
|[AZ sql server ad správce seznamu](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_list) |Vrací informace o aktuálně nakonfigurován pro server Azure SQL nebo Azure SQL Data Warehouse správce Azure Active Directory. |
|[aktualizace ad správce az sql server](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_update) |Aktualizace správce Active Directory pro server Azure SQL nebo Azure SQL Data Warehouse. |

Další informace o rozhraní příkazového řádku najdete v tématu [SQL - az sql](https://docs.microsoft.com/cli/azure/sql/server).  


## <a name="configure-your-client-computers"></a>Konfigurace klientských počítačů
Na všechny klientské počítače, ze kterých vaše aplikace nebo uživatele připojit k databázi SQL Azure nebo Azure SQL Data Warehouse pomocí Azure AD identity, musíte nainstalovat následující software:

* Rozhraní .NET framework 4.6 nebo novější z [https://msdn.microsoft.com/library/5a4x27ek.aspx](https://msdn.microsoft.com/library/5a4x27ek.aspx).
* Knihovna ověřování Azure Active Directory pro SQL Server (**ADALSQL. Knihovny DLL**) je k dispozici v několika jazycích (x86 a amd64) z webu Stažení softwaru [Microsoft Active Directory Authentication Library pro Microsoft SQL Server](http://www.microsoft.com/download/details.aspx?id=48742).

Můžete splňovat tyto požadavky podle:

* Instalace buď [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) nebo [SQL Server Data Tools pro Visual Studio 2015](https://msdn.microsoft.com/library/mt204009.aspx) splňuje požadavek na rozhraní .NET Framework 4.6.
* Verze aplikace SSMS nainstaluje x86 **ADALSQL. Knihovny DLL**.
* Rozšíření SSDT nainstaluje verzi amd64 **ADALSQL. Knihovny DLL**.
* Nejnovější sady Visual Studio z [Visual Studio stáhne](https://www.visualstudio.com/downloads/download-visual-studio-vs) splňuje požadavek na rozhraní .NET Framework 4.6, ale nenainstaluje požadované amd64 verzi **ADALSQL. Knihovny DLL**.

## <a name="create-contained-database-users-in-your-database-mapped-to-azure-ad-identities"></a>Vytvoření databáze s omezením uživatelů ve vaší databázi namapované na identit Azure AD

Ověřování Azure Active Directory vyžaduje, aby uživatelé databázi vytvořit, protože uživatelé databáze s omezením. Uživatel databáze s omezením na základě identity služby Azure AD, je uživateli databáze, který nemá přihlášení v hlavní databázi, a který se mapuje na identity v adresáři služby Azure AD, která souvisí s databází. Identita služby Azure AD může být individuální uživatelský účet nebo skupinu. Další informace o uživatele databáze s omezením naleznete v tématu [obsažené uživatelů databáze provádění vaše přenosné databáze](https://msdn.microsoft.com/library/ff929188.aspx).

> [!NOTE]
> Uživatelé databáze (s výjimkou správci) nelze vytvořit pomocí portálu. Role RBAC nebudou rozšířeny do systému SQL Server, SQL Database nebo SQL Data Warehouse. Azure role RBAC slouží ke správě prostředků Azure a nevztahují na oprávnění databáze. Například **SQL serveru Přispěvatel** role neuděluje přístup k připojení k SQL Database nebo SQL Data Warehouse. Přímo v databázi pomocí příkazů Transact-SQL musí udělit oprávnění k přístupu.
>

Chcete-li vytvořit Azure AD na základě obsažené databáze uživatele (jiné než na správce serveru, který vlastní databáze), připojení k databázi s identitou služby Azure AD jako uživatel s alespoň **ALTER ANY uživatele** oprávnění. Pak použijte následující syntaxi jazyka Transact-SQL:

```
CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
```

*Azure_AD_principal_name* může být hlavní název uživatele uživatele Azure AD, nebo zobrazovaný název pro skupinu služby Azure AD.

**Příklady:** vytvořit databázi s omezením uživatele Azure AD představující federovaný nebo spravovaný uživatele domény:
```
CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;
```

Chcete-li vytvořit uživatele databáze s omezením představující Azure AD nebo federovaných skupiny domény, zadejte zobrazovaný název skupiny zabezpečení:
```
CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;
```

Chcete-li vytvořit uživatele databáze s omezením představující aplikaci, která se připojuje pomocí tokenu Azure AD:

```
CREATE USER [appName] FROM EXTERNAL PROVIDER;
```

>  [!TIP]
>  Uživatele nelze vytvořit přímo z Azure Active Directory než Azure Active Directory, která souvisí s předplatným Azure. Členy jiné Active Directory, které jsou importované uživatele v přidružené služby Active Directory (označuje se jako externí uživatele) však můžete přidat do skupiny služby Active Directory v klientovi služby Active Directory. Vytvořením uživatele databáze s omezením pro tuto skupinu AD Uživatelé z externí služby Active Directory můžete získat přístup k databázi SQL.   

Další informace o vytváření obsahovala databázi uživatelů podle identit Azure Active Directory, najdete v části [vytvořit uživatele (Transact-SQL)](http://msdn.microsoft.com/library/ms173463.aspx).

> [!NOTE]
> Odebrání správce Azure Active Directory pro server Azure SQL zabrání žádné uživatele Azure AD authentication připojování k serveru. Pokud potřebné, nepoužitelná uživatele Azure AD může být přetažen ručně správcem databáze SQL.   

>  [!NOTE]
>  Pokud se zobrazí **vypršel časový limit připojení**, budete muset nastavit `TransparentNetworkIPResolution` parametr připojovací řetězec na hodnotu false. Další informace najdete v tématu [problém časový limit připojení v rozhraní .NET Framework 4.6.1 - TransparentNetworkIPResolution](https://blogs.msdn.microsoft.com/dataaccesstechnologies/2016/05/07/connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution/).   

   
Když vytvoříte uživatel databáze, tento uživatel obdrží **připojit** oprávnění a můžou se připojit k databázi jako člen skupiny **veřejné** role. Původně jenom oprávnění, která je k dispozici pro uživatele jsou všechna oprávnění udělená **veřejné** role nebo všechna oprávnění udělená skupiny libovolný Azure AD, že jsou členem. Po zřízení Azure uživatel databáze založené na AD obsažené můžete udělit uživatele další oprávnění, stejně jako udělit oprávnění typ uživatele. Obvykle udělit oprávnění pro databázové role a přidání uživatelů do rolí. Další informace najdete v tématu [základy oprávnění databáze modul](http://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx). Další informace o rolích speciální databáze SQL najdete v tématu [Správa databází a přihlašovacích údajů ve službě Azure SQL Database](sql-database-manage-logins.md).
Federované domény uživatelského účtu, který je importovat do spravované domény jako externího uživatele, musíte použít identita spravované domény.

> [!NOTE]
> Azure AD Uživatelé jsou označené v metadatech databáze s typem E (EXTERNAL_USER) a pro skupiny typu X (EXTERNAL_GROUPS). Další informace najdete v tématu [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx). 
>

## <a name="connect-to-the-user-database-or-data-warehouse-by-using-ssms-or-ssdt"></a>Připojení k uživatel databáze nebo datového skladu pomocí aplikace SSMS nebo rozšíření SSDT  
Chcete-li potvrdit, správce Azure AD je správně nastavit, připojte se ke **hlavní** databázi pomocí účtu správce služby Azure AD.
Ke zřízení služby Azure AD obsažené databáze uživatelů (jiné než na správce serveru, který vlastní databáze), připojení k databázi s identitou služby Azure AD, která má přístup k databázi.

> [!IMPORTANT]
> Podpora pro ověřování Azure Active Directory je k dispozici s [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) a [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) ve Visual Studiu 2015. Srpna 2016 verzi aplikace SSMS zahrnuje taky podporu pro univerzální ověřování služby Active Directory, které správcům umožňují vyžadovat Vícefaktorové ověřování pomocí telefonní hovor, textová zpráva, čipové karty s PIN kód nebo oznámení mobilní aplikace.
 
## <a name="using-an-azure-ad-identity-to-connect-using-ssms-or-ssdt"></a>Pomocí Azure AD identity a připojte se pomocí aplikace SSMS nebo rozšíření SSDT  

Následující postupy ukazují, jak se připojit k databázi SQL s Azure AD identity pomocí SQL Server Management Studio nebo nástroje databáze systému SQL Server.

### <a name="active-directory-integrated-authentication"></a>Integrované ověřování Active Directory

Tuto metodu použijte, pokud jsou přihlášení k systému Windows pomocí přihlašovacích údajů Azure Active Directory z federované domény.

1. Spustit Management Studio nebo nástrojů Data a v **připojit k serveru** (nebo **připojit k databázovém stroji**) dialogu **ověřování** vyberte  **Active Directory – integrované**. Žádné heslo je potřeba, nebo můžete zadat, protože zobrazí vaše stávající přihlašovací údaje pro připojení.   

    ![Vyberte integrované ověřování AD][11]
2. Klikněte na tlačítko **možnosti** tlačítko a na **vlastnosti připojení** stránky v **připojit k databázi** pole, zadejte název uživatele databáze, kterou chcete připojit k. ( **ID názvu nebo klienta domény AD**"možnost je podporována pouze pro **Universal s MFA připojení** možnosti, jinak se šedé.)  

    ![Vyberte název databáze][13]

## <a name="active-directory-password-authentication"></a>Ověřování hesla služby Active Directory

Tuto metodu použijte, pokud připojíte přes hlavní název služby Azure AD pomocí služby Azure AD spravované domény. Můžete ji použít i pro federované účet bez přístupu k doméně, například při práci se vzdáleně.

Tuto metodu použijte, pokud jste přihlášení k systému Windows pomocí přihlašovacích údajů z domény, která není sdružených se službou Azure, nebo pomocí ověřování Azure AD pomocí služby Azure AD na základě počáteční nebo domény klienta.

1. Spustit Management Studio nebo nástrojů Data a v **připojit k serveru** (nebo **připojit k databázovém stroji**) dialogu **ověřování** vyberte  **Active Directory – heslo**.
2. V **uživatelské jméno** zadejte jméno uživatele Azure Active Directory ve formátu  **username@domain.com** . Toto musí být účet ze služby Azure Active Directory nebo účet z domény vytvořit federaci s Azure Active Directory.
3. V **heslo** pole, zadejte heslo uživatele pro účet služby Azure Active Directory nebo federované účet domény.

    ![Vyberte možnost ověřování hesla služby AD][12]
4. Klikněte na tlačítko **možnosti** tlačítko a na **vlastnosti připojení** stránky v **připojit k databázi** pole, zadejte název uživatele databáze, kterou chcete připojit k. (Viz obrázek v předchozí možnost.)

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>Pomocí Azure AD identity pro připojení z klientské aplikace

Následující postupy ukazují, jak se připojit k databázi SQL s Azure AD identity z klientské aplikace.

###  <a name="active-directory-integrated-authentication"></a>Integrované ověřování Active Directory

Pokud chcete použít integrované ověřování systému Windows, musí být vaší doméně služby Active Directory sdružených se službou Azure Active Directory. Klientské aplikace (nebo služby) s připojením k databázi, musíte používat na počítači připojeném k doméně, v části přihlašovací údaje uživatele domény.

Chcete-li připojit k databázi pomocí integrované ověřování a identita Azure AD, musí být nastavena ověřování – klíčové slovo připojovacího řetězce databáze do Active Directory integrované. Následující ukázka kódu C# používá rozhraní ADO .NET.

```
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Klíčové slovo připojovacího řetězce ``Integrated Security=True`` není podporována pro připojení k databázi SQL Azure. Při vytváření připojení ODBC, musíte se k odebrání mezer a nastavení ověřování pro 'ActiveDirectoryIntegrated'.

### <a name="active-directory-password-authentication"></a>Ověřování hesla služby Active Directory

Chcete-li připojit k databázi pomocí integrované ověřování a identita Azure AD, musí být nastavena – klíčové slovo ověřování Active Directory heslo. Připojovací řetězec musí obsahovat identifikátor ID uživatele nebo uživatele a heslo/PWD klíčová slova a hodnoty. Následující ukázka kódu C# používá rozhraní ADO .NET.

```
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Další informace o metodách ověřování Azure AD pomocí ukázky kódu ukázku, která je k dispozici na [Azure AD Authentication Githubu ukázku](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth).

## <a name="azure-ad-token"></a>Azure AD token
Tato metoda ověřování umožňuje službám střední vrstvě pro připojení k databázi SQL Azure nebo Azure SQL Data Warehouse pomocí získání tokenu z Azure Active Directory (AAD). Umožňuje sofistikované scénáře, včetně ověřování pomocí certifikátů. Musíte provést čtyři základní kroky k ověření pomocí tokenu Azure AD:

1. Registrace aplikace pomocí Azure Active Directory a získejte id klienta pro váš kód. 
2. Vytvořte uživatele databáze představující aplikace. (Dokončit výše v kroku 6.)
3. Vytvoření certifikátu na spuštění počítače klienta aplikace.
4. Přidání certifikátu jako klíč pro vaši aplikaci.

Ukázka připojovací řetězec:

```
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
connection.AccessToken = "Your JWT token"
conn.Open();
```

Další informace najdete v tématu [SQL Server Security Blog](https://blogs.msdn.microsoft.com/sqlsecurity/2016/02/09/token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/). Informace o přidání certifikátu najdete v tématu [Začínáme s ověřováním na základě certifikátů ve službě Azure Active Directory](../active-directory/active-directory-certificate-based-authentication-get-started.md).

### <a name="sqlcmd"></a>sqlcmd

Následující příkazy, připojit pomocí sqlcmd, která je k dispozici od verze 13.1 [Download Center](http://go.microsoft.com/fwlink/?LinkID=825643).

```
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net  -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="next-steps"></a>Další postup
- Přehled řízení a přístupu pro SQL Database najdete v tématu věnovaném [řízení a přístupu k SQL Database](sql-database-control-access.md).
- Přehled přihlášení, uživatelů a databázových rolí ve službě SQL Database najdete v tématu věnovaném [přihlášením, uživatelům a databázovým rolím](sql-database-manage-logins.md).
- Další informace o objektech zabezpečení databáze najdete v tématu [Objekty zabezpečení](https://msdn.microsoft.com/library/ms181127.aspx).
- Další informace o databázových rolích najdete v tématu věnovaném [databázovým rolím](https://msdn.microsoft.com/library/ms189121.aspx).
- Další informace o pravidlech brány firewall pro SQL Database najdete v tématu [Pravidla brány firewall služby SQL Database](sql-database-firewall-configure.md).

<!--Image references-->

[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/active-directory-integrated.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth2.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db2.png

