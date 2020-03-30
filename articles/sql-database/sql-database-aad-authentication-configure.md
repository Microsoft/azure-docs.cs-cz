---
title: Konfigurace ověřování Azure Active Directory
description: Zjistěte, jak se připojit k databázi SQL Database, spravované instanci a datovému skladu SQL pomocí azure active directory authentication – po konfiguraci Azure AD.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: data warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, carlrab
ms.date: 01/07/2020
ms.openlocfilehash: 881c7076d5131746c730757a07da6fb938429c38
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80125044"
---
# <a name="configure-and-manage-azure-active-directory-authentication-with-sql"></a>Konfigurace a správa ověřování Azure Active Directory pomocí SQL

Tento článek ukazuje, jak vytvořit a naplnit Azure AD a potom použít Azure AD s Azure [SQL Database](sql-database-technical-overview.md), [spravované instance](sql-database-managed-instance.md)a SQL [Data Warehouse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Přehled najdete v tématu [Azure Active Directory Authentication](sql-database-aad-authentication.md).

> [!NOTE]
> Tento článek se vztahuje na Server Azure SQL a databáze SQL Database a SQL Data Warehouse, které jsou vytvořené na serveru Azure SQL. Pro zjednodušení se SQL Database používá k označení SQL Database i SQL Data Warehouse.

> [!IMPORTANT]  
> Připojení k SQL Serveru spuštěnéna na virtuálním počítači Azure není podporované pomocí účtu Azure Active Directory. Místo toho použijte účet služby Active Directory.

## <a name="create-and-populate-an-azure-ad"></a>Vytvoření a naplnění azure ad

Vytvořte Azure AD a naplňte ho uživateli a skupinami. Azure AD může být počáteční spravovaná doména Azure AD. Azure AD může být také místní služba Active Directory Domain Services, která je federovaná se službou Azure AD.

Další informace najdete v tématech [Integrování místních identit do služby Azure Active Directory](../active-directory/hybrid/whatis-hybrid-identity.md), [Přidání vlastního názvu domény do Azure AD](../active-directory/active-directory-domains-add-azure-portal.md), [Microsoft Azure podporuje federaci s Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/), [Správa adresáře služby Azure AD](../active-directory/fundamentals/active-directory-administer.md), [Správa služby Azure AD pomocí rozhraní Windows PowerShell](/powershell/azure/overview) a [Porty a protokoly, které vyžaduje hybridní identita](../active-directory/hybrid/reference-connect-ports.md).

## <a name="associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Přiřazení nebo přidání předplatného Azure do služby Azure Active Directory

1. Přidružte své předplatné Azure ke službě Azure Active Directory tím, že z adresáře uděláte důvěryhodný adresář pro předplatné Azure hostující databázi. Podrobnosti najdete v tématu [Jak jsou předplatná Azure přidružená k Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

2. Pomocí přepínače adresářů na webu Azure Portal přepněte na předplatné přidružené k doméně.

   > [!IMPORTANT]
   > Každé předplatné služby Azure má vztah důvěryhodnosti s instancí služby Azure AD. To znamená, že tomuto adresáři svěřuje ověřování uživatelů, služeb i zařízení. Několik předplatných může důvěřovat stejnému adresáři, ale jedno předplatné důvěřuje pouze jednomu adresáři. Vztah důvěryhodnosti, který má předplatné s adresářem, se liší od vztahu, který má předplatné se všemi ostatními prostředky ve službě Azure (webové stránky, databáze apod.), což jsou pro předplatné spíše podřízené prostředky. Pokud platnost předplatného vyprší, zastaví se i přístup k těmto dalším prostředkům přidruženým k předplatnému. Adresář však ve službě Azure zůstane a vy k němu můžete přidružit jiné předplatné a pokračovat ve správě uživatelů adresáře. Další informace o prostředcích najdete [v tématu Principy přístupu k prostředkům v Azure](../active-directory/active-directory-b2b-admin-add-users.md). Další informace o tomto důvěryhodném vztahu najdete v [tématu Jak přidružit nebo přidat předplatné Azure do služby Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="create-an-azure-ad-administrator-for-azure-sql-server"></a>Vytvoření správce Azure AD pro Azure SQL server

Každý server Azure SQL (který je hostitelem databáze SQL nebo datového skladu SQL) začíná s jedním účtem správce serveru, který je správcem celého serveru Azure SQL. Musí být vytvořen druhý správce SERVERU SQL Server, to je účet Azure AD. Tento objekt zabezpečení je vytvořen jako uživatel obsažené databáze v hlavní databázi. Jako správci jsou účty správce serveru členy **db_owner** role v každé databázi uživatelů a zadávají každou uživatelskou databázi jako uživatel **dbo.** Další informace o účtech správce serveru naleznete [v tématu Správa databází a přihlášení v Azure SQL Database](sql-database-manage-logins.md).

Při použití služby Azure Active Directory s geografickou replikací musí být správce služby Azure Active Directory nakonfigurován pro primární i sekundární servery. Pokud server nemá správce služby Azure Active Directory, přihlašuje se služba Azure Active Directory a uživatelé obdrží chybu "Nelze se připojit".

> [!NOTE]
> Uživatelé, kteří nejsou založeni na účtu Azure AD (včetně účtu správce serveru Azure SQL), nemohou vytvářet uživatele založené na Azure AD, protože nemají oprávnění k ověření navrhovaných uživatelů databáze pomocí služby Azure AD.

## <a name="provision-an-azure-active-directory-administrator-for-your-managed-instance"></a>Zřízení správce služby Azure Active Directory pro spravovanou instanci

> [!IMPORTANT]
> Postupujte pouze v případě, že zřazujete spravovanou instanci. Tuto operaci může provést jenom správce globální/společnosti nebo správce privilegovaných rolí ve službě Azure AD. Následující kroky popisují proces udělování oprávnění uživatelům s různými oprávněními v adresáři.

> [!NOTE]
> Pro správce Azure AD pro MI vytvořené před GA, ale pokračovat v provozu po GA, neexistuje žádná funkční změna stávající chování. Další informace najdete v [části Nové funkce správce Azure AD pro MI](#new-azure-ad-admin-functionality-for-mi) další podrobnosti.

Vaše spravovaná instance potřebuje oprávnění ke čtení Azure AD k úspěšnému dokončení úloh, jako je ověřování uživatelů prostřednictvím členství ve skupině zabezpečení nebo vytváření nových uživatelů. Aby to fungovalo, musíte udělit oprávnění spravované instanci ke čtení Azure AD. Existují dva způsoby, jak to udělat: z portálu a Prostředí PowerShell. Následující kroky obě metody.

1. Na webu Azure Portal v pravém horním rohu vyberte připojení a rozbalte seznam možných aktivních adresářů.

2. Zvolte správný active directory jako výchozí Azure AD.

   Tento krok propojí předplatné přidružené ke službě Active Directory se spravovanou instanci a ujistěte se, že stejné předplatné se používá pro Azure AD i spravované instance.

3. Přejděte na spravovanou instanci a vyberte ten, který chcete použít pro integraci Azure AD.

   ![aad](./media/sql-database-aad-authentication/aad.png)

4. Vyberte banner nad stránkou správce služby Active Directory a udělte oprávnění aktuálnímu uživateli. Pokud jste přihlášeni jako správce globální/společnosti ve službě Azure AD, můžete to udělat z webu Azure Portal nebo pomocí PowerShellu pomocí níže uvedeného skriptu.

    ![udělit oprávnění-portál](./media/sql-database-aad-authentication/grant-permissions.png)

    ```powershell
    # Gives Azure Active Directory read permission to a Service Principal representing the managed instance.
    # Can be executed only by a "Company Administrator", "Global Administrator", or "Privileged Role Administrator" type of user.

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
    if ($roleMember -eq $null) {
        Write-Output "Error: No Service Principals with name '$    ($managedInstanceName)', make sure that managedInstanceName parameter was     entered correctly."
        exit
    }
    if (-not ($roleMember.Count -eq 1)) {
        Write-Output "Error: More than one service principal with name pattern '$    ($managedInstanceName)'"
        Write-Output "Dumping selected service principals...."
        $roleMember
        exit
    }

    # Check if service principal is already member of readers role
    $allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
    $selDirReader = $allDirReaders | where{$_.ObjectId -match     $roleMember.ObjectId}

    if ($selDirReader -eq $null) {
        # Add principal to readers role
        Write-Output "Adding service principal '$($managedInstanceName)' to     'Directory Readers' role'..."
        Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId     $roleMember.ObjectId
        Write-Output "'$($managedInstanceName)' service principal added to     'Directory Readers' role'..."

        #Write-Output "Dumping service principal '$($managedInstanceName)':"
        #$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
        #$allDirReaders | where{$_.ObjectId -match $roleMember.ObjectId}
    }
    else {
        Write-Output "Service principal '$($managedInstanceName)' is already     member of 'Directory Readers' role'."
    }
    ```

5. Po úspěšném dokončení operace se v pravém horním rohu zobrazí následující oznámení:

    ![úspěch](./media/sql-database-aad-authentication/success.png)

6. Teď si můžete vybrat správce Azure AD pro spravovanou instanci. Na stránce správce služby Active Directory vyberte nastavit příkaz **správce.**

    ![set-admin](./media/sql-database-aad-authentication/set-admin.png)

7. Na stránce správce Služby AAD vyhledejte uživatele, vyberte uživatele nebo skupinu jako správce a pak vyberte **Vybrat**.

   Na stránce správce služby Active Directory jsou zobrazeni všichni členové a skupiny služby Active Directory. Uživatele nebo skupiny, které jsou zašedlé, nelze vybrat, protože nejsou podporovány jako správci Azure AD. Podívejte se na seznam podporovaných správců ve [službě Azure AD funkce a omezení](sql-database-aad-authentication.md#azure-ad-features-and-limitations). Řízení přístupu na základě rolí (RBAC) platí jenom pro portál Azure a není rozšířena na SQL Server.

    ![Přidání správce služby Azure Active Directory](./media/sql-database-aad-authentication/add-azure-active-directory-admin.png)

8. V horní části stránky správce služby Active Directory vyberte **Uložit**.

    ![save](./media/sql-database-aad-authentication/save.png)

    Proces změny správce může trvat několik minut. Poté se nový správce zobrazí v poli správce služby Active Directory.

Po zřízení správce Azure AD pro spravovanou instanci, můžete začít vytvářet objekty serveru Azure AD (přihlášení) s <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">create login</a> syntaxe. Další informace naleznete v tématu [přehled spravovaných instancí](sql-database-managed-instance.md#azure-active-directory-integration).

> [!TIP]
> Pokud chcete později správce odebrat, vyberte v horní části stránky správce služby Active Directory **možnost Odebrat správce**a pak vyberte **Uložit**.

### <a name="new-azure-ad-admin-functionality-for-mi"></a>Nové funkce správce Azure AD pro MI

Níže uvedená tabulka shrnuje funkce pro veřejné náhled azure ad přihlášení správce pro MI, versus nové funkce dodané s GA pro přihlášení Azure AD.

| Správce přihlášení Azure AD pro MI během veřejné verze Preview | Funkce GA pro správce Azure AD pro MI |
| --- | ---|
| Chová se podobně jako správce Azure AD pro SQL Database, který umožňuje ověřování Azure AD, ale správce Azure AD nemůže vytvořit přihlášení Azure AD nebo SQL v hlavním db pro MI. | Správce Azure AD má oprávnění sysadmin a můžete vytvořit AAD a SQL přihlášení v hlavním db pro MI. |
| Není k dispozici v zobrazení sys.server_principals | Je přítomen v zobrazení sys.server_principals |
| Umožňuje jednotlivým uživatelům typu Host Azure AD nastavit jako správce Azure AD pro MI. Další informace najdete v tématu [Přidání uživatelů spolupráce Služby Azure Active Directory B2B na webu Azure Portal](../active-directory/b2b/add-users-administrator.md). | Vyžaduje vytvoření skupiny Azure AD s uživateli typu Host jako členy k nastavení této skupiny jako správce Azure AD pro MI. Další informace najdete [v tématu Azure AD business to business support](sql-database-ssms-mfa-authentication.md#azure-ad-business-to-business-support). |

Jako osvědčený postup pro stávající správce Azure AD pro MI vytvořené před GA a stále provozní post GA, resetovat správce Azure AD pomocí webu Azure Portal "Odebrat správce" a "Nastavit správce" možnost pro stejné hostova uživatele nebo skupiny Azure AD.

### <a name="known-issues-with-the-azure-ad-login-ga-for-mi"></a>Známé problémy s Ga přihlášení Azure AD pro MI

- Pokud přihlášení Azure AD existuje v hlavní databázi pro MI, `CREATE LOGIN [myaadaccount] FROM EXTERNAL PROVIDER`vytvořené pomocí příkazu T-SQL , nelze nastavit jako správce Azure AD pro MI. K vytvoření přihlášení azure ad pomocí příkazů Azure Portal, PowerShell nebo CLI dojde k chybě nastavení přihlášení jako správce Azure AD pomocí příkazů Azure Portal, PowerShell nebo CLI k vytvoření přihlášení Azure AD.
  - Přihlášení musí být vynechána v hlavní `DROP LOGIN [myaadaccount]`databázi pomocí příkazu , před účet lze vytvořit jako správce Azure AD.
  - Nastavte účet správce Azure AD na webu `DROP LOGIN` Azure Portal po úspěšném úspěchu. 
  - Pokud nemůžete nastavit účet správce Azure AD, zkontrolujte v hlavní databázi spravované instance pro přihlášení. Použijte následující příkaz:`SELECT * FROM sys.server_principals`
  - Nastavení správce Azure AD pro MI automaticky vytvoří přihlášení v hlavní databázi pro tento účet. Odebrání správce Azure AD automaticky klesne přihlášení z hlavní databáze.

- Jednotliví uživatelé typu Host Azure AD nejsou podporováni jako správci Azure AD pro MI. Uživatelé typu Host musí být součástí skupiny Azure AD, která má být nastavena jako správce Azure AD. V současné době okno portálu Azure není šedivý uživatele typu Host pro jiné Azure AD, umožňuje uživatelům pokračovat v nastavení správce. Uložení uživatelů typu Host jako správce Azure AD způsobí selhání nastavení.
  - Pokud chcete, aby uživatel hosta správce Azure AD pro MI, zahrnout uživatele hosta ve skupině Azure AD a nastavit tuto skupinu jako správce Azure AD.

### <a name="powershell-for-sql-managed-instance"></a>Instance spravovaná prostředím PowerShell pro SQL

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Chcete-li spustit rutiny prostředí PowerShell, musíte mít nainstalovaný a spuštěný Azure PowerShell. Podrobné informace najdete v tématu [Instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview).

> [!IMPORTANT]
> Modul Správce prostředků Azure (RM) prostředí PowerShell je stále podporovaný službou Azure SQL Database, ale veškerý budoucí vývoj je pro modul Az.Sql. Modul AzureRM bude nadále dostávat opravy chyb nejméně do prosince 2020.  Argumenty pro příkazy v modulu Az a v modulech AzureRm jsou v podstatě identické. Další informace o jejich kompatibilitě [najdete v tématu Představení nového modulu Azure PowerShell Az](/powershell/azure/new-azureps-module-az).

Pokud chcete zřídit správce Azure AD, spusťte následující příkazy Azure PowerShellu:

- Účet Connect-Az
- Select-AzSubscription

Rutiny používané ke zřizování a správě správce Azure AD pro spravovanou instanci SQL:

| Název rutiny | Popis |
| --- | --- |
| [Správce služby ActiveDirectory set-azsqlinstance](/powershell/module/az.sql/set-azsqlinstanceactivedirectoryadministrator) |Zřídí správce Azure AD pro spravovanou instanci SQL v aktuálním předplatném. (Musí být z aktuálního předplatného)|
| [Odebrat správce služby AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlinstanceactivedirectoryadministrator) |Odebere správce Azure AD pro spravovanou instanci SQL v aktuálním předplatném. |
| [Správce služby Get-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlinstanceactivedirectoryadministrator) |Vrátí informace o správci Azure AD pro instanci spravované SQL v aktuálním předplatném.|

Následující příkaz získá informace o správci služby Azure AD pro spravovanou instanci s názvem ManagedInstance01, která je přidružena ke skupině prostředků s názvem ResourceGroup01.

```powershell
Get-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01"
```

Následující příkaz zřídí skupinu správců Azure AD s názvem DBA pro spravovanou instanci s názvem ManagedInstance01. Tento server je přidružen ke skupině prostředků ResourceGroup01.

```powershell
Set-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01" -DisplayName "DBAs" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353b"
```

Následující příkaz odebere správce Služby Azure AD pro spravovanou instanci s názvem ManagedInstanceName01 přidruženou ke skupině prostředků ResourceGroup01.

```powershell
Remove-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstanceName01" -Confirm -PassThru
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Můžete také zřídit správce Azure AD pro spravanou instanci SQL voláním následujících příkazů rozhraní příkazového příkazu:

| Příkaz | Popis |
| --- | --- |
|[az SQL mi ad-admin vytvořit](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-create) | Zřídí správce služby Azure Active Directory pro spravovanou instanci SQL. (Musí být z aktuálního předplatného) |
|[az SQL MI ad-admin odstranit](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-delete) | Odebere instanci spravované službou Azure Active Directory pro sql spravované instance. |
|[az sql mi ad-admin seznam](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-list) | Vrátí informace o správci služby Azure Active Directory, který je aktuálně nakonfigurovaný pro spravovanou instanci SQL. |
|[az sql mi ad-admin aktualizace](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-update) | Aktualizuje správce služby Active Directory pro instanci spravované jazykem SQL. |

Další informace o příkazech příkazu příkazu PŘÍKAZU CLI naleznete [v tématu az sql mi](/cli/azure/sql/mi).

* * *

## <a name="provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server"></a>Zřízení správce služby Azure Active Directory pro váš databázový server Azure SQL

> [!IMPORTANT]
> Postupujte jenom v případě, že zřazujete server Azure SQL Database nebo datový sklad.

Následující dva postupy ukazují, jak zřídit správce Služby Azure Active Directory pro váš server Azure SQL na webu Azure Portal a pomocí PowerShellu.

### <a name="azure-portal"></a>portál Azure

1. Na [webu Azure Portal](https://portal.azure.com/)vpravém horním rohu vyberte připojení a rozbalte seznam možných aktivních adresářů. Zvolte správný active directory jako výchozí Azure AD. Tento krok propojí službu Active Directory přidruženou k předplatnému se serverem Azure SQL a ujistěte se, že se stejné předplatné používá pro Azure AD i SQL Server. (Server Azure SQL může hostovat buď Azure SQL Database, nebo Azure SQL Data Warehouse.)

    ![vybrat-ad][8]

2. Vyhledejte a vyberte **server SQL**.

    ![Hledání a výběr serverů SQL](media/sql-database-aad-authentication/search-for-and-select-sql-servers.png)

    >[!NOTE]
    > Na této stránce můžete před výběrem **serverů SQL**vybrat **hvězdičku** vedle názvu, která kategorie *uvádí jako oblíbenou,* a přidat **servery SQL** na levý navigační panel.

3. Na stránce **SERVERU SQL Server** vyberte **položku Správce služby Active Directory**.

4. Na stránce **správce služby Active Directory** vyberte Nastavit **správce**.

    ![Servery SQL nastavují správce služby Active Directory](./media/sql-database-aad-authentication/sql-servers-set-active-directory-admin.png)  

5. Na stránce **Přidat správce** vyhledejte uživatele, vyberte uživatele nebo skupinu jako správce a pak vyberte **Vybrat**. (Stránka správce služby Active Directory zobrazuje všechny členy a skupiny služby Active Directory. Uživatele nebo skupiny, které jsou zašedlé nelze vybrat, protože nejsou podporovány jako správci Azure AD. (Podívejte se na seznam podporovaných správců v části **Funkce a omezení Azure AD** v části Použití ověřování Azure Active Directory pro ověřování pomocí databáze SQL nebo [datového skladu SQL](sql-database-aad-authentication.md).) Řízení přístupu na základě rolí (RBAC) platí pouze pro portál a není šířenna na SQL Server.

    ![Vybrat správce služby Azure Active Directory](./media/sql-database-aad-authentication/select-azure-active-directory-admin.png)  

6. V horní části stránky **správce služby Active Directory** vyberte **ULOŽIT**.

    ![uložit správce](./media/sql-database-aad-authentication/save-admin.png)

Proces změny správce může trvat několik minut. Poté se nový správce zobrazí v poli **správce služby Active Directory.**

   > [!NOTE]
   > Při nastavování správce Azure AD, nový název správce (uživatel nebo skupina) již nemůže být k dispozici v databázi virtuálního hlavního jako uživatel ověřování SQL Server. Pokud k dispozici, nastavení správce Azure AD se nezdaří; vrácení zpět jeho vytvoření a označující, že takový admin (název) již existuje. Vzhledem k tomu, že takový uživatel ověřování SQL Server není součástí Azure AD, jakékoli úsilí o připojení k serveru pomocí ověřování Azure AD nezdaří.

Pokud chcete později správce odebrat, vyberte v horní části stránky **správce služby Active Directory** možnost **Odebrat správce**a pak vyberte **Uložit**.

### <a name="powershell-for-azure-sql-database-and-azure-sql-data-warehouse"></a>PowerShell pro Azure SQL Database a Azure SQL Data Warehouse

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Chcete-li spustit rutiny prostředí PowerShell, musíte mít nainstalovaný a spuštěný Azure PowerShell. Podrobné informace najdete v tématu [Instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview). Pokud chcete zřídit správce Azure AD, spusťte následující příkazy Azure PowerShellu:

- Účet Connect-Az
- Select-AzSubscription

Rutiny používané ke zřizování a správě správce Azure AD pro Azure SQL Database a Azure SQL Data Warehouse:

| Název rutiny | Popis |
| --- | --- |
| [Správce služby Set-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlserveractivedirectoryadministrator) |Zřídí správce Azure Active Directory pro Azure SQL server nebo Azure SQL Data Warehouse. (Musí být z aktuálního předplatného) |
| [Odebrat správce služby AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlserveractivedirectoryadministrator) |Odebere správce Služby Azure Active Directory pro Azure SQL server nebo Azure SQL Data Warehouse. |
| [Správce služby Get-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator) |Vrátí informace o správci služby Azure Active Directory, který je aktuálně nakonfigurovaný pro server Azure SQL nebo datový sklad Azure SQL. |

Pomocí nápovědy příkazu PowerShell zobrazíte další informace pro každý z těchto příkazů. Například, `get-help Set-AzSqlServerActiveDirectoryAdministrator`.

Následující skript zřcuje skupinu správců Azure AD s názvem **DBA_Group** (ID `40b79501-b343-44ed-9ce7-da4c8cc7353f`objektu ) pro **server demo_server** ve skupině prostředků s názvem **Skupina-23**:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" -DisplayName "DBA_Group"
```

Vstupní parametr **DisplayName** přijímá zobrazovaný název Azure AD nebo hlavní název uživatele. Například ``DisplayName="John Smith"`` a ``DisplayName="johns@contoso.com"``. Pro skupiny Azure AD je podporován pouze zobrazovaný název Azure AD.

> [!NOTE]
> Příkaz ```Set-AzSqlServerActiveDirectoryAdministrator``` Azure PowerShell nebrání zřizování správců Azure AD pro nepodporované uživatele. Nepodporovaný uživatel může být zřízena, ale nelze se připojit k databázi.

Následující příklad používá volitelné **ObjectID**:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" `
    -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> Azure AD **ObjectID** je vyžadováno, pokud **DisplayName** není jedinečný. Chcete-li načíst hodnoty **ObjectID** a **DisplayName,** použijte část Služby Active Directory na portálu Azure Classic Portal a zobrazte vlastnosti uživatele nebo skupiny.

Následující příklad vrátí informace o aktuálním správci Azure AD pro server Azure SQL:

```powershell
Get-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" | Format-List
```

Následující příklad odebere správce Azure AD:

```powershell
Remove-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Správce Azure AD můžete zřídit voláním následujících příkazů rozhraní příkazového příkazu:

| Příkaz | Popis |
| --- | --- |
|[az sql server ad-admin vytvořit](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-create) | Zřídí správce Azure Active Directory pro Azure SQL server nebo Azure SQL Data Warehouse. (Musí být z aktuálního předplatného) |
|[az sql server ad-admin odstranit](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-delete) | Odebere správce Služby Azure Active Directory pro Azure SQL server nebo Azure SQL Data Warehouse. |
|[az sql server ad-admin seznam](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-list) | Vrátí informace o správci služby Azure Active Directory, který je aktuálně nakonfigurovaný pro server Azure SQL nebo datový sklad Azure SQL. |
|[Az sql server ad-admin aktualizace](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-update) | Aktualizuje správce služby Active Directory pro server Azure SQL nebo datový sklad Azure SQL. |

Další informace o příkazech příkazu příkazu PŘÍKAZU CLI naleznete [v tématu az sql server](/cli/azure/sql/server).

* * *

> [!NOTE]
> Správce služby Azure Active Directory můžete také zřídit pomocí rozhraní REST API. Další informace najdete v [tématu Reference rozhraní REST API služby a operace pro azure SQL database operations for Azure SQL Database Database](/rest/api/sql/)

## <a name="configure-your-client-computers"></a>Konfigurace klientských počítačů

Na všech klientských počítačích, ze kterých se vaše aplikace nebo uživatelé připojují k Azure SQL Database nebo Azure SQL Data Warehouse pomocí identit Azure AD, je nutné nainstalovat následující software:

- Rozhraní .NET Framework 4.6 nebo novější od [https://msdn.microsoft.com/library/5a4x27ek.aspx](https://msdn.microsoft.com/library/5a4x27ek.aspx).
- Azure Active Directory Authentication Library for SQL Server *(ADAL. DLL*). Níže jsou uvedeny odkazy ke stažení k instalaci nejnovější ovladač SSMS, ODBC a OLE DB, který obsahuje *ADAL. Knihovna DLL.*
    1. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)
    1. [Ovladač ODBC 17 pro SQL Server](https://www.microsoft.com/download/details.aspx?id=56567)
    1. [Ovladač OLE DB 18 pro SQL Server](https://www.microsoft.com/download/details.aspx?id=56730)

Tyto požadavky můžete splnit takto:

- Instalace nejnovější verze [sql server management studio](/sql/ssms/download-sql-server-management-studio-ssms) nebo SQL Server data [tools](/sql/ssdt/download-sql-server-data-tools-ssdt) splňuje požadavek rozhraní .NET Framework 4.6.
    - SSMS nainstaluje x86 verzi *ADAL. DLL*.
    - SSDT nainstaluje verzi AMD64 *ADAL. DLL*.
    - Nejnovější visual studio z [visual studio ke stažení](https://www.visualstudio.com/downloads/download-visual-studio-vs) splňuje požadavek rozhraní .NET Framework 4.6, ale nenainstaluje požadovanou verzi AMD64 *ADAL. DLL*.

## <a name="create-contained-database-users-in-your-database-mapped-to-azure-ad-identities"></a>Vytvoření obsahujících uživatelů databáze v databázi mapovaných na identity Azure AD

> [!IMPORTANT]
> Spravovaná instance teď podporuje objekty serveru Azure AD (přihlášení), což umožňuje vytvářet přihlášení od uživatelů Azure AD, skupin nebo aplikací. Objekty serveru Azure AD (přihlášení) poskytuje možnost ověření spravované instance bez nutnosti vytvářet uživatele databáze jako uživatele s obsahem databáze. Další informace naleznete v tématu [Přehled spravované instance](sql-database-managed-instance.md#azure-active-directory-integration). Syntaxe při vytváření objektů zabezpečení serveru Azure AD (přihlášení) najdete v tématu <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN</a>.

Ověřování azure active directory vyžaduje, aby uživatelé databáze byli vytvořeni jako uživatelé obsahující databáze. Obsažený uživatel databáze na základě identity Azure AD, je uživatel databáze, který nemá přihlášení v hlavní databázi a který se mapuje na identitu v adresáři Azure AD, který je přidružen k databázi. Identita Azure AD může být individuální uživatelský účet nebo skupina. Další informace o obsažených uživatelích databáze naleznete [v tématu Contained Database Users- Making Your Database Portable](https://msdn.microsoft.com/library/ff929188.aspx).

> [!NOTE]
> Uživatelé databáze (s výjimkou správců) nelze vytvořit pomocí portálu Azure. Role RBAC nejsou rozšířeny na SQL Server, SQL Database nebo SQL Data Warehouse. Role Azure RBAC se používají ke správě prostředků Azure a nevztahují se na oprávnění databáze. Například role **přispěvatele serveru SQL Server** neuděluje přístup k připojení k databázi SQL nebo datovému skladu SQL. Přístupová oprávnění musí být udělena přímo v databázi pomocí příkazů Transact-SQL.

> [!WARNING]
> Speciální znaky, `:` jako dvojtečka nebo ampersand, `&` pokud jsou zahrnuty jako uživatelská jména v T-SQL vytvořit přihlášení a vytvořit user příkazy nejsou podporovány.

Chcete-li vytvořit uživatele obsahující databázi založené na Azure AD (jiné než správce serveru, který databázi vlastní), připojte se k databázi s identitou Azure AD jako uživatel s alespoň **oprávněním ALTER ANY USER.** Potom použijte následující syntaxi Transact-SQL:

```sql
CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
```

*Azure_AD_principal_name* může být hlavní název uživatele Azure AD nebo zobrazovaný název pro skupinu Azure AD.

**Příklady:** Vytvoření uživatele s obsaženou databází představujícího federovaného nebo spravovaného uživatele domény služby Azure AD:

```sql
CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;
```

Chcete-li vytvořit uživatele obsahující databáze představujícího skupinu Azure AD nebo federované domény, zadejte zobrazovaný název skupiny zabezpečení:

```sql
CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;
```

Chcete-li vytvořit obsažené databáze uživatele představující aplikaci, která se připojuje pomocí tokenu Azure AD:

```sql
CREATE USER [appName] FROM EXTERNAL PROVIDER;
```

> [!NOTE]
> Tento příkaz vyžaduje, aby SQL access Azure AD (dále jen "externí zprostředkovatel") jménem přihlášeného uživatele. Někdy nastanou okolnosti, které způsobí, že Azure AD vrátit výjimku zpět do SQL. V těchto případech se uživateli zobrazí chyba SQL 33134, která by měla obsahovat chybovou zprávu specifickou pro AAD. Většinu času chyba bude říkat, že přístup je odepřen, nebo že uživatel musí zaregistrovat vícefaktorové ověřování pro přístup k prostředku, nebo že přístup mezi aplikacemi první strany musí být zpracovány prostřednictvím předběžné autorizace. V prvních dvou případech je problém obvykle způsoben zásady podmíněného přístupu, které jsou nastaveny v tenantovi AAD uživatele: brání uživateli v přístupu k externímu zprostředkovateli. Problém by měla vyřešit aktualizace zásad certifikační autority umožňující přístup k aplikaci '00000002-0000-0000-c000-0000000000000000000' (ID aplikace rozhraní API aad graphu). V případě, že chyba říká, že přístup mezi aplikacemi první strany musí být zpracovány prostřednictvím předběžné autorizace, problém je, protože uživatel je přihlášen jako instanční objekt. Příkaz by měl být úspěšný, pokud je místo toho proveden uživatelem.

> [!TIP]
> Přímo nemůžete vytvořit uživatele z jiné služby Azure Active Directory než služby Azure Active Directory, která je přidružená k vašemu předplatnému Azure. Členové jiných aktivních adresářů, kteří jsou importováni uživateli v přidružené službě Active Directory (označované jako externí uživatelé), však mohou být přidáni do skupiny Active Directory ve službě Active Directory v klientovi. Vytvořením obsaženého databázového uživatele pro tuto skupinu služby AD mohou uživatelé z externí služby Active Directory získat přístup k databázi SQL Database.

Další informace o vytváření uživatelů obsahujících databází na základě identit služby Azure Active Directory naleznete v tématu [CREATE USER (Transact-SQL).](https://msdn.microsoft.com/library/ms173463.aspx)

> [!NOTE]
> Odebráním správce služby Azure Active Directory pro server Azure SQL zabráníte jakémukoli uživateli ověřování Azure AD v připojení k serveru. V případě potřeby mohou být uživatelé azure ad ručně vynecháni správcem databáze SQL.

> [!NOTE]
> Pokud obdržíte **časový limit připojení vypršela**, `TransparentNetworkIPResolution` bude pravděpodobně nutné nastavit parametr připojovacího řetězce na false. Další informace naleznete v [tématu Connection timeout issue with .NET Framework 4.6.1 - TransparentNetworkIPResolution](https://blogs.msdn.microsoft.com/dataaccesstechnologies/20../../connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution/).

Při vytváření uživatele databáze tento uživatel obdrží oprávnění **CONNECT** a může se k této databázi připojit jako člen role **PUBLIC.** Zpočátku pouze oprávnění, která jsou k dispozici pro uživatele jsou všechna oprávnění udělená roli **PUBLIC** nebo všechna oprávnění udělená všem skupinám Azure AD, které jsou členem. Jakmile zřídíte uživatele obsahující ho databáze založeného na Azure AD, můžete uživateli udělit další oprávnění, stejně jako udělíte oprávnění jakémukoli jinému typu uživatele. Obvykle udělte oprávnění k databázovým rolím a přidejte uživatele do rolí. Další informace naleznete v [tématu Základy oprávnění databázového stroje](https://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx). Další informace o speciálních rolích databáze SQL najdete [v tématu Správa databází a přihlášení v Azure SQL Database](sql-database-manage-logins.md).
Federovaný uživatelský účet domény, který je importován do spravované domény jako externí uživatel, musí používat identitu spravované domény.

> [!NOTE]
> Uživatelé Azure AD jsou označeny v metadatech databáze typem E (EXTERNAL_USER) a pro skupiny s typem X (EXTERNAL_GROUPS). Další informace naleznete [v tématu sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).

## <a name="connect-to-the-user-database-or-data-warehouse-by-using-ssms-or-ssdt"></a>Připojení k uživatelské databázi nebo datovému skladu pomocí SSMS nebo SSDT  

Chcete-li potvrdit, že správce Azure AD je správně nastaven, připojte se k **hlavní** databázi pomocí účtu správce Azure AD.
Chcete-li zřídit uživatele obsahující databázi založené na Azure AD (jiné než správce serveru, který databázi vlastní), připojte se k databázi s identitou Azure AD, která má přístup k databázi.

> [!IMPORTANT]
> Podpora ověřování Azure Active Directory je dostupná v [centru SQL Serveru 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) a SQL Server Data [Tools](https://msdn.microsoft.com/library/mt204009.aspx) ve Visual Studiu 2015. Verze SSMS ze srpna 2016 také zahrnuje podporu univerzálního ověřování služby Active Directory, která umožňuje správcům vyžadovat vícefaktorové ověřování pomocí telefonního hovoru, textové zprávy, čipových karet s pinem nebo oznámení mobilní aplikace.

## <a name="using-an-azure-ad-identity-to-connect-using-ssms-or-ssdt"></a>Použití identity Azure AD pro připojení pomocí SSMS nebo SSDT

Následující postupy ukazují, jak se připojit k databázi SQL s identitou Azure AD pomocí SQL Server Management Studio nebo SQL Server Databázové nástroje.

### <a name="active-directory-integrated-authentication"></a>Integrované ověřování služby Active Directory

Tuto metodu použijte, pokud jste přihlášeni k Systému Windows pomocí pověření služby Azure Active Directory z federované domény.

1. Spusťte aplikaci Management Studio nebo datové nástroje a v dialogovém okně **Připojit k serveru** (nebo Připojit k **databázovému stroji)** vyberte v poli **Ověřování** **položku Active Directory - Integrated**. Není potřeba žádné heslo nebo je nelze zadat, protože pro připojení budou zobrazena vaše stávající pověření.

    ![Výběr integrovaného ověřování služby AD][11]

2. Vyberte tlačítko **Možnosti** a na stránce **Vlastnosti připojení** zadejte do pole **Připojit k databázi** název uživatelské databáze, ke které se chcete připojit. (Možnost **Název domény služby AD nebo ID klienta**je podporována pouze pro univerzální s možnostmi **připojení MFA,** jinak je zašedlá.)  

    ![Vyberte název databáze][13]

## <a name="active-directory-password-authentication"></a>Ověřování heslem služby Active Directory

Tuto metodu použijte při připojování s hlavním názvem Azure AD pomocí spravované domény Azure AD. Můžete jej také použít pro federované účty bez přístupu k doméně, například při vzdálené práci.

Pomocí této metody můžete ověřit sql DB/DW pomocí Azure AD pro nativní nebo federované uživatele Azure AD. Nativní uživatel je jeden explicitně vytvořené ve službě Azure AD a ověřování pomocí uživatelského jména a hesla, zatímco federovaný uživatel je uživatel windows, jehož doména je federované s Azure AD. Druhá metoda (pomocí uživatelského & heslo) lze použít, když uživatel chce použít své přihlašovací údaje systému Windows, ale jejich místní počítač není spojen s doménou (například pomocí vzdáleného přístupu). V takovém případě může uživatel systému Windows označit svůj účet domény a heslo a může se ověřit na SQL DB/DW pomocí federovaných pověření.

1. Spusťte aplikaci Management Studio nebo datové nástroje a v dialogovém okně **Připojit k serveru** (nebo Připojit k **databázovému stroji)** vyberte v poli **Ověřování** **položku Active Directory - Heslo**.

2. Do pole **Uživatelské jméno** zadejte uživatelské jméno služby Azure Active Directory do uživatelského **jména\@** ve formátu domain.com . Uživatelská jména musí být účet ze služby Azure Active Directory nebo účet z domény federate s Azure Active Directory.

3. Do pole **Heslo** zadejte uživatelské heslo pro účet Služby Azure Active Directory nebo účet federované domény.

    ![Výběr ověřování hesla služby AD][12]

4. Vyberte tlačítko **Možnosti** a na stránce **Vlastnosti připojení** zadejte do pole **Připojit k databázi** název uživatelské databáze, ke které se chcete připojit. (Viz obrázek v předchozí volbě.)

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>Použití identity Azure AD pro připojení z klientské aplikace

Následující postupy ukazují, jak se připojit k databázi SQL s identitou Azure AD z klientské aplikace.

### <a name="active-directory-integrated-authentication"></a>Integrované ověřování služby Active Directory

Chcete-li používat integrované ověřování systému Windows, musí být služba Active Directory vaší domény federována službou Azure Active Directory. Klientská aplikace (nebo služba) připojující se k databázi musí být spuštěna v počítači přilehlém k doméně pod pověřeními domény uživatele.

Chcete-li se připojit k databázi pomocí integrovaného ověřování a identity Azure AD, musí být klíčové slovo Ověřování v připojovacím řetězci databáze nastaveno na integrovanou službu Active Directory. Následující ukázka kódu jazyka C# používá rozhraní ADO .NET.

```csharp
string ConnectionString = @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Klíčové slovo `Integrated Security=True` připojovacího řetězce není podporováno pro připojení k Azure SQL Database. Při vytváření připojení ODBC bude nutné odebrat mezery a nastavit ověřování na možnost ActiveDirectoryIntegrated.

### <a name="active-directory-password-authentication"></a>Ověřování heslem služby Active Directory

Chcete-li se připojit k databázi pomocí integrovaného ověřování a identity Azure AD, musí být klíčové slovo Ověřování nastaveno na heslo služby Active Directory. Připojovací řetězec musí obsahovat klíčová slova a hodnoty ID uživatele/UID a hesla/PWD. Následující ukázka kódu jazyka C# používá rozhraní ADO .NET.

```csharp
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Další informace o metodách ověřování Azure AD pomocí ukázkových ukázek ukázkových kódů dostupných na [ukázce GitHub u azure a distentizace ověřování.](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth)

## <a name="azure-ad-token"></a>Token Azure AD

Tato metoda ověřování umožňuje službám střední vrstvy připojit se k Azure SQL Database nebo Azure SQL Data Warehouse získáním tokenu z Azure Active Directory (AAD). Umožňuje sofistikované scénáře včetně ověřování na základě certifikátu. Musíte dokončit čtyři základní kroky k použití ověřování tokenu Azure AD:

1. Zaregistrujte svou aplikaci ve službě Azure Active Directory a získejte ID klienta pro váš kód.
2. Vytvořte databázového uživatele představujícího aplikaci. (Dokončeno dříve v kroku 6.)
3. Vytvořte certifikát v klientském počítači spustí aplikaci.
4. Přidejte certifikát jako klíč pro vaši aplikaci.

Ukázkový připojovací řetězec:

```csharp
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
conn.AccessToken = "Your JWT token"
conn.Open();
```

Další informace naleznete v tématu [SQL Server Security Blog](https://blogs.msdn.microsoft.com/sqlsecurity/20../../token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/). Informace o přidání certifikátu [najdete v tématu Začínáme s ověřováním na základě certifikátu ve službě Azure Active Directory](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).

### <a name="sqlcmd"></a>sqlcmd

Následující příkazy, připojit pomocí verze 13.1 sqlcmd, který je k dispozici v [centru pro stahování](https://www.microsoft.com/download/details.aspx?id=53591).

> [!NOTE]
> `sqlcmd`s `-G` příkazem nefunguje s identitami systému a vyžaduje přihlášení uživatele.

```cmd
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="troubleshooting-azure-ad-authentication"></a>Poradce při potížích s ověřováním služby Azure AD

Pokyny k řešení problémů s ověřováním Azure AD najdete v následujícím blogu:<https://techcommunity.microsoft.com/t5/azure-sql-database/troubleshooting-problems-related-to-azure-ad-authentication-with/ba-p/1062991>

## <a name="next-steps"></a>Další kroky

- Přehled přihlášení, uživatelů, databázových rolí a oprávnění v databázi SQL naleznete v tématu [Přihlášení, uživatelé, databázové role a uživatelské účty](sql-database-manage-logins.md).
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
