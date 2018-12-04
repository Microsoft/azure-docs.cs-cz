---
title: Konfigurovat ověřování Azure Active Directory - SQL | Dokumentace Microsoftu
description: Zjistěte, jak se připojit k SQL Database Managed Instance a SQL Data Warehouse pomocí ověřování Azure Active Directory – po dokončení konfigurace služby Azure AD.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: data warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 12/03/2018
ms.openlocfilehash: 915aedd23019a91d434444c6da3b67a8c9c15337
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52849186"
---
# <a name="configure-and-manage-azure-active-directory-authentication-with-sql"></a>Konfigurace a Správa služby Azure Active Directory ověřování pomocí SQL

V tomto článku se dozvíte, jak vytvořit a naplnit Azure AD a Azure AD pomocí Azure [SQL Database](sql-database-technical-overview.md), [Managed Instance](sql-database-managed-instance.md), a [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Přehled najdete v tématu [ověřování Azure Active Directory](sql-database-aad-authentication.md).

> [!NOTE]
> Tento článek se týká k Azure SQL serveru a databází SQL Database a SQL Data Warehouse, které jsou vytvořeny na serveru Azure SQL. Pro zjednodušení se SQL Database používá k označení SQL Database i SQL Data Warehouse.
> [!IMPORTANT]  
> Připojení k SQL serveru běžícího na Virtuálním počítači Azure není podporováno použití účtu služby Azure Active Directory. Místo toho použijte doménu účtu služby Active Directory.

## <a name="create-and-populate-an-azure-ad"></a>Vytvoření a naplnění Azure AD

Vytvoření Azure AD a přidání uživatelů a skupin. Azure AD může být počáteční Azure AD spravované domény. Azure AD může být také v místním Active Directory Domain Services, která se federuje s Azure AD.

Další informace najdete v tématech [Integrování místních identit do služby Azure Active Directory](../active-directory/hybrid/whatis-hybrid-identity.md), [Přidání vlastního názvu domény do Azure AD](../active-directory/active-directory-domains-add-azure-portal.md), [Microsoft Azure podporuje federaci s Windows Server Active Directory](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), [Správa adresáře služby Azure AD](../active-directory/fundamentals/active-directory-administer.md), [Správa služby Azure AD pomocí rozhraní Windows PowerShell](/powershell/azure/overview?view=azureadps-2.0) a [Porty a protokoly, které vyžaduje hybridní identita](../active-directory/hybrid/reference-connect-ports.md).

## <a name="associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Přiřazení nebo přidání předplatného Azure ke službě Azure Active Directory

1. Přidružte předplatné Azure do služby Azure Active Directory tím, že adresář důvěryhodné adresář pro předplatné Azure, který je hostitelem databáze. Podrobnosti najdete v tématu [předplatné Azure propojeno se službou Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).
2. Na webu Azure Portal pomocí přepínače adresářů přejděte na předplatné spojené s doménou.

   **Další informace:** každé předplatné Azure má vztah důvěryhodnosti s instancí služby Azure AD. To znamená, že tomuto adresáři svěřuje ověřování uživatelů, služeb i zařízení. Několik předplatných může důvěřovat stejnému adresáři, ale jedno předplatné důvěřuje pouze jednomu adresáři. Vztah důvěryhodnosti, který má předplatné s adresářem, se liší od vztahu, který má předplatné se všemi ostatními prostředky ve službě Azure (webové stránky, databáze apod.), což jsou pro předplatné spíše podřízené prostředky. Pokud platnost předplatného vyprší, zastaví se i přístup k těmto dalším prostředkům přidruženým k předplatnému. Adresář však ve službě Azure zůstane a vy k němu můžete přidružit jiné předplatné a pokračovat ve správě uživatelů adresáře. Další informace o prostředcích najdete v tématu [Principy přístupu k prostředkům v Azure](../active-directory/active-directory-b2b-admin-add-users.md). Další informace o tom důvěryhodný vztah viz [přiřazení nebo přidání předplatného Azure ke službě Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="create-an-azure-ad-administrator-for-azure-sql-server"></a>Vytvoření správce Azure AD pro Azure SQL server

Každý server Azure SQL (který je hostitelem SQL Database nebo SQL Data Warehouse) se spustí pomocí účtu správce jeden server, který je správcem celý server Azure SQL. Druhý správce systému SQL Server musí být vytvořeny, že je účet služby Azure AD. Tento objekt je vytvořen jako uživatele databáze s omezením v hlavní databázi. Účty správce serveru jako správce, jsou členy **db_owner** roli v každé uživatelské databázi a zadejte každé uživatelské databázi jako **dbo** uživatele. Další informace o účtech správce serveru najdete v tématu [Správa databází a přihlášení ve službě Azure SQL Database](sql-database-manage-logins.md).

Při použití služby Azure Active Directory s geografickou replikací, musí být správce Azure Active Directory nakonfigurovaný pro primární a sekundární servery. Pokud server nemá správce Azure Active Directory, pak přihlašovacích údajů Azure Active Directory a uživatelů zobrazí "se nemůže připojit" k chybě serveru.

> [!NOTE]
> Uživatelé, které nejsou založené na účtu Azure AD (včetně účet správce serveru Azure SQL), Azure AD uživateli, nelze vytvořit, protože nemají oprávnění k ověření navrhovaným databázovým uživatelů s Azure AD.

## <a name="provision-an-azure-active-directory-administrator-for-your-managed-instance"></a>Zřízení správce Azure Active Directory pro Managed Instance

> [!IMPORTANT]
> Následující kroky proveďte, pouze pokud zřizujete Managed Instance. Tuto operaci může provést jenom globální/správce společnosti ve službě Azure AD. Následující kroky popisují proces udělování oprávnění pro uživatele s různá oprávnění v adresáři.

Managed Instance potřebuje oprávnění ke čtení služby Azure AD úspěšně provádět úlohy, jako je například ověřování uživatelů pomocí členství ve skupině zabezpečení nebo vytváření nových uživatelů. Aby to fungovalo budete muset udělit oprávnění k Managed Instance pro čtení služby Azure AD. Chcete-li to provést dvěma způsoby: z portálu a prostředí PowerShell. Následující kroky obě metody.

1. Na webu Azure Portal, v pravém horním rohu vyberte připojení ke rozevírací seznam možných aktivního adresáře.
2. Zvolte správné služby Active Directory jako výchozí služby Azure AD.

   Propojí předplatné spojené se službou Active Directory s Managed Instance a ujistěte se, že stejné předplatné používá pro obě služby Azure AD a Managed Instance.
3. Přejděte do Managed Instance a vyberte ten, který chcete použít k integraci Azure AD.

   ![aad](./media/sql-database-aad-authentication/aad.png)

4. Vyberte informační zprávu na stránce Správce služby Active Directory a udělení oprávnění pro aktuálního uživatele. Pokud jste přihlášeni jako globální/správce společnosti ve službě Azure AD, můžete ji provést z webu Azure portal nebo pomocí níže uvedeného skriptu Powershellu.

    ![udělit oprávnění portálu](./media/sql-database-aad-authentication/grant-permissions.png)

    ```PowerShell
    # Gives Azure Active Directory read permission to a Service Principal representing the Managed Instance.
    # Can be executed only by a "Company Administrator" or "Global Administrator" type of user.

    $aadTenant = "<YourTenantId>" # Enter your tenant ID
    $managedInstanceName = "MyManagedInstance"

    # Get Azure AD role "Directory Users" and create if it doesn't exist
    $roleName = "Directory Readers"
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
    if ($role -eq $null) {
        # Instantiate an instance of the role template
        $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq $roleName}
        Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId
        $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
    }

    # Get service principal for managed instance
    $roleMember = Get-AzureADServicePrincipal -SearchString $managedInstanceName
    $roleMember.Count
    if ($roleMember -eq $null)
    {
        Write-Output "Error: No Service Principals with name '$    ($managedInstanceName)', make sure that managedInstanceName parameter was     entered correctly."
        exit
    }
    if (-not ($roleMember.Count -eq 1))
    {
        Write-Output "Error: More than one service principal with name pattern '$    ($managedInstanceName)'"
        Write-Output "Dumping selected service principals...."
        $roleMember
        exit
    }

    # Check if service principal is already member of readers role
    $allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
    $selDirReader = $allDirReaders | where{$_.ObjectId -match     $roleMember.ObjectId}

    if ($selDirReader -eq $null)
    {
        # Add principal to readers role
        Write-Output "Adding service principal '$($managedInstanceName)' to     'Directory Readers' role'..."
        Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId     $roleMember.ObjectId
        Write-Output "'$($managedInstanceName)' service principal added to     'Directory Readers' role'..."

        #Write-Output "Dumping service principal '$($managedInstanceName)':"
        #$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
        #$allDirReaders | where{$_.ObjectId -match $roleMember.ObjectId}
    }
    else
    {
        Write-Output "Service principal '$($managedInstanceName)' is already     member of 'Directory Readers' role'."
    }
    ```

5. Po úspěšném dokončení operace následující oznámení se zobrazí v pravém horním rohu:

    ![úspěch](./media/sql-database-aad-authentication/success.png)

6. Nyní můžete zvolit správce Azure AD pro vaši Managed Instance. K tomu, na stránce Správce služby Active Directory vyberte **nastavit správce** příkazu.

    ![nastavit správce](./media/sql-database-aad-authentication/set-admin.png)

7. Na stránce Správce AAD vyhledat uživatele, vyberte uživatele nebo skupiny jako správce a pak vyberte **vyberte**.

   Na stránce správy služby Active Directory zobrazí všichni členové a skupiny služby Active Directory. Uživatelé nebo skupiny, jež jsou zobrazena šedě nelze vybrat, protože nejsou podporovány jako správce Azure AD. Zobrazit seznam podporovaných správců v [funkce Azure AD a omezení](sql-database-aad-authentication.md#azure-ad-features-and-limitations). Řízení přístupu na základě role (RBAC) se vztahuje pouze na webu Azure portal a se nerozšíří do systému SQL Server.

    ![přidat správce](./media/sql-database-aad-authentication/add-admin.png)

8. V horní části na stránce správy služby Active Directory, vyberte **Uložit**.

    ![Uložit](./media/sql-database-aad-authentication/save.png)

    Proces změny správce může trvat několik minut. Nový správce se pak objeví v poli Správce služby Active Directory.

Po zřízení správce Azure AD pro vaši Managed Instance, můžete začít vytvářet přihlášení Azure AD (**ve verzi public preview**) se <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN</a> syntaxe. Další informace najdete v tématu [Přehled služby Managed Instance](sql-database-managed-instance.md#azure-active-directory-integration).

> [!TIP]
> Chcete-li později odebrat správce, v horní části na stránce správy služby Active Directory vyberte **odebrat správce**a pak vyberte **Uložit**.

## <a name="provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server"></a>Zřízení správce Azure Active Directory pro váš server Azure SQL Database

> [!IMPORTANT]
> Následující kroky proveďte, pouze pokud zřizujete k serveru Azure SQL Database nebo datového skladu.

Následující dva postupy ukazují, jak zřídit správce Azure Active Directory pro server Azure SQL na webu Azure Portal a pomocí prostředí PowerShell.

### <a name="azure-portal"></a>portál Azure

1. V [webu Azure portal](https://portal.azure.com/), v pravém horním rohu vyberte připojení ke rozevírací seznam možných aktivního adresáře. Zvolte správné služby Active Directory jako výchozí služby Azure AD. Propojí Active Directory pro předplatné spojené s využitím Azure SQL serveru a ujistěte se, že stejné předplatné používá pro obě služby Azure AD a systému SQL Server. (Server Azure SQL lze hostování, Azure SQL Database nebo Azure SQL Data Warehouse.) ![zvolte ad][8]

2. Na panelu vlevo vyberte **všechny služby**a v okně Filtr zadejte ve **systému SQL server**. Vyberte **Sql servery**.

    ![sqlservers.PNG](media/sql-database-aad-authentication/sqlservers.png)

    >[!NOTE]
    > Na této stránce, než vyberete **SQL servery**, můžete vybrat **hvězdičky** vedle názvu *Oblíbené* kategorii a přidejte **SQL servery**na levém navigačním panelu.

3. Na **systému SQL Server** stránce **správce Active Directory**.
4. V **správce Active Directory** stránce **nastavit správce**.  ![vyberte služby active directory](./media/sql-database-aad-authentication/select-active-directory.png)  

5. V **přidat správce** stránce, vyhledat uživatele, vyberte uživatele nebo skupiny jako správce a vyberte **vyberte**. (Na stránce správy služby Active Directory zobrazí všichni členové a skupiny služby Active Directory. Uživatelé nebo skupiny, jež jsou zobrazena šedě nelze vybrat, protože nejsou podporovány jako správce Azure AD. (Viz seznam podporovaných správců v **funkce Azure AD a omezení** část [používání ověřování Azure Active Directory pro ověřování pomocí SQL Database nebo SQL Data Warehouse](sql-database-aad-authentication.md).) Řízení přístupu na základě role (RBAC) se vztahuje pouze na portálu a se nerozšíří do systému SQL Server.
    ![Vyberte správce](./media/sql-database-aad-authentication/select-admin.png)  

6. V horní části **správce Active Directory** stránce **Uložit**.
    ![Uložit správce](./media/sql-database-aad-authentication/save-admin.png)

Proces změny správce může trvat několik minut. Pak nový správce se zobrazí v **správce Active Directory** pole.

   > [!NOTE]
   > Při nastavování správce Azure AD, nový název správce (uživatel nebo skupina) už nejde použít ve virtuální hlavní databázi jako uživatel ověřování serveru SQL Server. Pokud jsou k dispozici, se nezdaří instalace správce Azure AD; vrácení zpět jeho vytvoření a označením tohoto takový správce (název) již existuje. Od tohoto systému SQL Server ověřování uživatele není součástí služby Azure AD, žádné úsilí pro připojení k serveru pomocí ověřování Azure AD se nezdaří.

Později odebrat správce, v horní části **správce Active Directory** stránce **odebrat správce**a pak vyberte **Uložit**.

### <a name="powershell"></a>PowerShell

Pokud chcete spustit rutiny prostředí PowerShell, musíte mít Azure PowerShell nainstalovaný a spuštěný. Podrobné informace najdete v tématu [Instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview). Ke zřízení správce Azure AD, spusťte následující příkazy Azure Powershellu:

- Connect-AzureRmAccount
- Select-AzureRmSubscription

Rutiny použít ke zřízení a správě správce Azure AD:

| Název rutiny | Popis |
| --- | --- |
| [Set-AzureRmSqlServerActiveDirectoryAdministrator](/powershell/module/azurerm.sql/set-azurermsqlserveractivedirectoryadministrator) |Zřídí správce Azure Active Directory pro server Azure SQL nebo Azure SQL Data Warehouse. (Musí být z aktuálního předplatného.) |
| [Remove-AzureRmSqlServerActiveDirectoryAdministrator](/powershell/module/azurerm.sql/remove-azurermsqlserveractivedirectoryadministrator) |Odebere správce Azure Active Directory pro server Azure SQL nebo Azure SQL Data Warehouse. |
| [Get-AzureRmSqlServerActiveDirectoryAdministrator](/powershell/module/azurerm.sql/get-azurermsqlserveractivedirectoryadministrator) |Vrátí informace o aktuálně nakonfigurovaný pro server Azure SQL nebo Azure SQL Data Warehouse správce Azure Active Directory. |

Použít příkaz prostředí PowerShell get-help zobrazíte další informace pro každý z těchto příkazů, třeba ``get-help Set-AzureRmSqlServerActiveDirectoryAdministrator``.

Následující skript zřídí skupinu správce Azure AD s názvem **DBA_Group** (ID objektu `40b79501-b343-44ed-9ce7-da4c8cc7353f`) pro **demo_server** server ve skupině prostředků s názvem **skupiny-23**:

```powershell
Set-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23"
-ServerName "demo_server" -DisplayName "DBA_Group"
```

**DisplayName** vstupní parametr přijímá zobrazovaný název služby Azure AD nebo hlavní název uživatele. Například ``DisplayName="John Smith"`` a ``DisplayName="johns@contoso.com"``. Zobrazovaný název skupiny Azure AD jenom služby Azure AD jsou podporovány.

> [!NOTE]
> Příkaz prostředí Azure PowerShell ```Set-AzureRmSqlServerActiveDirectoryAdministrator``` nebrání zřízení správce Azure AD pro nepodporované uživatele. Nepodporovaná uživatele je možné zřídit, ale nemůže připojit k databázi.

Následující příklad používá volitelný **ObjectID**:

```powershell
Set-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23"
-ServerName "demo_server" -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> Azure AD **ObjectID** se požaduje, pokud **DisplayName** není jedinečný. Načíst **ObjectID** a **DisplayName** hodnot, pomocí služby Active Directory části portálu Azure Classic a zobrazení vlastností uživatele nebo skupinu.

Následující příklad vrátí informace o aktuální správce Azure AD pro Azure SQL server:

```powershell
Get-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" | Format-List
```

Následující příklad odebere správce Azure AD:

```powershell
Remove-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server"
```

Správce Azure Active Directory můžete také vytvářet pomocí rozhraní REST API. Další informace najdete v tématu [operace pro operace databáze SQL Azure pro službu Azure SQL Database a Service Management REST API – referenční informace](https://msdn.microsoft.com/library/azure/dn505719.aspx)

### <a name="cli"></a>Rozhraní příkazového řádku  

Správce Azure AD můžou zřizovat také voláním následující příkazy rozhraní příkazového řádku:
| Příkaz | Popis |
| --- | --- |
|[vytvoření ad správce az sql server](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-create) |Zřídí správce Azure Active Directory pro server Azure SQL nebo Azure SQL Data Warehouse. (Musí být z aktuálního předplatného.) |
|[AZ sql server ad admin delete](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-delete) |Odebere správce Azure Active Directory pro server Azure SQL nebo Azure SQL Data Warehouse. |
|[AZ sql server správce ad seznamu](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-list) |Vrátí informace o aktuálně nakonfigurovaný pro server Azure SQL nebo Azure SQL Data Warehouse správce Azure Active Directory. |
|[aktualizace Správce služby ad az sql server](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-update) |Aktualizace správce Active Directory pro server Azure SQL nebo Azure SQL Data Warehouse. |

Další informace o příkazech rozhraní příkazového řádku najdete v tématu [SQL – az sql](https://docs.microsoft.com/cli/azure/sql/server).  

## <a name="configure-your-client-computers"></a>Konfigurace klientských počítačů

Na všechny klientské počítače, ze kterých aplikacím nebo uživatelům připojení k Azure SQL Database nebo Azure SQL Data Warehouse s využitím identit Azure AD, je třeba nainstalovat následující software:

- Rozhraní .NET framework 4.6 nebo novější z [ https://msdn.microsoft.com/library/5a4x27ek.aspx ](https://msdn.microsoft.com/library/5a4x27ek.aspx).
- Azure Active Directory Authentication Library pro SQL Server (**ADALSQL. Knihovna DLL**) je k dispozici v různých jazycích (x86 a amd64) ze služby Stažení softwaru na [Microsoft Active Directory Authentication Library pro Microsoft SQL Server](https://www.microsoft.com/download/details.aspx?id=48742).

Můžete splňovat tyto požadavky podle:

- Instalace buď [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) nebo [SQL Server Data Tools pro Visual Studio 2015](https://msdn.microsoft.com/library/mt204009.aspx) splňuje požadavek na rozhraní .NET Framework 4.6.
- Verze aplikace SSMS nainstaluje x86 **ADALSQL. Knihovna DLL**.
- Rozšíření SSDT nainstaluje verzi amd64 **ADALSQL. Knihovna DLL**.
- Nejnovější sady Visual Studio z [stahování sady Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs) splňuje požadavek na rozhraní .NET Framework 4.6, ale není možné nainstalovat požadované amd64 verzi **ADALSQL. Knihovna DLL**.

## <a name="create-contained-database-users-in-your-database-mapped-to-azure-ad-identities"></a>Vytvořte uživatele databáze s omezením v databázi namapované na identit Azure AD

>[!IMPORTANT]
>Spravovaná Instance teď podporuje přihlašování Azure AD (**ve verzi public preview**), což vám umožní vytvářet přihlášení od uživatele, skupiny nebo aplikace Azure AD. Přihlašovací údaje Azure AD umožňuje ověřování k Managed Instance bez toho databáze, který bude vytvořen jako uživatele databáze s omezením. Další informace najdete v tématu [Přehled služby Managed Instance](sql-database-managed-instance.md#azure-active-directory-integration). Syntaxe týkající se vytvoření přihlášení Azure AD, najdete v části <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN</a>.

Ověřování pomocí Azure Active Directory vyžaduje, aby uživatelé databáze má být vytvořen jako uživatele databáze s omezením. Uživatel databáze s omezením na základě Azure AD identity je uživatel databáze nemá přihlášení v hlavní databázi, a který se mapuje na identitu v adresáři Azure AD, který je přidružený k databázi. Identity Azure AD může být jednotlivý uživatelský účet nebo skupinu. Další informace o uživatele databáze s omezením najdete v tématu [uživatelé databáze s omezením vytváření přenosné databáze](https://msdn.microsoft.com/library/ff929188.aspx).

> [!NOTE]
> Nelze vytvořit uživatele databáze (s výjimkou správci) pomocí webu Azure portal. Role RBAC nejsou šířeny do systému SQL Server, SQL Database nebo SQL Data Warehouse. Role Azure RBAC se používají pro správu prostředků Azure a se nevztahují na oprávnění databáze. Například **Přispěvatel SQL serveru** role bez možnosti udělovat přístup pro připojení k SQL Database nebo SQL Data Warehouse. Přímo v databázi pomocí příkazů jazyka Transact-SQL musí být udělena oprávnění k přístupu.
> [!WARNING]
> Speciální znaky, jako je dvojtečka `:` nebo ampersand `&` obsažen jako uživatelská jména v příkazech jazyka T-SQL vytvořte přihlášení a vytvoření uživatele nejsou podporovány.

Chcete-li vytvořit Azure AD na základě obsažené databázi uživatele (jiné než správce serveru, který vlastní databáze), připojení k databázi pomocí identity Azure AD, jako uživatel s alespoň **ALTER ANY USER** oprávnění. Potom použijte následující syntaxi jazyka Transact-SQL:

```sql
CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
```

*Azure_AD_principal_name* může být hlavní název uživatele uživatele služby Azure AD nebo zobrazovaného názvu skupiny Azure AD.

**Příklady:** vytvořit databázi s omezením uživatele Azure AD představující federovaný nebo spravovaný uživatel domény:

```sql
CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;
```

Pokud chcete vytvořit uživatele databáze s omezením představující Azure AD nebo federovaného doménová skupina, zadání zobrazovaného názvu skupiny zabezpečení:

```sql
CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;
```

Chcete-li vytvořit uživatele databáze s omezením reprezentující aplikaci, která se připojuje pomocí tokenu Azure AD:

```sql
CREATE USER [appName] FROM EXTERNAL PROVIDER;
```

> [!TIP]
> Uživatele nelze vytvořit přímo z Azure Active Directory než Azure Active Directory, který je přidružený k vašemu předplatnému Azure. Pro skupiny služby Active Directory v tenantovi Active Directory můžete však přidat členy jiné aktivní adresáře, které jsou importované uživatele v přidružené služby Active Directory (označuje se jako externí uživatele). Tak, že vytvoříte uživatele databáze s omezením pro tuto skupinu AD, uživatelé z externí služby Active Directory můžete získat přístup ke službě SQL Database.

Další informace o vytváření obsažené databáze uživatelů na základě identit Azure Active Directory, naleznete v tématu [CREATE USER (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx).

> [!NOTE]
> Odebrání správce Azure Active Directory pro server Azure SQL jakéhokoli uživatele Azure AD authentication brání v připojení k serveru. Pokud je nezbytné, nepůjdou použít uživatelů Azure AD můžete vyřadit ručně správcem databáze SQL.
> [!NOTE]
> Pokud se zobrazí **vypršel časový limit připojení**, je nutné nastavit `TransparentNetworkIPResolution` parametr připojovací řetězec na hodnotu false. Další informace najdete v tématu [problém časový limit připojení pomocí rozhraní .NET Framework 4.6.1 – TransparentNetworkIPResolution](https://blogs.msdn.microsoft.com/dataaccesstechnologies/2016/05/07/connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution/).

Když vytvoříte uživatele databáze, tento uživatel obdrží **připojit** oprávnění a můžou se připojit k databázi jako člen **veřejné** role. Zpočátku to jediná oprávnění, které jsou k dispozici pro uživatele se všechna oprávnění udělená **veřejné** role nebo oprávnění udělená do kterékoli ze skupin Azure AD, že jsou členem. Jakmile zřídíte Azure uživatele databáze založené na AD obsažené, můžete udělit uživateli další oprávnění, stejným způsobem jako udělujete oprávnění pro jakýkoli jiný typ uživatele. Obvykle udělují oprávnění k databázovým rolím a přidání uživatelů do rolí. Další informace najdete v tématu [základy oprávnění modulu databáze](https://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx). Další informace o rolích speciální SQL Database najdete v tématu [Správa databází a přihlášení ve službě Azure SQL Database](sql-database-manage-logins.md).
Federované domény uživatelského účtu, který se importují do spravované domény jako externí uživatel, musí používat identitu spravované domény.

> [!NOTE]
> Uživatelé Azure AD jsou označeny jako metadata databáze s typem E (EXTERNAL_USER) a pro skupiny s typem X (EXTERNAL_GROUPS). Další informace najdete v tématu [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).
>

## <a name="connect-to-the-user-database-or-data-warehouse-by-using-ssms-or-ssdt"></a>Připojení k uživatelské databázi ani na datový sklad s použitím aplikace SSMS a SSDT  

Pokud chcete potvrdit, správce Azure AD je správně nastavený, připojení k **hlavní** databázi s použitím účtu správce Azure AD.
Ke zřízení založené na AD obsažené databázi uživatele Azure (jiné než správce serveru, který vlastní databáze), připojení k databázi pomocí identity Azure AD, který má přístup k databázi.

> [!IMPORTANT]
> Podpora pro ověřování Azure Active Directory je k dispozici [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) a [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) v sadě Visual Studio 2015. Srpen 2016 vydaná verze SSMS zahrnuje také podporu pro univerzálního ověřování Active Directory, které správcům umožňují vyžadovat Vícefaktorové ověřování pomocí telefonního hovoru, textové zprávy, čipové karty s PIN kódu nebo oznámení přes mobilní aplikaci. Pomocí služby Azure AD přihlášení a uživatele (**ve verzi public preview**) s rozšířením SSDT v tuto chvíli nepodporuje.

## <a name="using-an-azure-ad-identity-to-connect-using-ssms-or-ssdt"></a>Pomocí identity Azure AD a připojte se pomocí aplikace SSMS a SSDT

Následující postupy ukazují, jak se připojit k databázi SQL pomocí identity Azure AD pomocí SQL Server Management Studio nebo SQL Server Database Tools.

>[!IMPORTANT]
>Pomocí služby Azure AD přihlášení a uživatele (**ve verzi public preview**) s rozšířením SSDT v tuto chvíli nepodporuje.

### <a name="active-directory-integrated-authentication"></a>Integrované ověřování Active Directory

Tuto metodu použijte, pokud jsou přihlášení k Windows pomocí přihlašovacích údajů Azure Active Directory z federovanou doménu.

1. Spuštění Management Studio nebo nástroje pro Data a **připojit k serveru** (nebo **připojit k databázovému stroji**) v dialogovém **ověřování** vyberte  **Active Directory – integrované**. Žádné heslo je potřeba, nebo můžete zadat, protože se zobrazí vaše stávající přihlašovací údaje pro připojení.

    ![Zvolte možnost ověření integrované AD][11]
2. Vyberte **možnosti** tlačítko a na **vlastnosti připojení** stránku, **připojit k databázi** zadejte název uživatelské databáze, kterou chcete připojit k. ( **ID tenanta nebo název domény AD**"možnost je podporována pouze pro **univerzální s připojením MFA** možnosti, jinak ho zašedlé.)  

    ![Vyberte název databáze][13]

## <a name="active-directory-password-authentication"></a>Ověřování hesla služby Active Directory

Tuto metodu použijte, pokud připojení s názvem instančního objektu Azure AD pomocí služby Azure AD spravované domény. Můžete také použít jej pro federované účty bez přístupu k doméně, například když pracujete vzdáleně.

Tuto metodu použijte k ověření do SQL DB/DW s Azure AD pro nativní nebo federovaných uživatelů Azure AD. Nativní uživatelská je jednou explicitně vytvořena ve službě Azure AD a ověřuje pomocí uživatelského jména a hesla, zatímco federovaného uživatele se Windows uživatele má doménu je Federovaná pomocí služby Azure AD. Druhá metoda (s použitím uživatele a heslo) lze použít, když chce uživatel použít svoje přihlašovací údaje systému windows, ale jejich místní počítač není připojený k doméně (například pomocí vzdáleného přístupu). V tomto případě uživatel Windows můžete určit jejich účet domény a heslo a může ověřit do SQL DB/DW pomocí federovaného pověření.

1. Spuštění Management Studio nebo nástroje pro Data a **připojit k serveru** (nebo **připojit k databázovému stroji**) v dialogovém **ověřování** vyberte  **Active Directory – heslo**.
2. V **uživatelské jméno** zadejte jméno uživatele Azure Active Directory ve formátu **username@domain.com**. Uživatelská jména musí být účet ze služby Azure Active Directory nebo účet z domény provést federaci se službou Azure Active Directory.
3. V **heslo** zadejte vaše uživatelské heslo pro účet služby Azure Active Directory nebo federované účet domény.

    ![Vyberte metodu ověřování hesla služby AD][12]
4. Vyberte **možnosti** tlačítko a na **vlastnosti připojení** stránku, **připojit k databázi** zadejte název uživatelské databáze, kterou chcete připojit k. (Viz obrázek v předchozí způsob).

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>Pomocí identity Azure AD pro připojení z klientské aplikace

Následující postupy ukazují, jak se připojit k SQL database pomocí identity Azure AD z klientské aplikace.

### <a name="active-directory-integrated-authentication"></a>Integrované ověřování Active Directory

Aby využíval integrované ověřování Windows, musí být vaší domény služby Active Directory sdružených se službou Azure Active Directory. Klientské aplikace (nebo služba) s připojením k databázi, musí běžet na počítači připojeném k doméně v části přihlašovací údaje uživatele domény.

Pro připojení k databázi pomocí integrovaného ověřování a identitu služby Azure AD, ověření – klíčové slovo připojovacího řetězce databáze musí být nastavena na Active Directory Integrated. Následující vzorový kód jazyka C# používá ADO .NET.

```C#
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Klíčové slovo připojovacího řetězce ``Integrated Security=True`` není podporována pro připojení ke službě Azure SQL Database. Při vytváření připojení rozhraní ODBC, je potřeba odebrat mezery a nastavte ověřování na "ActiveDirectoryIntegrated".

### <a name="active-directory-password-authentication"></a>Ověřování hesla služby Active Directory

Pro připojení k databázi pomocí integrovaného ověřování a identitu služby Azure AD, musí být nastavena – klíčové slovo ověřování hesla Active Directory. Připojovací řetězec musí obsahovat uživatelské ID nebo UID a hesla/PWD klíčová slova a hodnoty. Následující vzorový kód jazyka C# používá ADO .NET.

```C#
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Další informace o metodách ověřování Azure AD pomocí ukázky k dispozici na ukázku [ukázka Githubu ověřování Azure AD](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth).

## <a name="azure-ad-token"></a>Tokenu Azure AD

Tato metoda ověřování umožňuje střední vrstvy služeb pro připojení k Azure SQL Database nebo Azure SQL Data Warehouse s získání tokenu z Azure Active Directory (AAD). Umožňuje složité scénáře, včetně ověřování prostřednictvím certifikátu. Je třeba provést čtyři základní kroky pro použití ověřování tokenu Azure AD:

1. Registrace aplikace pomocí Azure Active Directory a získat id klienta vašeho kódu.
2. Vytvořte uživatele databáze představující aplikace. (Dokončení výše v kroku 6.)
3. Vytvoření certifikátu na počítače spuštění klienta aplikace.
4. Přidání certifikátu jako klíč pro vaši aplikaci.

Ukázka připojovací řetězec:

```c#
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
connection.AccessToken = "Your JWT token"
conn.Open();
```

Další informace najdete v tématu [blogu k SQL serveru zabezpečení](https://blogs.msdn.microsoft.com/sqlsecurity/2016/02/09/token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/). Informace o přidání certifikátu najdete v tématu [Začínáme s ověřováním na základě certifikátů v Azure Active Directory](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).

### <a name="sqlcmd"></a>sqlcmd

Následující příkazy, připojte se pomocí sqlcmd, který je k dispozici od verze 13.1 [Download Center](https://go.microsoft.com/fwlink/?LinkID=825643).

```cmd
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
