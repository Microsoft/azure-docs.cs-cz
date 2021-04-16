---
title: Konfigurace ověřování Azure Active Directory
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: Po nakonfigurování služby Azure AD se naučíte připojit se k SQL Database, spravované instanci SQL a Azure synapse Analytics pomocí ověřování Azure Active Directory.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse, has-adal-ref, sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, sstein
ms.date: 08/17/2020
ms.openlocfilehash: 5894defca5a90f1d8cd7f312f47a37df6495ccd3
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376167"
---
# <a name="configure-and-manage-azure-ad-authentication-with-azure-sql"></a>Konfigurace a Správa ověřování Azure AD pomocí Azure SQL

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

V tomto článku se dozvíte, jak vytvořit a naplnit instanci Azure Active Directory (Azure AD), a pak použít Azure AD s [Azure SQL Database](sql-database-paas-overview.md), [spravovanou instancí SQL Azure](../managed-instance/sql-managed-instance-paas-overview.md)a [analýzou Azure synapse](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Přehled najdete v tématu [Azure Active Directory Authentication](authentication-aad-overview.md).

## <a name="azure-ad-authentication-methods"></a>Metody ověřování Azure AD

Ověřování Azure AD podporuje následující metody ověřování:

- Identity jenom cloudu Azure AD
- Hybridní identity Azure AD, které podporují:
  - Cloudové ověřování se dvěma možnostmi společně s bezproblémové jednotné přihlašování (SSO)
    - Ověřování hodnoty hash hesel Azure AD
    - Předávací ověřování Azure AD
  - Federovaného ověřování

Další informace o metodách ověřování Azure AD a o tom, která z nich si můžete vybrat, najdete v tématu [Volba správné metody ověřování pro Azure Active Directory řešení hybridní identity](../../active-directory/hybrid/choose-ad-authn.md).

Další informace o hybridních identitách, nastaveních a synchronizaci Azure AD najdete v těchto tématech:

- Ověřování hodnotou hash hesla – [implementace synchronizace hodnot hash hesel pomocí Azure AD Connect synchronizace](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)
- Předávací ověřování – [Azure Active Directory předávací ověřování](../../active-directory/hybrid/how-to-connect-pta-quick-start.md)
- Federované ověřování – [nasazení Active Directory Federation Services (AD FS) v Azure](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs) a [Azure AD Connect a federace](../../active-directory/hybrid/how-to-connect-fed-whatis.md)

## <a name="create-and-populate-an-azure-ad-instance"></a>Vytvoření a naplnění instance Azure AD

Vytvořte instanci Azure AD a naplňte ji uživateli a skupinami. Azure AD může být počáteční doménou spravovanou službou Azure AD. Azure AD může být také místní Active Directory Domain Services, která je federované s Azure AD.

Další informace najdete v tématech [Integrování místních identit do služby Azure Active Directory](../../active-directory/hybrid/whatis-hybrid-identity.md), [Přidání vlastního názvu domény do Azure AD](../../active-directory/fundamentals/add-custom-domain.md), [Microsoft Azure podporuje federaci s Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/), [Správa adresáře služby Azure AD](../../active-directory/fundamentals/active-directory-whatis.md), [Správa služby Azure AD pomocí rozhraní Windows PowerShell](/powershell/azure/) a [Porty a protokoly, které vyžaduje hybridní identita](../../active-directory/hybrid/reference-connect-ports.md).

## <a name="associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Přiřazení nebo přidání předplatného Azure do služby Azure Active Directory

1. Přidružte své předplatné Azure k Azure Active Directory tím, že adresář považujete za důvěryhodný adresář pro předplatné Azure hostující databázi. Podrobnosti najdete v tématu [přidružení nebo přidání předplatného Azure ke svému tenantovi Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

2. Pomocí přepínání adresářů v Azure Portal přepněte do předplatného přidruženého k doméně.

   > [!IMPORTANT]
   > Každé předplatné služby Azure má vztah důvěryhodnosti s instancí služby Azure AD. To znamená, že tomuto adresáři svěřuje ověřování uživatelů, služeb i zařízení. Několik předplatných může důvěřovat stejnému adresáři, ale jedno předplatné důvěřuje pouze jednomu adresáři. Vztah důvěryhodnosti, který má předplatné s adresářem, se liší od vztahu, který má předplatné se všemi ostatními prostředky ve službě Azure (webové stránky, databáze apod.), což jsou pro předplatné spíše podřízené prostředky. Pokud platnost předplatného vyprší, zastaví se i přístup k těmto dalším prostředkům přidruženým k předplatnému. Adresář však ve službě Azure zůstane a vy k němu můžete přidružit jiné předplatné a pokračovat ve správě uživatelů adresáře. Další informace o prostředcích najdete v tématu [porozumění přístupu k prostředkům v Azure](../../active-directory/external-identities/add-users-administrator.md). Další informace o tomto důvěryhodném vztahu najdete v tématu [Postup přidružení nebo přidání předplatného Azure do Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="azure-ad-admin-with-a-server-in-sql-database"></a>Správce Azure AD se serverem v SQL Database

Každý [Server](logical-servers.md) v Azure (který hostuje SQL Database nebo Azure synapse) začíná s jedním účtem správce serveru, který je správcem celého serveru. Vytvořte druhý účet správce jako účet Azure AD. Tento objekt zabezpečení je vytvořen jako uživatel databáze s omezením v hlavní databázi serveru. Účty správců jsou členy role **db_owner** v každé databázi uživatelů a jako uživatel **dbo** zadejte každou uživatelskou databázi. Další informace o účtech správců najdete v tématu [Správa databází a přihlašovacích](logins-create-manage.md)údajů.

Při použití Azure Active Directory s geografickou replikací musí být správce Azure Active Directory nakonfigurovaný jak pro primární, tak pro sekundární servery. Pokud server nemá správce Azure Active Directory, Azure Active Directory přihlášení a uživatelé obdrží `Cannot connect` chybu serveru.

> [!NOTE]
> Uživatelé, kteří nejsou založeni na účtu Azure AD (včetně účtu správce serveru), nemůžou vytvářet uživatele založené na službě Azure AD, protože nemají oprávnění k ověřování uživatelů navrhovaných databází pomocí Azure AD.

## <a name="provision-azure-ad-admin-sql-managed-instance"></a>Zřízení správce Azure AD (spravovaná instance SQL)

> [!IMPORTANT]
> Postupovat pouze v případě, že zřizujete spravovanou instanci Azure SQL. Tuto operaci může spustit jenom globální správce nebo správce privilegované role v Azure AD.
>
> Ve **verzi Public Preview** můžete přiřadit roli **čtenáři adresáře** ke skupině ve službě Azure AD. Vlastníci skupiny pak můžou přidat identitu spravované instance jako člena této skupiny, což vám umožní zřídit správce Azure AD pro spravovanou instanci SQL. Další informace o této funkci najdete v tématu [role čtečky adresářů v Azure Active Directory pro Azure SQL](authentication-aad-directory-readers-role.md).

Vaše spravovaná instance SQL potřebuje oprávnění ke čtení služby Azure AD, aby bylo možné úspěšně provádět úlohy, jako je například ověřování uživatelů prostřednictvím členství ve skupině zabezpečení nebo vytváření nových uživatelů. Aby to fungovalo, musíte pro čtení Azure AD udělit oprávnění ke spravované instanci SQL. Můžete to provést pomocí Azure Portal nebo PowerShellu.

### <a name="azure-portal"></a>portál Azure

Pokud chcete vašemu účtu SQL Managed instance udělit oprávnění ke čtení Azure AD pomocí Azure Portal, přihlaste se jako globální správce v Azure AD a postupujte podle těchto kroků:

1. V [Azure Portal](https://portal.azure.com)v pravém horním rohu vyberte připojení z rozevíracího seznamu možných aktivních adresářů.

2. Vyberte správnou službu Active Directory jako výchozí službu Azure AD.

   Tento krok propojí předplatné přidružené ke službě Active Directory se službou SQL Managed instance a zajistěte, aby se stejné předplatné používalo jak pro instanci služby Azure AD, tak pro spravovanou instanci SQL.

3. Přejděte do spravované instance SQL, kterou chcete použít pro integraci služby Azure AD.

   ![Snímek obrazovky Azure Portal zobrazující stránku Správce služby Active Directory otevřenou pro vybranou spravovanou instanci SQL](./media/authentication-aad-configure/aad.png)

4. Vyberte banner v horní části stránky Správce služby Active Directory a udělte aktuálnímu uživateli oprávnění.

    ![Snímek obrazovky dialogového okna pro udělení oprávnění spravované instanci SQL pro přístup ke službě Active Directory Je vybráno tlačítko udělení oprávnění.](./media/authentication-aad-configure/grant-permissions.png)

5. Po úspěšném dokončení operace se v pravém horním rohu zobrazí následující oznámení:

    ![Snímek obrazovky s potvrzením, že oprávnění ke čtení služby Active Directory byla pro spravovanou instanci úspěšně aktualizována.](./media/authentication-aad-configure/success.png)

6. Teď si můžete vybrat svého správce Azure AD pro vaši spravovanou instanci SQL. V takovém případě na stránce Správce služby Active Directory vyberte **nastavit správce** .

    ![Snímek obrazovky se zvýrazněným příkazem set admin na stránce pro správu služby Active Directory pro vybranou spravovanou instanci SQL](./media/authentication-aad-configure/set-admin.png)

7. Na stránce správce Azure AD vyhledejte uživatele, vyberte uživatele nebo skupinu, které chcete správce, a pak vyberte **Vybrat**.

   Na stránce Správce služby Active Directory se zobrazují všichni členové a skupiny služby Active Directory. Uživatele nebo skupiny, které jsou zobrazené šedě, nejde vybrat, protože nejsou podporované jako správci Azure AD. Podívejte se na seznam podporovaných správců ve [funkcích a omezeních služby Azure AD](authentication-aad-overview.md#azure-ad-features-and-limitations). Řízení přístupu na základě role v Azure (Azure RBAC) se vztahuje jenom na Azure Portal a není šířené do SQL Database, spravované instance SQL ani do Azure synapse.

    ![Přidat správce Azure Active Directory](./media/authentication-aad-configure/add-azure-active-directory-admin.png)

8. V horní části stránky Správce služby Active Directory vyberte **Uložit**.

    ![Snímek obrazovky se stránkou pro správu služby Active Directory pomocí tlačítka Uložit v horním řádku vedle tlačítek nastavit správce a odebrat správce](./media/authentication-aad-configure/save.png)

    Proces změny správce může trvat několik minut. Pak se nový správce zobrazí v poli Správce služby Active Directory.

Po zřízení správce Azure AD pro spravovanou instanci SQL můžete začít vytvářet objekty zabezpečení serveru Azure AD (přihlášení) pomocí syntaxe [Vytvoření přihlášení](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true) . Další informace najdete v tématu [Přehled spravované instance SQL](../managed-instance/sql-managed-instance-paas-overview.md#azure-active-directory-integration).

> [!TIP]
> Chcete-li později odebrat správce, v horní části stránky Správce služby Active Directory vyberte možnost **Odebrat správce** a pak vyberte **Uložit**.

### <a name="powershell"></a>PowerShell

Pokud chcete udělit oprávnění ke čtení Azure AD spravované instance SQL pomocí PowerShellu, spusťte tento skript:

```powershell
# Gives Azure Active Directory read permission to a Service Principal representing the SQL Managed Instance.
# Can be executed only by a "Global Administrator" or "Privileged Role Administrator" type of user.

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

# Get service principal for your SQL Managed Instance
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

### <a name="powershell-for-sql-managed-instance"></a>PowerShell pro spravovanou instanci SQL

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pokud chcete spustit rutiny PowerShellu, musíte mít Azure PowerShell nainstalovanou a spuštěnou. Podrobné informace najdete v tématu [Instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/).

> [!IMPORTANT]
> Modul Azure Resource Manager PowerShellu (RM) je ještě podporován službou Azure SQL Managed instance, ale všechny budoucí vývojové prostředí jsou pro modul AZ. SQL. V modulu AzureRM bude i nadále docházet k opravám chyb až do prosince 2020.  Argumenty pro příkazy v modulech AZ a v modulech AzureRm jsou v podstatě identické. Další informace o kompatibilitě najdete v tématu [představení nového Azure PowerShell AZ Module](/powershell/azure/new-azureps-module-az).

Pokud chcete zřídit správce Azure AD, spusťte následující příkazy Azure PowerShell:

- Connect-AzAccount
- Select-AzSubscription

Rutiny používané ke zřízení a správě správce Azure AD pro vaši spravovanou instanci SQL jsou uvedené v následující tabulce:

| Název rutiny | Popis |
| --- | --- |
| [Set-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlinstanceactivedirectoryadministrator) |Zřídí správce Azure AD pro spravovanou instanci SQL v aktuálním předplatném. (Musí být z aktuálního předplatného)|
| [Remove-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlinstanceactivedirectoryadministrator) |Odebere správce Azure AD pro spravovanou instanci SQL v aktuálním předplatném. |
| [Get-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlinstanceactivedirectoryadministrator) |Vrátí informace o Správci služby Azure AD pro spravovanou instanci SQL v aktuálním předplatném.|

Následující příkaz načte informace o Správci služby Azure AD pro spravovanou instanci SQL s názvem ManagedInstance01, která je přidružená ke skupině prostředků s názvem ResourceGroup01.

```powershell
Get-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01"
```

Následující příkaz zřídí skupinu správců Azure AD s názvem specializující pro spravovanou instanci SQL s názvem ManagedInstance01. Tento server je přidružený ke skupině prostředků ResourceGroup01.

```powershell
Set-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01" -DisplayName "DBAs" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353b"
```

Následující příkaz odebere správce Azure AD pro spravovanou instanci SQL s názvem ManagedInstanceName01 přidruženou ke skupině prostředků ResourceGroup01.

```powershell
Remove-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstanceName01" -Confirm -PassThru
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Správce Azure AD pro spravovanou instanci SQL můžete také zřídit voláním následujících příkazů rozhraní příkazového řádku:

| Příkaz | Popis |
| --- | --- |
|[AZ SQL mi AD-admin Create](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-create) | Zřídí správce Azure Active Directory pro spravovanou instanci SQL (musí být z aktuálního předplatného). |
|[AZ SQL mi AD-admin DELETE](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-delete) | Odebere správce Azure Active Directory pro spravovanou instanci SQL. |
|[AZ SQL mi AD – seznam správců](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-list) | Vrátí informace o Správci Azure Active Directory aktuálně nakonfigurovaném pro spravovanou instanci SQL. |
|[AZ SQL mi AD – aktualizace pro správce](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-update) | Aktualizuje Správce služby Active Directory pro spravovanou instanci SQL. |

Další informace o příkazech rozhraní příkazového řádku najdete v tématu [AZ SQL mi](/cli/azure/sql/mi).

* * *

## <a name="provision-azure-ad-admin-sql-database"></a>Zřízení správce Azure AD (SQL Database)

> [!IMPORTANT]
> Postupovat pouze v případě, že zřizujete [Server](logical-servers.md) pro SQL Database nebo Azure synapse.

Následující dva postupy vám ukážou, jak zřídit správce Azure Active Directory pro váš server v Azure Portal a pomocí PowerShellu.

### <a name="azure-portal"></a>portál Azure

1. V [Azure Portal](https://portal.azure.com/)v pravém horním rohu výběrem svého připojení vyrozevíracíte seznam možných aktivních adresářů. Vyberte správnou službu Active Directory jako výchozí službu Azure AD. Tento krok propojí službu Active Directory přidruženou k předplatnému se serverem, aby se zajistilo, že se stejné předplatné používá pro Azure AD i server.

2. Vyhledejte a vyberte **SQL Server**.

    ![Vyhledat a vybrat servery SQL](./media/authentication-aad-configure/search-for-and-select-sql-servers.png)

    >[!NOTE]
    > Na této stránce před výběrem **SQL serveru** můžete vybrat **hvězdičku** vedle názvu *, abyste kategorii mohli přidat* a přidat **SQL servery** do levého navigačního panelu.

3. Na stránce **SQL Server** vyberte možnost **Správce služby Active Directory**.

4. Na stránce **Správce služby Active Directory** vyberte **nastavit správce**.

    ![SQL servery – nastavení správce služby Active Directory](./media/authentication-aad-configure/sql-servers-set-active-directory-admin.png)  

5. Na stránce **přidat správce** vyhledejte uživatele, vyberte uživatele nebo skupinu, které mají být správcem, a pak vyberte **Vybrat**. (Na stránce Správce služby Active Directory se zobrazují všichni členové a skupiny služby Active Directory. Uživatele nebo skupiny, které jsou šedé, nelze vybrat, protože nejsou podporovány jako správci služby Azure AD. (Další informace najdete v seznamu podporovaných správců v části **funkce a omezení služby Azure AD** tématu [použití Azure Active Directory ověřování pro ověřování pomocí SQL Database nebo Azure synapse](authentication-aad-overview.md)). Řízení přístupu na základě role Azure (Azure RBAC) se vztahuje jenom na portál a nešíří se na SQL Server.

    ![Vybrat správce Azure Active Directory](./media/authentication-aad-configure/select-azure-active-directory-admin.png)  

6. V horní části stránky **Správce služby Active Directory** vyberte **Uložit**.

    ![Uložit správce](./media/authentication-aad-configure/save-admin.png)

Proces změny správce může trvat několik minut. Pak se nový správce zobrazí v poli **Správce služby Active Directory** .

   > [!NOTE]
   > Při nastavování správce Azure AD se nové jméno správce (uživatel nebo skupina) už ve virtuální hlavní databázi nedá nacházet jako uživatel s ověřováním serverem. Pokud je k dispozici, instalace správce Azure AD se nezdaří. vrácení svého vytvoření a označení, že takový správce (jméno) již existuje. Vzhledem k tomu, že takový uživatel ověřování serveru není součástí služby Azure AD, neproběhne žádné úsilí pro připojení k serveru pomocí ověřování Azure AD.

Chcete-li později odebrat správce, v horní části stránky **Správce služby Active Directory** vyberte možnost **Odebrat správce** a pak vyberte **Uložit**.

### <a name="powershell-for-sql-database-and-azure-synapse"></a>PowerShell pro SQL Database a Azure synapse

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pokud chcete spustit rutiny PowerShellu, musíte mít Azure PowerShell nainstalovanou a spuštěnou. Podrobné informace najdete v tématu [Instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/). Pokud chcete zřídit správce Azure AD, spusťte následující příkazy Azure PowerShell:

- Connect-AzAccount
- Select-AzSubscription

Rutiny používané ke zřízení a správě správce Azure AD pro SQL Database a Azure synapse:

| Název rutiny | Popis |
| --- | --- |
| [Set-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlserveractivedirectoryadministrator) |Zřídí správce Azure Active Directory pro server hostující SQL Database nebo Azure synapse. (Musí být z aktuálního předplatného) |
| [Remove-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlserveractivedirectoryadministrator) |Odebere správce Azure Active Directory pro server hostující SQL Database nebo Azure synapse.|
| [Get-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator) |Vrátí informace o Správci Azure Active Directory aktuálně nakonfigurovaném pro server hostující SQL Database nebo Azure synapse. |

K zobrazení dalších informací pro každý z těchto příkazů použijte příkaz PowerShellu Get-Help. Například, `get-help Set-AzSqlServerActiveDirectoryAdministrator`.

Následující skript zřídí skupinu správců Azure AD s názvem **DBA_Group** (ID objektu `40b79501-b343-44ed-9ce7-da4c8cc7353f` ) pro server **demo_server** ve skupině prostředků s názvem **Skupina-23**:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" -DisplayName "DBA_Group"
```

Vstupní parametr **DisplayName** přijímá buď zobrazované jméno Azure AD, nebo hlavní název uživatele. Příklad: ``DisplayName="John Smith"`` a ``DisplayName="johns@contoso.com"``. Pro skupiny Azure AD se podporuje jenom zobrazované jméno Azure AD.

> [!NOTE]
> Příkaz Azure PowerShell ```Set-AzSqlServerActiveDirectoryAdministrator``` nebrání zřizování správců Azure AD pro nepodporované uživatele. Můžete zřídit nepodporovaného uživatele, ale nemůžete se připojit k databázi.

Následující příklad používá volitelné **objectID**:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" `
    -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> **Identifikátor objectID** služby Azure AD je vyžadován, pokud **Zobrazovaný** název není jedinečný. Chcete-li načíst hodnoty **objectID** a **DisplayName** , použijte část portál Azure Classic služby Active Directory a zobrazte vlastnosti uživatele nebo skupiny.

Následující příklad vrátí informace o aktuálním Správci služby Azure AD pro server:

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
|[AZ SQL Server AD-admin Create](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-create) | Zřídí správce Azure Active Directory pro server hostující SQL Database nebo Azure synapse. (Musí být z aktuálního předplatného) |
|[AZ SQL Server AD – odstranění správce](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-delete) | Odebere správce Azure Active Directory pro server hostující SQL Database nebo Azure synapse. |
|[AZ SQL Server AD – seznam správců](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-list) | Vrátí informace o Správci Azure Active Directory aktuálně nakonfigurovaném pro server hostující SQL Database nebo Azure synapse. |
|[AZ SQL Server AD – aktualizace pro správce](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-update) | Aktualizuje Správce služby Active Directory pro server hostující SQL Database nebo Azure synapse. |

Další informace o příkazech rozhraní příkazového řádku najdete v tématu [AZ SQL Server](/cli/azure/sql/server).

* * *

> [!NOTE]
> Správce Azure Active Directory můžete také zřídit pomocí rozhraní REST API. Další informace najdete v tématu [referenční informace o operacích správy služby REST API a operacích Azure SQL Database pro Azure SQL Database](/rest/api/sql/)

## <a name="configure-your-client-computers"></a>Konfigurace klientských počítačů

Na všech klientských počítačích, ze kterých se vaše aplikace nebo uživatelé připojují k SQL Database nebo Azure synapse pomocí identit Azure AD, musíte nainstalovat následující software:

- .NET Framework 4,6 nebo novější z [https://msdn.microsoft.com/library/5a4x27ek.aspx](/dotnet/framework/install/guide-for-developers) .
- Azure Active Directory knihovny ověřování pro SQL Server (*ADAL.DLL*). Níže jsou uvedené odkazy ke stažení pro instalaci nejnovějšího ovladače SSMS, ODBC a OLE DB, který obsahuje knihovnu *ADAL.DLL* .
  - [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)
  - [ODBC Driver 17 pro SQL Server](/sql/connect/odbc/download-odbc-driver-for-sql-server?view=sql-server-ver15&preserve-view=true)
  - [OLE DB ovladače 18 pro SQL Server](/sql/connect/oledb/download-oledb-driver-for-sql-server?view=sql-server-ver15&preserve-view=true)

Tyto požadavky můžete splnit:

- Instalace nejnovější verze [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) nebo [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt) splňuje požadavky .NET Framework 4,6.
  - SSMS nainstaluje verzi x86 *ADAL.DLL*.
  - SSDT nainstaluje *ADAL.DLL* verze amd64.
  - Nejnovější verze sady Visual Studio ze sady [Visual Studio ke stažení](https://www.visualstudio.com/downloads/download-visual-studio-vs) splňuje požadavek .NET Framework 4,6, ale neinstaluje požadovanou verzi amd64 *ADAL.DLL*.

## <a name="create-contained-users-mapped-to-azure-ad-identities"></a>Vytváření uživatelů s omezením namapovaných na identity Azure AD

Protože spravovaná instance SQL podporuje objekty zabezpečení serveru Azure AD (přihlášení), není nutné používat uživatele databáze s omezením. Objekty zabezpečení (přihlášení) serveru Azure AD umožňuje vytvářet přihlášení z uživatelů, skupin nebo aplikací Azure AD. To znamená, že můžete ověřit pomocí SQL spravované instance pomocí přihlašovacích údajů serveru Azure AD, nikoli uživatele databáze s omezením. Další informace najdete v tématu [Přehled spravované instance SQL](../managed-instance/sql-managed-instance-paas-overview.md#azure-active-directory-integration). Syntaxi při vytváření objektů zabezpečení serveru Azure AD (přihlášení) najdete v tématu věnovaném [Vytvoření přihlašovacích](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true)údajů.

Ověřování pomocí Azure Active Directory s SQL Database a Azure synapse ale vyžaduje používání uživatelů databáze s omezením na základě identity Azure AD. Uživatel databáze s omezením nemá přihlašovací údaje v hlavní databázi a mapuje se na identitu ve službě Azure AD, která je přidružená k databázi. Identitou Azure AD může být buď jednotlivý uživatelský účet, nebo skupina. Další informace o uživatelích databáze s omezením najdete v tématu databáze [uživatelů s omezením – vytvoření přenosné](/sql/relational-databases/security/contained-database-users-making-your-database-portable)databáze.

> [!NOTE]
> Uživatele databáze (s výjimkou správců) nelze vytvořit pomocí Azure Portal. Role Azure se nešíří do databáze nástroje SQL Database, spravované instance SQL nebo Azure synapse. Role Azure se používají ke správě prostředků Azure a nevztahují se na oprávnění databáze. Například role **přispěvatel SQL Server** neuděluje přístup pro připojení k databázi v SQL Database, spravované instanci SQL nebo Azure synapse. Přístupové oprávnění musí být uděleno přímo v databázi pomocí příkazů jazyka Transact-SQL.

> [!WARNING]
> Speciální znaky jako dvojtečka `:` nebo ampersand, `&` Pokud jsou zahrnuty jako uživatelská jména v příkazech T-SQL a, nejsou `CREATE LOGIN` `CREATE USER` podporovány.

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
> Tento příkaz vyžaduje, aby služba SQL Access Azure AD ("externí poskytovatel") jménem přihlášeného uživatele. Někdy nastane situace, které způsobí, že Azure AD vrátí výjimku zpátky do SQL. V těchto případech se uživateli zobrazí chyba SQL 33134, která by měla obsahovat chybovou zprávu specifickou pro Azure AD. Ve většině případů tato chyba znamená, že přístup je odepřený nebo že se uživatel musí zaregistrovat v MFA pro přístup k prostředku nebo že přístup mezi aplikacemi první strany musí být zpracován prostřednictvím předvedení. V prvních dvou případech je problém obvykle způsoben zásadami podmíněného přístupu, které jsou nastaveny v tenantovi Azure AD uživatele: znemožňuje uživateli přístup k externímu poskytovateli. Aktualizace zásad podmíněného přístupu tak, aby povolovala přístup k aplikaci "00000002-0000-0000-C000-000000000000" (ID aplikace Graph API Azure AD) by měla tento problém vyřešit. V případě, že chyba znamená, že přístup mezi aplikacemi první strany musí být zpracován prostřednictvím předvedení, problém je proto, že uživatel je přihlášený jako instanční objekt. Příkaz by měl být úspěšný, pokud ho spustí uživatel.

> [!TIP]
> Nemůžete přímo vytvořit uživatele z jiné Azure Active Directory než Azure Active Directory, která je přidružená k vašemu předplatnému Azure. Do skupiny služby Active Directory ve službě Active Directory klienta se ale dají přidat i členové jiných aktivních adresářů, které jsou importované uživatele v přidružené službě Active Directory (označované jako externí uživatelé). Když pro tuto skupinu AD vytvoříte uživatele databáze s omezením, můžou uživatelé z externí služby Active Directory získat přístup k SQL Database.

Další informace o vytváření uživatelů databáze s omezením na základě Azure Active Directory identit naleznete v tématu [Create User (Transact-SQL)](/sql/t-sql/statements/create-user-transact-sql).

> [!NOTE]
> Odebráním správce Azure Active Directory pro server znemožníte uživateli ověřování Azure AD, aby se připojil k serveru. V případě potřeby je možné uživatele Azure AD, kteří se nedají použít, vyřadit ručně pomocí správce SQL Database.

> [!NOTE]
> Pokud obdržíte **časový limit připojení**, možná budete muset nastavit `TransparentNetworkIPResolution` parametr připojovacího řetězce na hodnotu NEPRAVDA. Další informace najdete v tématu [Chyba vypršení časového limitu připojení .NET Framework 4.6.1-konfiguruje nastavení transparentnetworkipresolution](/archive/blogs/dataaccesstechnologies/connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution).

Když vytvoříte uživatele databáze, obdrží tento uživatel oprávnění **připojit** a může se k této databázi připojit jako člen **veřejné** role. Zpočátku jsou jedinou oprávnění k dispozici pro uživatele jakákoli oprávnění udělená **veřejné** roli nebo jakákoli oprávnění udělená všem SKUPINÁM Azure AD, které jsou členy. Po zřízení uživatele databáze s omezením založeného na službě Azure AD můžete uživateli udělit další oprávnění stejným způsobem jako oprávnění pro jakýkoli jiný typ uživatele. Typicky udělte oprávnění databázovým rolím a přidávají uživatele do rolí. Další informace najdete v tématu [základy oprávnění k databázovému stroji](https://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx). Další informace o zvláštních rolích SQL Database najdete [v tématu Správa databází a přihlášení v Azure SQL Database](logins-create-manage.md).
Uživatelský účet federované domény, který je importován do spravované domény jako externí uživatel, musí používat identitu spravované domény.

> [!NOTE]
> Uživatelé Azure AD jsou označeni v metadatech databáze typu E (EXTERNAL_USER) a pro skupiny s typem X (EXTERNAL_GROUPS). Další informace najdete v tématu [Sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql).

## <a name="connect-to-the-database-using-ssms-or-ssdt"></a>Připojení k databázi pomocí SSMS nebo SSDT  

Pokud chcete potvrdit, že správce Azure AD je správně nastavený, připojte se k **Hlavní** databázi pomocí účtu správce Azure AD.
Pokud chcete zřídit uživatele databáze s omezením založené na službě Azure AD (jiný než správce serveru, který je vlastníkem databáze), připojte se k databázi pomocí identity Azure AD, která má přístup k databázi.

> [!IMPORTANT]
> Podpora ověřování Azure Active Directory je k dispozici v [SQL Server 2016 Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) a [datových nástrojích SQL Server](/sql/ssdt/download-sql-server-data-tools-ssdt) v aplikaci Visual Studio 2015. Verze SSMS ze srpna 2016 obsahuje také podporu univerzálního ověřování služby Active Directory, která správcům umožňuje vyžadovat Multi-Factor Authentication pomocí telefonního hovoru, textové zprávy, čipové karty s kódem PIN nebo oznámením o mobilní aplikaci.

## <a name="using-an-azure-ad-identity-to-connect-using-ssms-or-ssdt"></a>Použití identity Azure AD pro připojení pomocí SSMS nebo SSDT

Následující postupy ukazují, jak se připojit k SQL Database pomocí identity Azure AD s využitím SQL Server Management Studio nebo databázových nástrojů SQL Server.

### <a name="active-directory-integrated-authentication"></a>Integrované ověřování služby Active Directory

Tuto metodu použijte, pokud jste se přihlásili k Windows pomocí přihlašovacích údajů Azure Active Directory z federované domény nebo spravované domény, která je nakonfigurovaná pro bezproblémové jednotné přihlašování pro předávací a zatřiďovací ověřování pomocí hesla. Další informace najdete v tématu [Azure Active Directory bezproblémové jednotné přihlašování](../../active-directory/hybrid/how-to-connect-sso.md).

1. Spusťte Management Studio nebo datové nástroje a v dialogovém okně **připojit k serveru** (nebo **se připojte k databázovému stroji**) v poli **ověřování** vyberte **Azure Active Directory integrováno**. Není potřeba žádné heslo, nebo ho nejde zadat, protože pro připojení se zobrazí vaše existující přihlašovací údaje.

   ![Vybrat integrované ověřování Active Directory][11]

2. Vyberte tlačítko **Možnosti** a na stránce **Vlastnosti připojení** do pole **připojit k databázi** zadejte název uživatelské databáze, ke které se chcete připojit. Další informace najdete v článku [vícefaktorové ověřování Azure AD](authentication-mfa-ssms-overview.md#azure-ad-domain-name-or-tenant-id-parameter) na rozdíl mezi vlastnostmi připojení pro SSMS 17. x a 18. x.

   ![Vyberte název databáze.][13]

### <a name="active-directory-password-authentication"></a>Ověřování hesla služby Active Directory

Tuto metodu použijte při připojení k hlavnímu názvu Azure AD pomocí spravované domény Azure AD. Můžete ji také použít pro federované účty bez přístupu k doméně, například při vzdálené práci.

Tuto metodu použijte, chcete-li provést ověření v databázi v SQL Database nebo spravované instanci SQL s uživateli identity pouze cloudu Azure AD, nebo s použitím hybridních identit Azure AD. Tato metoda podporuje uživatele, kteří chtějí používat pověření systému Windows, ale jejich místní počítač není připojen k doméně (například pomocí vzdáleného přístupu). V takovém případě může uživatel systému Windows označit svůj účet domény a heslo a ověřit ho v databázi v SQL Database, spravované instanci SQL nebo Azure synapse.

1. Spusťte Management Studio nebo datové nástroje a v dialogovém okně **připojit k serveru** (nebo **se připojte k databázovému stroji**) v poli **ověřování** vyberte možnost **Azure Active Directory-heslo**.

2. Do pole **uživatelské jméno** zadejte Azure Active Directory uživatelské jméno ve formátu **uživatelské jméno \@ Domain.com**. Uživatelské jméno musí být účet z Azure Active Directory nebo účet ze spravované nebo federované domény s Azure Active Directory.

3. Do pole **heslo** zadejte heslo uživatele pro účet Azure Active Directory nebo účet spravovaného nebo federovaného doménového účtu.

    ![Vybrat ověřování hesla služby AD][12]

4. Vyberte tlačítko **Možnosti** a na stránce **Vlastnosti připojení** do pole **připojit k databázi** zadejte název uživatelské databáze, ke které se chcete připojit. (Viz obrázek v předchozí možnosti.)

### <a name="active-directory-interactive-authentication"></a>Interaktivní ověřování služby Active Directory

Tuto metodu použijte pro interaktivní ověřování s nebo bez Multi-Factor Authentication (MFA) s heslem, které se požaduje interaktivně. Tato metoda se dá použít k ověření databáze v SQL Database, spravované instanci SQL a Azure synapse pro uživatele identity jenom pro cloudové identity Azure AD nebo pro uživatele, kteří používají hybridní identity Azure AD.

Další informace najdete v tématu [použití ověřování Multi-Factor Azure AD s SQL Database a Azure synapse (podpora SSMS pro MFA)](authentication-mfa-ssms-overview.md).

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>Připojení z klientské aplikace pomocí identity Azure AD

Následující postupy ukazují, jak se připojit k SQL Database s identitou Azure AD z klientské aplikace.

### <a name="active-directory-integrated-authentication"></a>Integrované ověřování služby Active Directory

Pokud chcete použít integrované ověřování systému Windows, musí být služba Active Directory vaší domény federované s Azure Active Directory, nebo by měla být spravovaná doména, která je nakonfigurovaná na bezproblémové jednotné přihlašování pro ověřování pomocí předávacího nebo hesla hash. Další informace najdete v tématu [Azure Active Directory bezproblémové jednotné přihlašování](../../active-directory/hybrid/how-to-connect-sso.md).

> [!NOTE]
> [MSAL.NET (Microsoft. identity. Client)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#roadmap) pro integrované ověřování systému Windows není podporováno pro bezproblémové jednotné přihlašování pro předávací a zatřiďovací ověřování pomocí hesla.

Klientská aplikace (nebo služba), která se připojuje k databázi, musí být spuštěná na počítači připojeném k doméně v rámci pověření domény uživatele.

Pokud se chcete připojit k databázi pomocí integrovaného ověřování a identity Azure AD, musí být klíčové slovo ověřování v připojovacím řetězci databáze nastavené na `Active Directory Integrated` . Následující ukázka kódu jazyka C# používá rozhraní ADO .NET.

```csharp
string ConnectionString = @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Klíčové slovo připojovacího řetězce `Integrated Security=True` není podporováno pro připojení k Azure SQL Database. Při vytváření připojení rozhraní ODBC budete muset odebrat mezery a nastavit ověřování na ' ActiveDirectoryIntegrated '.

### <a name="active-directory-password-authentication"></a>Ověřování hesla služby Active Directory

Pokud se chcete připojit k databázi pomocí uživatelských účtů identit jenom cloudu Azure AD nebo těch, kteří používají hybridní identity Azure AD, musí být klíčové slovo ověřování nastavené na `Active Directory Password` . Připojovací řetězec musí obsahovat klíčová slova ID uživatele/UID a heslo/heslo a hodnoty. Následující ukázka kódu jazyka C# používá rozhraní ADO .NET.

```csharp
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Přečtěte si další informace o metodách ověřování Azure AD pomocí ukázkových ukázek kódu dostupných v [ukázce Azure AD Authentication GitHubu](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth).

## <a name="azure-ad-token"></a>Token Azure AD

Tato metoda ověřování umožňuje službám střední vrstvy získat tokeny [JSON web tokeny (Jwt)](../../active-directory/develop/id-tokens.md) pro připojení k databázi v SQL Database, spravované instanci SQL nebo Azure synapse získáním tokenu z Azure AD. Tato metoda umožňuje různé scénáře aplikací, včetně identit služeb, instančních objektů a aplikací pomocí ověřování založeného na certifikátech. Chcete-li použít ověřování pomocí tokenu Azure AD, musíte provést čtyři základní kroky:

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

Další informace najdete v [blogu o SQL Server zabezpečení](/archive/blogs/sqlsecurity/token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth). Informace o přidání certifikátu najdete v tématu Začínáme [s ověřováním na základě certifikátů v Azure Active Directory](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).

### <a name="sqlcmd"></a>sqlcmd

Následující příkazy se připojují pomocí nástroje Sqlcmd verze 13,1, který je k dispozici na [webu Download Center](https://www.microsoft.com/download/details.aspx?id=53591).

> [!NOTE]
> `sqlcmd` pomocí `-G` příkazu nefunguje u systémových identit a vyžaduje přihlášení k hlavnímu uživateli.

```cmd
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="troubleshoot-azure-ad-authentication"></a>Řešení potíží s ověřováním Azure AD

Pokyny k řešení problémů s ověřováním Azure AD najdete na následujícím blogu: <https://techcommunity.microsoft.com/t5/azure-sql-database/troubleshooting-problems-related-to-azure-ad-authentication-with/ba-p/1062991>

## <a name="next-steps"></a>Další kroky

- Přehled přihlašovacích údajů, uživatelů, databázových rolí a oprávnění v SQL Database najdete v tématech [přihlášení, uživatelé, databázové role a uživatelské účty](logins-create-manage.md).
- Další informace o objektech zabezpečení databáze najdete v tématu [Objekty zabezpečení](/sql/relational-databases/security/authentication-access/principals-database-engine).
- Další informace o databázových rolích najdete v tématu věnovaném [databázovým rolím](/sql/relational-databases/security/authentication-access/database-level-roles).
- Další informace o pravidlech brány firewall pro SQL Database najdete v tématu [Pravidla brány firewall služby SQL Database](firewall-configure.md).
- Informace o tom, jak nastavit uživatele hosta Azure AD jako správce Azure AD, najdete v tématu [Vytvoření uživatelů hosta Azure AD a nastavení jako správce Azure AD](authentication-aad-guest-users.md).
- Informace o instančních objektech se službou Azure SQL najdete v tématu [Vytvoření uživatelů Azure AD pomocí aplikací Azure AD](authentication-aad-service-principal-tutorial.md) .

<!--Image references-->

[11]: ./media/authentication-aad-configure/active-directory-integrated.png
[12]: ./media/authentication-aad-configure/12connect-using-pw-auth2.png
[13]: ./media/authentication-aad-configure/13connect-to-db2.png
