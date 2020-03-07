---
title: Konfigurace ověřování Azure Active Directory
description: Přečtěte si, jak se připojit k SQL Database, spravované instanci a Azure synapse pomocí ověřování Azure Active Directory – po nakonfigurování služby Azure AD.
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
tags: azure-synapse
ms.openlocfilehash: 42f79b83d174571d26f49b28ed480f86a004036c
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78358411"
---
# <a name="configure-and-manage-azure-active-directory-authentication-with-sql"></a>Konfigurace a Správa ověřování Azure Active Directory pomocí SQL

V tomto článku se dozvíte, jak vytvořit a naplnit Azure AD a potom použít Azure AD s Azure [SQL Database](sql-database-technical-overview.md), [Managed instance](sql-database-managed-instance.md)a [Azure synapse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Přehled najdete v tématu [Azure Active Directory Authentication](sql-database-aad-authentication.md).

> [!NOTE]
> Tento článek se týká Azure SQL serveru a SQL Database a Azure Synpase], které jsou vytvořené na Azure SQL serveru. Pro zjednodušení se SQL Database používá při odkazování na SQL Database a Azure synapse.

> [!IMPORTANT]  
> Připojení k SQL Server běžícímu na virtuálním počítači Azure se pomocí účtu Azure Active Directory nepodporuje. Místo toho použijte účet domény služby Active Directory.

## <a name="create-and-populate-an-azure-ad"></a>Vytvoření a naplnění služby Azure AD

Vytvořte Azure AD a naplňte ho uživateli a skupinami. Azure AD může být počáteční doménou spravovanou službou Azure AD. Azure AD může být také místní Active Directory Domain Services, která je federované s Azure AD.

Další informace najdete v tématech [Integrování místních identit do služby Azure Active Directory](../active-directory/hybrid/whatis-hybrid-identity.md), [Přidání vlastního názvu domény do Azure AD](../active-directory/active-directory-domains-add-azure-portal.md), [Microsoft Azure podporuje federaci s Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/), [Správa adresáře služby Azure AD](../active-directory/fundamentals/active-directory-administer.md), [Správa služby Azure AD pomocí rozhraní Windows PowerShell](/powershell/azure/overview) a [Porty a protokoly, které vyžaduje hybridní identita](../active-directory/hybrid/reference-connect-ports.md).

## <a name="associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Přidružte nebo přidejte předplatné Azure k Azure Active Directory

1. Přidružte své předplatné Azure k Azure Active Directory tím, že adresář považujete za důvěryhodný adresář pro předplatné Azure hostující databázi. Podrobnosti najdete v tématu [jak jsou předplatná Azure přidružená k Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

2. Pomocí přepínání adresářů v Azure Portal přepněte do předplatného přidruženého k doméně.

   > [!IMPORTANT]
   > Každé předplatné služby Azure má vztah důvěryhodnosti s instancí služby Azure AD. To znamená, že tomuto adresáři svěřuje ověřování uživatelů, služeb i zařízení. Několik předplatných může důvěřovat stejnému adresáři, ale jedno předplatné důvěřuje pouze jednomu adresáři. Vztah důvěryhodnosti, který má předplatné s adresářem, se liší od vztahu, který má předplatné se všemi ostatními prostředky ve službě Azure (webové stránky, databáze apod.), což jsou pro předplatné spíše podřízené prostředky. Pokud platnost předplatného vyprší, zastaví se i přístup k těmto dalším prostředkům přidruženým k předplatnému. Adresář však ve službě Azure zůstane a vy k němu můžete přidružit jiné předplatné a pokračovat ve správě uživatelů adresáře. Další informace o prostředcích najdete v tématu [porozumění přístupu k prostředkům v Azure](../active-directory/active-directory-b2b-admin-add-users.md). Další informace o tomto důvěryhodném vztahu najdete v tématu [Postup přidružení nebo přidání předplatného Azure do Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="create-an-azure-ad-administrator-for-azure-sql-server"></a>Vytvoření správce Azure AD pro Azure SQL Server

Každý server SQL Azure (který je hostitelem SQL Database nebo Azure synapse) začíná s jedním účtem správce serveru, který je správcem celého serveru SQL Azure. Je potřeba vytvořit druhého správce SQL Server, který je účtem Azure AD. Tento objekt zabezpečení je vytvořen jako uživatel databáze s omezením v hlavní databázi. Jako správci jsou účty správců serveru členy role **db_owner** v každé uživatelské databázi a jako uživatel **dbo** zadejte každou uživatelskou databázi. Další informace o účtech správců serveru najdete v tématu [Správa databází a přihlášení v Azure SQL Database](sql-database-manage-logins.md).

Při použití Azure Active Directory s geografickou replikací musí být správce Azure Active Directory nakonfigurovaný jak pro primární, tak pro sekundární servery. Pokud server nemá správce Azure Active Directory, Azure Active Directory přihlášení a uživatelé obdrží chybu "nelze se připojit" k chybě serveru.

> [!NOTE]
> Uživatelé, kteří nejsou založeni na účtu Azure AD (včetně účtu správce Azure SQL serveru), nemůžou vytvářet uživatele založené na službě Azure AD, protože nemají oprávnění k ověřování uživatelů navrhovaných databází pomocí Azure AD.

## <a name="provision-an-azure-active-directory-administrator-for-your-managed-instance"></a>Zřízení správce Azure Active Directory pro spravovanou instanci

> [!IMPORTANT]
> Postupovat pouze v případě, že zřizujete spravovanou instanci. Tuto operaci může provést jenom globální správce nebo správce společnosti nebo správce privilegované role v Azure AD. Následující kroky popisují proces udělení oprávnění pro uživatele s různými oprávněními v adresáři.

> [!NOTE]
> Pro správce Azure AD pro MI se vytvořil před GA, ale pokračuje v provozu po GA, neexistuje žádná funkční Změna stávajícího chování. Další informace najdete v části [nové funkce správce Azure AD pro mi](#new-azure-ad-admin-functionality-for-mi) , kde najdete další podrobnosti.

Vaše spravovaná instance potřebuje oprávnění ke čtení služby Azure AD, aby bylo možné úspěšně provádět úlohy, jako je například ověřování uživatelů prostřednictvím členství ve skupině zabezpečení nebo vytváření nových uživatelů. Aby to fungovalo, musíte udělit oprávnění ke spravované instanci pro čtení Azure AD. Existují dva způsoby, jak to provést: z portálu a PowerShellu. Následující postup obou metod.

1. V Azure Portal v pravém horním rohu výběrem svého připojení vyrozevíracíte seznam možných aktivních adresářů.

2. Vyberte správnou službu Active Directory jako výchozí službu Azure AD.

   Tento krok propojí předplatné přidružené ke službě Active Directory se spravovanou instancí a zajistí, že se stejné předplatné používá pro Azure AD i pro spravovanou instanci.

3. Přejděte do spravované instance a vyberte ji, kterou chcete použít pro integraci služby Azure AD.

   ![poplašné](./media/sql-database-aad-authentication/aad.png)

4. Vyberte banner v horní části stránky Správce služby Active Directory a udělte aktuálnímu uživateli oprávnění. Pokud jste v Azure AD přihlášeni jako globální správce/správce společnosti, můžete to udělat z Azure Portal nebo pomocí skriptu v prostředí PowerShell.

    ![udělení oprávnění – portál](./media/sql-database-aad-authentication/grant-permissions.png)

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

6. Teď si můžete vybrat svého správce Azure AD pro spravovanou instanci. V takovém případě na stránce Správce služby Active Directory vyberte **nastavit správce** .

    ![Nastavení – správce](./media/sql-database-aad-authentication/set-admin.png)

7. Na stránce Správce AAD vyhledejte uživatele, vyberte uživatele nebo skupinu, kterým chcete správce, a pak vyberte **Vybrat**.

   Na stránce Správce služby Active Directory se zobrazují všichni členové a skupiny služby Active Directory. Uživatele nebo skupiny, které jsou zobrazené šedě, nejde vybrat, protože nejsou podporované jako správci Azure AD. Podívejte se na seznam podporovaných správců ve [funkcích a omezeních služby Azure AD](sql-database-aad-authentication.md#azure-ad-features-and-limitations). Řízení přístupu na základě role (RBAC) platí jenom pro Azure Portal a není šířené do SQL Server.

    ![Přidat správce Azure Active Directory](./media/sql-database-aad-authentication/add-azure-active-directory-admin.png)

8. V horní části stránky Správce služby Active Directory vyberte **Uložit**.

    ![Uložit](./media/sql-database-aad-authentication/save.png)

    Proces změny správce může trvat několik minut. Pak se nový správce zobrazí v poli Správce služby Active Directory.

Po zřízení správce Azure AD pro spravovanou instanci můžete začít vytvářet objekty zabezpečení serveru Azure AD (přihlášení) pomocí syntaxe <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Vytvoření přihlášení</a> . Další informace najdete v tématu [Přehled spravované instance](sql-database-managed-instance.md#azure-active-directory-integration).

> [!TIP]
> Chcete-li později odebrat správce, v horní části stránky Správce služby Active Directory vyberte možnost **Odebrat správce**a pak vyberte **Uložit**.

### <a name="new-azure-ad-admin-functionality-for-mi"></a>Nové funkce správce Azure AD pro MI

Následující tabulka shrnuje funkce pro správce přihlášení služby Azure AD ve verzi Public Preview pro MI, a to oproti novým funkcím, které jsou poskytovány s GA pro přihlášení Azure AD.

| Správce přihlášení Azure AD pro MI během veřejné verze Preview | Funkce GA pro správce Azure AD pro MI |
| --- | ---|
| Se chová podobným způsobem jako správce Azure AD pro SQL Database, který umožňuje ověřování Azure AD, ale správce Azure AD nemůže v hlavní databázi pro MI vytvořit přihlašovací údaje Azure AD ani SQL. | Správce Azure AD má oprávnění sysadmin a může vytvořit přihlašovací údaje AAD a SQL v databázi Master DB pro MI. |
| Není k dispozici v zobrazení sys. server_principals | Je k dispozici v zobrazení sys. server_principals |
| Umožňuje nastavit jako správce Azure AD pro MI jednotlivé uživatele typu hosta Azure AD. Další informace najdete v tématu [přidání Azure Active Directory uživatelů spolupráce B2B v Azure Portal](../active-directory/b2b/add-users-administrator.md). | Vyžaduje vytvoření skupiny Azure AD s uživateli typu Host jako členové, kteří tuto skupinu nastavili jako správce Azure AD pro MI. Další informace najdete v tématu [Podpora Azure AD Business to Business](sql-database-ssms-mfa-authentication.md#azure-ad-business-to-business-support). |

Jako osvědčený postup pro stávající správce Azure AD pro MI vytvořil před GA a pořád provozovaná po GA, resetujte správce Azure AD pomocí možnosti Azure Portal "odebrat správce" a "nastavit správce" pro stejného uživatele nebo skupinu Azure AD.

### <a name="known-issues-with-the-azure-ad-login-ga-for-mi"></a>Známé problémy s přihlašováním Azure AD GA pro MI

- Pokud v hlavní databázi pro MI existuje přihlášení Azure AD, které se vytvořilo pomocí příkazu T-SQL `CREATE LOGIN [myaadaccount] FROM EXTERNAL PROVIDER`, nejde ho nastavit jako správce Azure AD pro MI. Při nastavování přihlašovacích údajů jako správce Azure AD dojde k chybě pomocí příkazů Azure Portal, PowerShellu nebo rozhraní příkazového řádku pro vytvoření přihlášení k Azure AD.
  - Aby bylo možné vytvořit účet jako správce Azure AD, musí být přihlašovací jméno vyřazené z hlavní databáze pomocí příkazu `DROP LOGIN [myaadaccount]`.
  - Nastavte účet správce Azure AD v Azure Portal po úspěšném `DROP LOGIN`. 
  - Pokud nemůžete nastavit účet správce Azure AD, vraťte se do hlavní databáze spravované instance pro přihlášení. Použijte následující příkaz: `SELECT * FROM sys.server_principals`
  - Nastavením správce Azure AD pro MI bude pro tento účet automaticky vytvářet přihlašovací údaje v hlavní databázi. Odebrání správce Azure AD automaticky vynechá přihlašovací údaje z hlavní databáze.

- Jednotliví uživatelé typu Host služby Azure AD nejsou podporováni jako správci služby Azure AD pro MI. Uživatelé typu Host musí být součástí skupiny Azure AD, aby je bylo možné nastavit jako správce Azure AD. V současné době okno Azure Portal nezobrazuje šedě uživatele typu Host pro jinou službu Azure AD a umožňuje uživatelům pokračovat v instalaci správce. Při ukládání uživatelů typu Host jako správce Azure AD dojde k selhání instalace.
  - Pokud chcete, aby uživatel typu Host byl pro MI správce Azure AD, zahrňte uživatele typu Host ve skupině Azure AD a nastavte tuto skupinu jako správce Azure AD.

### <a name="powershell-for-sql-managed-instance"></a>PowerShell pro spravovanou instanci SQL

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pokud chcete spustit rutiny PowerShellu, musíte mít Azure PowerShell nainstalovanou a spuštěnou. Podrobné informace najdete v tématu [Instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview).

> [!IMPORTANT]
> Modul Azure Resource Manager PowerShellu (RM) je stále podporován Azure SQL Database, ale všechny budoucí vývojové prostředí jsou k dispozici pro modul AZ. SQL. V modulu AzureRM bude i nadále docházet k opravám chyb až do prosince 2020.  Argumenty pro příkazy v modulech AZ a v modulech AzureRm jsou v podstatě identické. Další informace o kompatibilitě najdete v tématu [představení nového Azure PowerShell AZ Module](/powershell/azure/new-azureps-module-az).

Pokud chcete zřídit správce Azure AD, spusťte následující příkazy Azure PowerShell:

- Connect-AzAccount
- Vybrat – AzSubscription

Rutiny používané ke zřízení a správě správce Azure AD pro spravovanou instanci SQL:

| Název rutiny | Popis |
| --- | --- |
| [Set-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlinstanceactivedirectoryadministrator) |Zřídí správce Azure AD pro spravovanou instanci SQL v aktuálním předplatném. (Musí být z aktuálního předplatného)|
| [Remove-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlinstanceactivedirectoryadministrator) |Odebere správce Azure AD pro spravovanou instanci SQL v aktuálním předplatném. |
| [Get-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlinstanceactivedirectoryadministrator) |Vrátí informace o Správci služby Azure AD pro spravovanou instanci SQL v aktuálním předplatném.|

Následující příkaz získá informace o Správci Azure AD pro spravovanou instanci s názvem ManagedInstance01, která je přidružená ke skupině prostředků s názvem ResourceGroup01.

```powershell
Get-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01"
```

Následující příkaz zřídí skupinu správců Azure AD s názvem specializující pro spravovanou instanci nazvanou ManagedInstance01. Tento server je přidružený ke skupině prostředků ResourceGroup01.

```powershell
Set-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01" -DisplayName "DBAs" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353b"
```

Následující příkaz odebere správce Azure AD pro spravovanou instanci s názvem ManagedInstanceName01 přidruženou ke skupině prostředků ResourceGroup01.

```powershell
Remove-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstanceName01" -Confirm -PassThru
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Správce Azure AD pro spravovanou instanci SQL můžete také zřídit voláním následujících příkazů rozhraní příkazového řádku:

| Příkaz | Popis |
| --- | --- |
|[AZ SQL mi AD-admin Create](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-create) | Zřídí správce Azure Active Directory pro spravovanou instanci SQL. (Musí být z aktuálního předplatného) |
|[AZ SQL mi AD-admin DELETE](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-delete) | Odebere správce Azure Active Directory pro spravovanou instanci SQL. |
|[AZ SQL mi AD – seznam správců](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-list) | Vrátí informace o Správci Azure Active Directory aktuálně nakonfigurovaném pro spravovanou instanci SQL. |
|[AZ SQL mi AD – aktualizace pro správce](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-update) | Aktualizuje Správce služby Active Directory pro spravovanou instanci SQL. |

Další informace o příkazech rozhraní příkazového řádku najdete v tématu [AZ SQL mi](/cli/azure/sql/mi).

* * *

## <a name="provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server"></a>Zřízení správce Azure Active Directory pro server Azure SQL Database

> [!IMPORTANT]
> Postupovat pouze v případě, že zřizujete Azure SQL Database Server nebo fond SQL ve službě Azure synapse.

Následující dva postupy vám ukážou, jak zřídit správce Azure Active Directory pro Azure SQL Server v Azure Portal a pomocí PowerShellu.

### <a name="azure-portal"></a>Azure Portal

1. V [Azure Portal](https://portal.azure.com/)v pravém horním rohu výběrem svého připojení vyrozevíracíte seznam možných aktivních adresářů. Vyberte správnou službu Active Directory jako výchozí službu Azure AD. Tento krok propojí službu Active Directory přidruženou k předplatnému se službou Azure SQL Server, která zajišťuje, že se stejné předplatné používá pro Azure AD i SQL Server. (Azure SQL Server může hostovat Azure SQL Database nebo Azure synapse.)

    ![zvolit – AD][8]

2. Vyhledejte a vyberte **SQL Server**.

    ![Vyhledat a vybrat servery SQL](media/sql-database-aad-authentication/search-for-and-select-sql-servers.png)

    >[!NOTE]
    > Na této stránce před výběrem **SQL serveru**můžete vybrat **hvězdičku** vedle názvu *, abyste kategorii mohli přidat* a přidat **SQL servery** do levého navigačního panelu.

3. Na stránce **SQL Server** vyberte možnost **Správce služby Active Directory**.

4. Na stránce **Správce služby Active Directory** vyberte **nastavit správce**.

    ![SQL servery – nastavení správce služby Active Directory](./media/sql-database-aad-authentication/sql-servers-set-active-directory-admin.png)  

5. Na stránce **přidat správce** vyhledejte uživatele, vyberte uživatele nebo skupinu, které mají být správcem, a pak vyberte **Vybrat**. (Na stránce Správce služby Active Directory se zobrazují všichni členové a skupiny služby Active Directory. Uživatele nebo skupiny, které jsou šedé, nelze vybrat, protože nejsou podporovány jako správci služby Azure AD. (Další informace najdete v seznamu podporovaných správců v části **funkce a omezení služby Azure AD** tématu [použití Azure Active Directory ověřování pro ověřování pomocí SQL Database nebo Azure synapse](sql-database-aad-authentication.md)). Řízení přístupu na základě role (RBAC) se vztahuje jenom na portál a nešíří se na SQL Server.

    ![Vybrat správce Azure Active Directory](./media/sql-database-aad-authentication/select-azure-active-directory-admin.png)  

6. V horní části stránky **Správce služby Active Directory** vyberte **Uložit**.

    ![Uložit správce](./media/sql-database-aad-authentication/save-admin.png)

Proces změny správce může trvat několik minut. Pak se nový správce zobrazí v poli **Správce služby Active Directory** .

   > [!NOTE]
   > Při nastavování správce Azure AD se nové jméno správce (uživatel nebo skupina) už ve virtuální hlavní databázi nedá nacházet jako uživatel s ověřováním SQL Server. Pokud je k dispozici, instalace správce Azure AD se nezdaří. vrácení svého vytvoření a označení, že takový správce (jméno) již existuje. Vzhledem k tomu, že takový uživatel SQL Server ověřování není součástí služby Azure AD, neproběhne žádné úsilí pro připojení k serveru pomocí ověřování Azure AD.

Chcete-li později odebrat správce, v horní části stránky **Správce služby Active Directory** vyberte možnost **Odebrat správce**a pak vyberte **Uložit**.

### <a name="powershell-for-azure-sql-database-and-azure-synapse"></a>PowerShell pro Azure SQL Database a Azure synapse

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pokud chcete spustit rutiny PowerShellu, musíte mít Azure PowerShell nainstalovanou a spuštěnou. Podrobné informace najdete v tématu [Instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview). Pokud chcete zřídit správce Azure AD, spusťte následující příkazy Azure PowerShell:

- Connect-AzAccount
- Vybrat – AzSubscription

Rutiny používané ke zřízení a správě správce Azure AD pro Azure SQL Database a fond SQL ve službě Azure Synpase:

| Název rutiny | Popis |
| --- | --- |
| [Set-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlserveractivedirectoryadministrator) |Zřídí správce Azure Active Directory pro Azure SQL Server nebo Azure Synpase. (Musí být z aktuálního předplatného) |
| [Remove-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlserveractivedirectoryadministrator) |Odebere správce Azure Active Directory pro Azure SQL Server nebo Azure synapse. |
| [Get-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator) |Vrátí informace o Správci Azure Active Directory aktuálně nakonfigurovaném pro Azure SQL Server nebo Azure synapse. |

K zobrazení dalších informací pro každý z těchto příkazů použijte příkaz PowerShellu Get-Help. například `get-help Set-AzSqlServerActiveDirectoryAdministrator`.

Následující skript zřídí skupinu správců Azure AD s názvem **DBA_Group** (ID objektu `40b79501-b343-44ed-9ce7-da4c8cc7353f`) pro **demo_server** Server ve skupině prostředků s názvem **Skupina-23**:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" -DisplayName "DBA_Group"
```

Vstupní parametr **DisplayName** přijímá buď zobrazované jméno Azure AD, nebo hlavní název uživatele. Například ``DisplayName="John Smith"`` a ``DisplayName="johns@contoso.com"``. Pro skupiny Azure AD se podporuje jenom zobrazované jméno Azure AD.

> [!NOTE]
> Příkaz Azure PowerShell ```Set-AzSqlServerActiveDirectoryAdministrator``` nebrání zřizování správců Azure AD pro nepodporované uživatele. Můžete zřídit nepodporovaného uživatele, ale nemůžete se připojit k databázi.

Následující příklad používá volitelné **objectID**:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" `
    -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> **Identifikátor objectID** služby Azure AD je vyžadován, pokud **Zobrazovaný** název není jedinečný. Chcete-li načíst hodnoty **objectID** a **DisplayName** , použijte část portál Azure Classic služby Active Directory a zobrazte vlastnosti uživatele nebo skupiny.

Následující příklad vrátí informace o aktuálním Správci služby Azure AD pro Azure SQL Server:

```powershell
Get-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" | Format-List
```

Následující příklad odebere správce Azure AD:

```powershell
Remove-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Správce Azure AD můžete zřídit voláním následujících příkazů rozhraní příkazového řádku:

| Příkaz | Popis |
| --- | --- |
|[AZ SQL Server AD-admin Create](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-create) | Zřídí správce Azure Active Directory pro Azure SQL Server nebo Azure synapse. (Musí být z aktuálního předplatného) |
|[AZ SQL Server AD – odstranění správce](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-delete) | Odebere správce Azure Active Directory pro Azure SQL Server nebo Azure synapse. |
|[AZ SQL Server AD – seznam správců](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-list) | Vrátí informace o Správci Azure Active Directory aktuálně nakonfigurovaném pro Azure SQL Server nebo Azure synapse. |
|[AZ SQL Server AD – aktualizace pro správce](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-update) | Aktualizuje Správce služby Active Directory pro Azure SQL Server nebo Azure synapse. |

Další informace o příkazech rozhraní příkazového řádku najdete v tématu [AZ SQL Server](/cli/azure/sql/server).

* * *

> [!NOTE]
> Správce Azure Active Directory můžete také zřídit pomocí rozhraní REST API. Další informace najdete v tématu [referenční informace o operacích správy služby REST API a operacích Azure SQL Database pro Azure SQL Database](/rest/api/sql/)

## <a name="configure-your-client-computers"></a>Konfigurace klientských počítačů

Na všech klientských počítačích, ze kterých se vaše aplikace nebo uživatelé připojují k Azure SQL Database nebo Synpase fondu SQL Azure pomocí identit Azure AD, je nutné nainstalovat následující software:

- .NET Framework 4,6 nebo novější z [https://msdn.microsoft.com/library/5a4x27ek.aspx](https://msdn.microsoft.com/library/5a4x27ek.aspx).
- Knihovna ověřování Azure Active Directory pro SQL Server (*ADAL. DLL*). Níže jsou uvedené odkazy ke stažení pro instalaci nejnovějšího ovladače SSMS, ODBC a OLE DB, který obsahuje *ADAL. Knihovna DLL* .
    1. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)
    1. [ODBC Driver 17 pro SQL Server](https://www.microsoft.com/download/details.aspx?id=56567)
    1. [OLE DB ovladače 18 pro SQL Server](https://www.microsoft.com/download/details.aspx?id=56730)

Tyto požadavky můžete splnit:

- Instalace nejnovější verze [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) nebo [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt) splňuje požadavky .NET Framework 4,6.
    - SSMS nainstaluje verzi x86 knihovny *ADAL. Knihovna DLL*:
    - SSDT nainstaluje verzi knihovny *ADAL. Knihovna DLL*:
    - Nejnovější verze sady Visual Studio ze sady [Visual Studio ke stažení](https://www.visualstudio.com/downloads/download-visual-studio-vs) splňuje požadavek .NET Framework 4,6, ale neinstaluje požadovanou verzi amd64 knihovny *ADAL. Knihovna DLL*:

## <a name="create-contained-database-users-in-your-database-mapped-to-azure-ad-identities"></a>Umožňuje vytvořit uživatele databáze s omezením v databázi namapované na identity Azure AD.

> [!IMPORTANT]
> Spravovaná instance teď podporuje objekty zabezpečení serveru Azure AD (přihlášení), které umožňují vytvářet přihlášení z uživatelů, skupin a aplikací Azure AD. Objekty zabezpečení serveru Azure AD (přihlášení) poskytují možnost ověřování ve vaší spravované instanci bez toho, aby se uživatelé databáze museli vytvořit jako uživatel s omezením databáze. Další informace najdete v tématu [Přehled spravované instance](sql-database-managed-instance.md#azure-active-directory-integration). Syntaxi při vytváření objektů zabezpečení serveru Azure AD (přihlášení) najdete v tématu věnovaném <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Vytvoření přihlašovacích</a>údajů.

Ověřování Azure Active Directory vyžaduje, aby uživatelé databáze byli vytvořeni jako uživatelé databáze s omezením. Uživatel databáze s omezením, který je založený na identitě Azure AD, je uživatel databáze, který nemá přihlašovací údaje v hlavní databázi a který se mapuje na identitu v adresáři služby Azure AD, který je přidružený k databázi. Identitou Azure AD může být buď jednotlivý uživatelský účet, nebo skupina. Další informace o uživatelích databáze s omezením najdete v tématu databáze [uživatelů s omezením – vytvoření přenosné](https://msdn.microsoft.com/library/ff929188.aspx)databáze.

> [!NOTE]
> Uživatele databáze (s výjimkou správců) nelze vytvořit pomocí Azure Portal. Role RBAC se nešíří do SQL Server, SQL Database nebo fondu SQL ve službě Azure synapse. Role Azure RBAC se používají ke správě prostředků Azure a nevztahují se na oprávnění databáze. Například role **přispěvatel SQL Server** neuděluje přístup pro připojení k SQL Database nebo ke fondu SQL ve službě Azure synapse. Přístupové oprávnění musí být uděleno přímo v databázi pomocí příkazů jazyka Transact-SQL.

> [!WARNING]
> Speciální znaky jako dvojtečka `:` nebo ampersand `&`, pokud jsou zahrnuté jako uživatelská jména v příkazech T-SQL CREATE LOGIN a CREATE USER nejsou podporovány.

Pokud chcete vytvořit uživatele databáze s omezením založené na službě Azure AD (jiný než správce serveru, který je vlastníkem databáze), připojte se k databázi pomocí identity Azure AD jako uživatel, který má aspoň oprávnění ke **změně libovolného uživatele** . Pak použijte následující syntaxi jazyka Transact-SQL:

```sql
CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
```

*Azure_AD_principal_name* může být hlavní název uživatele Azure AD nebo zobrazované jméno pro skupinu Azure AD.

**Příklady:** Vytvoření uživatele databáze s omezením představujícího uživatele federovaného nebo spravované domény Azure AD:

```sql
CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;
```

Pokud chcete vytvořit uživatele databáze s omezením, který představuje skupinu zabezpečení Azure AD nebo federované domény, zadejte zobrazovaný název skupiny zabezpečení:

```sql
CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;
```

Vytvoření uživatele databáze s omezením představující aplikaci, která se připojuje pomocí tokenu Azure AD:

```sql
CREATE USER [appName] FROM EXTERNAL PROVIDER;
```

> [!NOTE]
> Tento příkaz vyžaduje, aby služba SQL Access Azure AD ("externí poskytovatel") jménem přihlášeného uživatele. Někdy nastane situace, které způsobí, že Azure AD vrátí výjimku zpátky do SQL. V těchto případech se uživateli zobrazí chyba SQL 33134, která by měla obsahovat chybovou zprávu specifickou pro AAD. Ve většině případů tato chyba znamená, že přístup je odepřený nebo že se uživatel musí zaregistrovat v MFA pro přístup k prostředku nebo že přístup mezi aplikacemi první strany musí být zpracován prostřednictvím předvedení. V prvních dvou případech je problém obvykle způsoben zásadami podmíněného přístupu, které jsou nastaveny v tenantovi AAD uživatele: zabrání uživateli v přístupu k externímu poskytovateli. Aktualizace zásad certifikační autority tak, aby povolovala přístup k aplikaci "00000002-0000-0000-C000-000000000000" (ID aplikace Graph API AAD) by měla tento problém vyřešit. V případě, že chyba znamená, že přístup mezi aplikacemi první strany musí být zpracován prostřednictvím předvedení, problém je proto, že uživatel je přihlášený jako instanční objekt. Příkaz by měl být úspěšný, pokud ho spustí uživatel.

> [!TIP]
> Nemůžete přímo vytvořit uživatele z jiné Azure Active Directory než Azure Active Directory, která je přidružená k vašemu předplatnému Azure. Do skupiny služby Active Directory ve službě Active Directory klienta se ale dají přidat i členové jiných aktivních adresářů, které jsou importované uživatele v přidružené službě Active Directory (označované jako externí uživatelé). Když pro tuto skupinu AD vytvoříte uživatele databáze s omezením, můžou uživatelé z externí služby Active Directory získat přístup k SQL Database.

Další informace o vytváření uživatelů databáze s omezením na základě Azure Active Directory identit naleznete v tématu [Create User (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx).

> [!NOTE]
> Odebráním správce Azure Active Directory pro Azure SQL Server znemožníte uživatelům ověřování Azure AD, aby se připojili k serveru. V případě potřeby je možné uživatele Azure AD, kteří se nedají použít, vyřadit ručně pomocí správce SQL Database.

> [!NOTE]
> Pokud obdržíte **časový limit připojení**, možná budete muset nastavit parametr `TransparentNetworkIPResolution` připojovacího řetězce na hodnotu NEPRAVDA. Další informace najdete v tématu [Chyba vypršení časového limitu připojení .NET Framework 4.6.1-konfiguruje nastavení transparentnetworkipresolution](https://blogs.msdn.microsoft.com/dataaccesstechnologies/20../../connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution/).

Když vytvoříte uživatele databáze, obdrží tento uživatel oprávnění **připojit** a může se k této databázi připojit jako člen **veřejné** role. Zpočátku jsou jedinou oprávnění k dispozici pro uživatele jakákoli oprávnění udělená **veřejné** roli nebo jakákoli oprávnění udělená všem SKUPINÁM Azure AD, které jsou členy. Po zřízení uživatele databáze s omezením založeného na službě Azure AD můžete uživateli udělit další oprávnění stejným způsobem jako oprávnění pro jakýkoli jiný typ uživatele. Typicky udělte oprávnění databázovým rolím a přidávají uživatele do rolí. Další informace najdete v tématu [základy oprávnění k databázovému stroji](https://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx). Další informace o zvláštních rolích SQL Database najdete [v tématu Správa databází a přihlášení v Azure SQL Database](sql-database-manage-logins.md).
Uživatelský účet federované domény, který je importován do spravované domény jako externí uživatel, musí používat identitu spravované domény.

> [!NOTE]
> Uživatelé Azure AD jsou označeni v metadatech databáze typu E (EXTERNAL_USER) a pro skupiny s typem X (EXTERNAL_GROUPS). Další informace najdete v tématu [Sys. database_principals](https://msdn.microsoft.com/library/ms187328.aspx).

## <a name="connect-to-the-user-database-or-data-warehouse-by-using-ssms-or-ssdt"></a>Připojení k uživatelské databázi nebo datovému skladu pomocí SSMS nebo SSDT  

Pokud chcete potvrdit, že správce Azure AD je správně nastavený, připojte se k **Hlavní** databázi pomocí účtu správce Azure AD.
Pokud chcete zřídit uživatele databáze s omezením založené na službě Azure AD (jiný než správce serveru, který je vlastníkem databáze), připojte se k databázi pomocí identity Azure AD, která má přístup k databázi.

> [!IMPORTANT]
> Podpora ověřování Azure Active Directory je k dispozici v [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) a [datových nástrojích SQL Server](https://msdn.microsoft.com/library/mt204009.aspx) v aplikaci Visual Studio 2015. Verze SSMS ze srpna 2016 obsahuje také podporu univerzálního ověřování služby Active Directory, která správcům umožňuje vyžadovat Multi-Factor Authentication pomocí telefonního hovoru, textové zprávy, čipové karty s kódem PIN nebo oznámením o mobilní aplikaci.

## <a name="using-an-azure-ad-identity-to-connect-using-ssms-or-ssdt"></a>Použití identity Azure AD pro připojení pomocí SSMS nebo SSDT

Následující postupy ukazují, jak se připojit k databázi SQL pomocí identity Azure AD pomocí SQL Server Management Studio nebo SQL Server databázových nástrojů.

### <a name="active-directory-integrated-authentication"></a>Integrované ověřování služby Active Directory

Tuto metodu použijte, pokud jste přihlášeni k Windows pomocí přihlašovacích údajů Azure Active Directory z federované domény.

1. Spusťte Management Studio nebo datové nástroje a v dialogovém okně **připojit k serveru** (nebo **se připojte k databázovému stroji**) vyberte v poli **ověřování** možnost **integrovaná se službou Active Directory**. Není potřeba žádné heslo, nebo ho nejde zadat, protože pro připojení se zobrazí vaše existující přihlašovací údaje.

    ![Vybrat integrované ověřování Active Directory][11]

2. Vyberte tlačítko **Možnosti** a na stránce **Vlastnosti připojení** do pole **připojit k databázi** zadejte název uživatelské databáze, ke které se chcete připojit. (Možnost **název domény služby AD nebo ID tenanta**se podporuje jenom pro možnosti **připojení s MFA (Universal** ), v opačném případě se jedná o šedé.)  

    ![Vyberte název databáze.][13]

## <a name="active-directory-password-authentication"></a>Ověřování hesla služby Active Directory

Tuto metodu použijte při připojení k hlavnímu názvu Azure AD pomocí spravované domény Azure AD. Můžete ji také použít pro federované účty bez přístupu k doméně, například při vzdálené práci.

Tuto metodu použijte k ověření pro SQL DB/DW s Azure AD pro nativní nebo federované uživatele Azure AD. Nativní uživatel je explicitně vytvořený v Azure AD a ověřuje se pomocí uživatelského jména a hesla, zatímco Federovaný uživatel je uživatel systému Windows, jehož doména je federované pomocí Azure AD. Druhá metoda (pomocí uživatelského & heslo) se dá použít, když chce uživatel použít svoje přihlašovací údaje pro Windows, ale jejich místní počítač není připojený k doméně (třeba pomocí vzdáleného přístupu). V takovém případě může uživatel systému Windows označit svůj doménový účet a heslo a ověřit je v databázi SQL DB/DW pomocí federovaných přihlašovacích údajů.

1. Spusťte Management Studio nebo datové nástroje a v dialogovém okně **připojit k serveru** (nebo **se připojte k databázovému stroji**) v poli **ověřování** vyberte možnost **Active Directory – heslo**.

2. Do pole **uživatelské jméno** zadejte Azure Active Directory uživatelské jméno ve formátu **username\@Domain.com**. Uživatelské jméno musí být účet z Azure Active Directory nebo účet z federovat domény s Azure Active Directory.

3. Do pole **heslo** zadejte heslo uživatele pro účet Azure Active Directory nebo účet federované domény.

    ![Vybrat ověřování hesla služby AD][12]

4. Vyberte tlačítko **Možnosti** a na stránce **Vlastnosti připojení** do pole **připojit k databázi** zadejte název uživatelské databáze, ke které se chcete připojit. (Viz obrázek v předchozí možnosti.)

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>Připojení z klientské aplikace pomocí identity Azure AD

Následující postupy ukazují, jak se připojit k databázi SQL pomocí identity Azure AD z klientské aplikace.

### <a name="active-directory-integrated-authentication"></a>Integrované ověřování služby Active Directory

Pokud chcete použít integrované ověřování systému Windows, musí být služba Active Directory vaší domény federované s Azure Active Directory. Klientská aplikace (nebo služba), která se připojuje k databázi, musí být spuštěná na počítači připojeném k doméně v rámci pověření domény uživatele.

Pokud se chcete připojit k databázi pomocí integrovaného ověřování a identity Azure AD, musí být klíčové slovo ověřování v připojovacím řetězci databáze nastavené na Active Directory Integrated. Následující C# ukázka kódu používá rozhraní ADO .NET.

```csharp
string ConnectionString = @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Klíčové slovo připojovacího řetězce `Integrated Security=True` není podporované pro připojení k Azure SQL Database. Při vytváření připojení rozhraní ODBC budete muset odebrat mezery a nastavit ověřování na ' ActiveDirectoryIntegrated '.

### <a name="active-directory-password-authentication"></a>Ověřování hesla služby Active Directory

Pokud se chcete připojit k databázi pomocí integrovaného ověřování a identity Azure AD, musí být klíčové slovo ověřování nastavené na heslo služby Active Directory. Připojovací řetězec musí obsahovat klíčová slova ID uživatele/UID a heslo/heslo a hodnoty. Následující C# ukázka kódu používá rozhraní ADO .NET.

```csharp
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Přečtěte si další informace o metodách ověřování Azure AD pomocí ukázkových ukázek kódu dostupných v [ukázce Azure AD Authentication GitHubu](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth).

## <a name="azure-ad-token"></a>Token Azure AD

Tato metoda ověřování umožňuje službám střední vrstvy připojit se k Azure SQL Database nebo fondu SQL ve službě Azure synapse získáním tokenu z Azure Active Directory (AAD). Umožňuje sofistikované scénáře, včetně ověřování založeného na certifikátech. Chcete-li použít ověřování pomocí tokenu Azure AD, musíte provést čtyři základní kroky:

1. Zaregistrujte svoji aplikaci pomocí Azure Active Directory a Získejte ID klienta pro svůj kód.
2. Vytvořte uživatele databáze reprezentující aplikaci. (Dokončeno dříve v kroku 6.)
3. V klientském počítači se spustí aplikace s vytvořením certifikátu.
4. Přidejte certifikát jako klíč pro vaši aplikaci.

Vzorový připojovací řetězec:

```csharp
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
conn.AccessToken = "Your JWT token"
conn.Open();
```

Další informace najdete v [blogu o SQL Server zabezpečení](https://blogs.msdn.microsoft.com/sqlsecurity/20../../token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/). Informace o přidání certifikátu najdete v tématu Začínáme [s ověřováním na základě certifikátů v Azure Active Directory](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).

### <a name="sqlcmd"></a>sqlcmd

Následující příkazy se připojují pomocí nástroje Sqlcmd verze 13,1, který je k dispozici na [webu Download Center](https://www.microsoft.com/download/details.aspx?id=53591).

> [!NOTE]
> `sqlcmd` s příkazem `-G` nefunguje u systémových identit a vyžaduje přihlášení k hlavnímu uživateli.

```cmd
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="troubleshooting-azure-ad-authentication"></a>Řešení potíží s ověřováním Azure AD

Pokyny k řešení problémů s ověřováním Azure AD najdete na následujícím blogu: <https://techcommunity.microsoft.com/t5/azure-sql-database/troubleshooting-problems-related-to-azure-ad-authentication-with/ba-p/1062991>

## <a name="next-steps"></a>Další kroky

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
