---
title: Vytvoření uživatelů služby Azure AD pomocí instančních objektů
description: Tento kurz vás provede vytvořením uživatele Azure AD s aplikacemi Azure AD (instančními objekty) v Azure SQL Database a Azure synapse Analytics.
ms.service: sql-database
ms.subservice: security
ms.custom: azure-synapse
ms.topic: tutorial
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 10/21/2020
ms.openlocfilehash: 6231e4631c19aa3595fa85ca0aa7997861de65a3
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92675031"
---
# <a name="tutorial-create-azure-ad-users-using-azure-ad-applications"></a>Kurz: vytvoření uživatelů Azure AD pomocí aplikací Azure AD

[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

> [!NOTE]
> Tento článek je ve **verzi Public Preview** . Další informace najdete v tématu [Azure Active Directory instančního objektu se službou Azure SQL](authentication-aad-service-principal.md). Tento článek bude používat Azure SQL Database k předvedení nezbytných kroků kurzu, ale může se obdobně použít pro [Azure synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md).

Tento článek vás provede procesem vytvoření uživatelů Azure AD v Azure SQL Database pomocí instančních objektů Azure (aplikace Azure AD). Tato funkce už ve spravované instanci SQL Azure existuje, ale teď se zavádí v Azure SQL Database a Azure synapse Analytics. Pro podporu tohoto scénáře musí být identita Azure AD vygenerovaná a přiřazená k logickému serveru Azure SQL.

Další informace o ověřování Azure AD pro Azure SQL najdete v článku [použití Azure Active Directory ověřování](authentication-aad-overview.md).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> - Přiřazení identity k logickému serveru Azure SQL
> - Přiřazení oprávnění čtenářů adresářů k identitě logického serveru SQL
> - Vytvoření instančního objektu (aplikace služby Azure AD) ve službě Azure AD
> - Vytvoření uživatele instančního objektu ve službě Azure SQL Database
> - Vytvoření jiného uživatele Azure AD v SQL Database pomocí uživatele instančního objektu služby Azure AD

## <a name="prerequisites"></a>Předpoklady

- Existující [Azure SQL Database](single-database-create-quickstart.md) nebo nasazení služby [Azure synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) . Předpokládáme, že pro účely tohoto kurzu máte pracovní SQL Database.
- Přístup k již existujícímu Azure Active Directory.
- [AZ. SQL 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) Module nebo vyšší je potřeba při použití prostředí PowerShell k nastavení individuální aplikace Azure AD jako správce Azure AD pro Azure SQL. Ujistěte se, že jste provedli upgrade na nejnovější modul.

## <a name="assign-an-identity-to-the-azure-sql-logical-server"></a>Přiřazení identity k logickému serveru Azure SQL

1. Připojte se k vašemu Azure Active Directory. Bude nutné najít ID tenanta. Dá se najít tak, že do [Azure Portal](https://portal.azure.com)zařadíte do svého prostředku **Azure Active Directory** . V podokně **Přehled** by se mělo zobrazit vaše **ID tenanta** . Spusťte následující příkaz PowerShellu:

    - Nahraďte `<TenantId>` **ID tenanta** .

    ```powershell
    Connect-AzAccount -Tenant <TenantId>
    ```

    Záznam `TenantId` pro budoucí použití v tomto kurzu.

1. Vygenerujte a přiřaďte identitu Azure AD k logickému serveru Azure SQL. Spusťte následující příkaz prostředí PowerShell:

    - Nahraďte `<resource group>` a `<server name>` s vašimi prostředky. Pokud je název vašeho serveru `myserver.database.windows.net` , nahraďte parametr `<server name>` `myserver` .

    ```powershell
    Set-AzSqlServer -ResourceGroupName <resource group> -ServerName <server name> -AssignIdentity
    ```

    Další informace najdete v příkazu [set-AzSqlServer](/powershell/module/az.sql/set-azsqlserver) .

    > [!IMPORTANT]
    > Pokud je pro logický Server Azure SQL nastavená identita Azure AD, musí být oprávnění [**čtenářům adresáře**](../../active-directory/roles/permissions-reference.md#directory-readers) udělená identitě. Provedeme vás tento krok v následující části. Tento krok **nepřeskakujte** , protože ověřování Azure AD přestane fungovat.

    - Pokud jste v minulosti použili příkaz [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) s parametrem `AssignIdentity` pro nové vytvoření SQL serveru, budete muset příkaz [set-AzSqlServer](/powershell/module/az.sql/set-azsqlserver) spustit později jako samostatný příkaz, který tuto vlastnost povolí v prostředcích infrastruktury Azure.

1. Ověření identity serveru bylo úspěšně přiřazeno. Spusťte následující příkaz prostředí PowerShell:

    - Nahraďte `<resource group>` a `<server name>` s vašimi prostředky. Pokud je název vašeho serveru `myserver.database.windows.net` , nahraďte parametr `<server name>` `myserver` .
    
    ```powershell
    $xyz = Get-AzSqlServer  -ResourceGroupName <resource group> -ServerName <server name>
    $xyz.identity
    ```

    Výstup by měl zobrazovat `PrincipalId` , `Type` a `TenantId` . Přiřazená identita je `PrincipalId` .

1. Identitu můžete také ověřit tak, že na [Azure Portal](https://portal.azure.com)kliknete.

    - V části prostředek **Azure Active Directory** přejít na **podnikové aplikace** . Zadejte název logického serveru SQL. Uvidíte, že obsahuje **ID objektu** připojené k prostředku.
    
    :::image type="content" source="media/authentication-aad-service-principals-tutorial/enterprise-applications-object-id.png" alt-text="ID objektu":::

## <a name="assign-directory-readers-permission-to-the-sql-logical-server-identity"></a>Přiřazení oprávnění čtenářů adresářů k identitě logického serveru SQL

Aby identita přiřazená službě Azure AD mohla správně fungovat pro Azure SQL, `Directory Readers` musí být oprávnění služby Azure AD udělené identitě serveru.

Pokud chcete toto požadované oprávnění udělit, spusťte následující skript.

> [!NOTE] 
> Tento skript musí být spuštěný pomocí Azure AD `Global Administrator` nebo `Privileged Roles Administrator` .
>
> Ve **verzi Public Preview** můžete tuto roli přiřadit `Directory Readers` ke skupině ve službě Azure AD. Vlastníci skupiny pak můžou přidat spravovanou identitu jako člena této skupiny, což by obejít potřebu `Global Administrator` nebo `Privileged Roles Administrator` pro udělení této `Directory Readers` role. Další informace o této funkci najdete v tématu [role čtečky adresářů v Azure Active Directory pro Azure SQL](authentication-aad-directory-readers-role.md).

- Nahraďte `<TenantId>` `TenantId` dříve shromážděnými.
- Nahraďte `<server name>` názvem logického serveru SQL. Pokud je název vašeho serveru `myserver.database.windows.net` , nahraďte parametr `<server name>` `myserver` .

```powershell
# This script grants Azure "Directory Readers" permission to a Service Principal representing the Azure SQL logical server
# It can be executed only by a "Global Administrator" or "Privileged Roles Administrator" type of user.
# To check if the "Directory Readers" permission was granted, execute this script again

Import-Module AzureAD
Connect-AzureAD -TenantId "<TenantId>"    #Enter your actual TenantId
$AssignIdentityName = "<server name>"     #Enter Azure SQL logical server name
 
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
$roleMember = Get-AzureADServicePrincipal -SearchString $AssignIdentityName
$roleMember.Count
if ($roleMember -eq $null) {
    Write-Output "Error: No Service Principals with name '$($AssignIdentityName)', make sure that AssignIdentityName parameter was entered correctly."
    exit
}

if (-not ($roleMember.Count -eq 1)) {
    Write-Output "Error: More than one service principal with name pattern '$($AssignIdentityName)'"
    Write-Output "Dumping selected service principals...."
    $roleMember
    exit
}
 
# Check if service principal is already member of readers role
$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
$selDirReader = $allDirReaders | where{$_.ObjectId -match $roleMember.ObjectId}
 
if ($selDirReader -eq $null) {
    # Add principal to readers role
    Write-Output "Adding service principal '$($AssignIdentityName)' to 'Directory Readers' role'..."
    Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId $roleMember.ObjectId
    Write-Output "'$($AssignIdentityName)' service principal added to 'Directory Readers' role'..."
 
    #Write-Output "Dumping service principal '$($AssignIdentityName)':"
    #$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
    #$allDirReaders | where{$_.ObjectId -match $roleMember.ObjectId}
} else {
    Write-Output "Service principal '$($AssignIdentityName)' is already member of 'Directory Readers' role'."
}
```

> [!NOTE]
> Výstup z tohoto skriptu uvedeného výše bude označovat, jestli se k identitě udělilo oprávnění čtenáři adresáře. Pokud si nejste jistí, jestli mu bylo udělené oprávnění, můžete skript znovu spustit.

Podobný přístup k nastavení oprávnění **čtenářů adresáře** pro SPRAVOVANOU instanci SQL najdete v tématu [zřízení správce Azure AD (SQL Managed instance)](authentication-aad-configure.md#powershell).

## <a name="create-a-service-principal-an-azure-ad-application-in-azure-ad"></a>Vytvoření instančního objektu (aplikace služby Azure AD) ve službě Azure AD

1. Pokud chcete [svoji aplikaci zaregistrovat a nastavit oprávnění](active-directory-interactive-connect-azure-sql-db.md#register-your-app-and-set-permissions), postupujte podle pokynů v příručce.

    Nezapomeňte přidat **oprávnění aplikace** i **delegovaná oprávnění** .

    :::image type="content" source="media/authentication-aad-service-principals-tutorial/aad-apps.png" alt-text="ID objektu":::

    :::image type="content" source="media/authentication-aad-service-principals-tutorial/aad-app-registration-api-permissions.png" alt-text="ID objektu":::

2. Také budete muset vytvořit tajný klíč klienta pro přihlášení. Pomocí příručky sem [Nahrajte certifikát nebo vytvořte tajný klíč pro přihlášení](../../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options).

3. Zaznamenejte následující z registrace aplikace. Měl by být k dispozici v podokně s **přehledem** :
    - **ID aplikace**
    - **ID tenanta** – mělo by to být stejné jako před

V tomto kurzu budeme používat *AppSP* jako hlavní instanční objekt a *Mojeapl* jako druhý uživatel instančního objektu, který se vytvoří v Azure SQL pomocí *AppSP* . Budete muset vytvořit dvě aplikace, *AppSP* a *MyApp* .

Další informace o tom, jak vytvořit aplikaci Azure AD, najdete v článku [Postupy: použití portálu k vytvoření aplikace a instančního objektu služby Azure AD, který má přístup k prostředkům](../../active-directory/develop/howto-create-service-principal-portal.md).

### <a name="permissions-required-to-set-or-unset-the-azure-ad-admin"></a>Oprávnění požadovaná k nastavení nebo zrušení správce Azure AD

Aby instanční objekt nastavil nebo oddělil správce Azure AD pro Azure SQL, je potřeba další oprávnění API. [Adresář. Read. všechna](https://docs.microsoft.com/graph/permissions-reference#application-permissions-18) oprávnění Application API se budou muset do vaší aplikace přidat ve službě Azure AD.

:::image type="content" source="media/authentication-aad-service-principals-tutorial/aad-directory-reader-all-permissions.png" alt-text="ID objektu":::

Instanční objekt bude také potřebovat roli [**přispěvatel SQL Server**](../../role-based-access-control/built-in-roles.md#sql-server-contributor) pro SQL Database nebo roli [**Přispěvatel spravované instance SQL**](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor) pro spravovanou instanci SQL.

> [!NOTE]
> I když je služba Azure AD Graph API zastaralá, **adresář. Reader. všechna** oprávnění se pořád týkají tohoto kurzu. Rozhraní API pro Microsoft Graph se nevztahuje na tento kurz.

## <a name="create-the-service-principal-user-in-azure-sql-database"></a>Vytvořit uživatele instančního objektu v Azure SQL Database

Po vytvoření instančního objektu ve službě Azure AD vytvořte uživatele v SQL Database. Budete se muset připojit k SQL Database s platným přihlašovacím jménem s oprávněními k vytváření uživatelů v databázi.

1. Pomocí následujícího příkazu T-SQL vytvořte v SQL Database uživatele *AppSP* :

    ```sql
    CREATE USER [AppSP] FROM EXTERNAL PROVIDER
    GO
    ```

2. Udělte `db_owner` oprávnění *AppSP* , která umožňuje uživateli vytvářet v databázi další uživatele Azure AD.

    ```sql
    EXEC sp_addrolemember 'db_owner', [AppSP]
    GO
    ```

    Další informace najdete v tématu [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql)

    Alternativně `ALTER ANY USER` lze místo poskytování role udělit oprávnění `db_owner` . Tato akce umožní instančnímu objektu přidat další uživatele služby Azure AD.

    ```sql
    GRANT ALTER ANY USER TO [AppSp]
    GO
    ```

    > [!NOTE]
    > Výše uvedené nastavení není vyžadováno, pokud je *AppSP* nastaveno jako správce Azure AD pro server. K nastavení instančního objektu jako správce služby AD pro logický server SQL můžete použít příkazy Azure Portal, PowerShellu nebo rozhraní příkazového řádku Azure. Další informace najdete v tématu [zřízení správce Azure AD (SQL Database)](authentication-aad-configure.md?tabs=azure-powershell#powershell-for-sql-database-and-azure-synapse).

## <a name="create-an-azure-ad-user-in-sql-database-using-an-azure-ad-service-principal"></a>Vytvoření uživatele Azure AD v SQL Database pomocí instančního objektu služby Azure AD

> [!IMPORTANT]
> Instanční objekt použitý k přihlášení do SQL Database musí mít tajný klíč klienta. Pokud to ještě nemáte, postupujte podle pokynů v části [Vytvoření instančního objektu (aplikace Azure AD) ve službě Azure AD](#create-a-service-principal-an-azure-ad-application-in-azure-ad). Tento tajný klíč klienta je nutné přidat jako vstupní parametr ve skriptu níže.

1. Pomocí následujícího skriptu vytvořte uživatele instančního objektu služby Azure AD *Mojeapl* pomocí instančního objektu *AppSP* .

    - Nahraďte `<TenantId>` `TenantId` dříve shromážděnými.
    - Nahraďte `<ClientId>` `ClientId` dříve shromážděnými.
    - Nahraďte `<ClientSecret>` dříve vytvořeným tajným klíčem klienta.
    - Nahraďte `<server name>` názvem logického serveru SQL. Pokud je název vašeho serveru `myserver.database.windows.net` , nahraďte parametr `<server name>` `myserver` .
    - Nahraďte `<database name>` názvem SQL Database.

    ```powershell
    # PowerShell script for creating a new SQL user called myapp using application AppSP with secret

    $tenantId = "<TenantId>"   #  tenantID (Azure Directory ID) were AppSP resides
    $clientId = "<ClientId>"   #  AppID also ClientID for AppSP     
    $clientSecret = "<ClientSecret>"   #  client secret for AppSP 
    $Resource = "https://database.windows.net/"
    
    $adalPath  = "${env:ProgramFiles}\WindowsPowerShell\Modules\AzureRM.profile\5.8.3"
    # To install the latest AzureRM.profile version execute  -Install-Module -Name AzureRM.profile
    $adal      = "$adalPath\Microsoft.IdentityModel.Clients.ActiveDirectory.dll"
    $adalforms = "$adalPath\Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll"
    [System.Reflection.Assembly]::LoadFrom($adal) | Out-Null
      $resourceAppIdURI = 'https://database.windows.net/'

      # Set Authority to Azure AD Tenant
      $authority = 'https://login.windows.net/' + $tenantId

      $ClientCred = [Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential]::new($clientId, $clientSecret)
      $authContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]::new($authority)
      $authResult = $authContext.AcquireTokenAsync($resourceAppIdURI,$ClientCred)
      $Tok = $authResult.Result.CreateAuthorizationHeader()
      $Tok=$Tok.Replace("Bearer ","")
      Write-host "token"
      $Tok
      Write-host  " "

    $SQLServerName = "<server name>"    # Azure SQL logical server name 
    Write-Host "Create SQL connectionstring"
    $conn = New-Object System.Data.SqlClient.SQLConnection 
    $DatabaseName = "<database name>"     # Azure SQL database name
    $conn.ConnectionString = "Data Source=$SQLServerName.database.windows.net;Initial Catalog=$DatabaseName;Connect Timeout=30"
    $conn.AccessToken = $Tok
    
    Write-host "Connect to database and execute SQL script"
    $conn.Open() 
    $ddlstmt = 'CREATE USER [myapp] FROM EXTERNAL PROVIDER;'
    Write-host " "
    Write-host "SQL DDL command"
    $ddlstmt
    $command = New-Object -TypeName System.Data.SqlClient.SqlCommand($ddlstmt, $conn)       
    
    Write-host "results"
    $command.ExecuteNonQuery()
    $conn.Close()  
    ``` 

    Alternativně můžete použít ukázku kódu v blogu, [ověřování instančního objektu služby Azure AD v ukázce kódu SQL DB](https://techcommunity.microsoft.com/t5/azure-sql-database/azure-ad-service-principal-authentication-to-sql-db-code-sample/ba-p/481467). Upravte skript pro spuštění příkazu DDL `CREATE USER [myapp] FROM EXTERNAL PROVIDER` . Stejný skript se dá použít k vytvoření běžného uživatele Azure AD ve SQL Database skupině.

    > [!NOTE]
    > Pokud potřebujete nainstalovat modul AzureRM. Profile, budete muset otevřít PowerShell jako správce. Pomocí následujících příkazů můžete automaticky nainstalovat nejnovější verzi AzureRM. Profile a nastavit `$adalpath` pro výše uvedený skript:
    > 
    > ```powershell
    > Install-Module AzureRM.profile -force
    > Import-Module AzureRM.profile
    > $version = (Get-Module -Name AzureRM.profile).Version.toString()
    > $adalPath = "${env:ProgramFiles}\WindowsPowerShell\Modules\AzureRM.profile\${version}"
    > ```
    
2. Spuštěním následujícího příkazu ověřte, jestli uživatel *MyApp* v databázi existuje:

    ```sql
    SELECT name, type, type_desc, CAST(CAST(sid as varbinary(16)) as uniqueidentifier) as appId from sys.database_principals WHERE name = 'myapp'
    GO
    ```

    Měl by se zobrazit podobný výstup:

    ```output
    name    type    type_desc   appId
    myapp   E   EXTERNAL_USER   6d228f48-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ```

## <a name="next-steps"></a>Další kroky

- [Azure Active Directory instančního objektu se službou Azure SQL](authentication-aad-service-principal.md)
- [Jaké jsou spravované identity prostředků Azure?](../../active-directory/managed-identities-azure-resources/overview.md)
- [Použití spravovaných identit pro App Service a Azure Functions](../../app-service/overview-managed-identity.md)
- [Ověřování instančního objektu služby Azure AD do SQL DB – ukázka kódu](https://techcommunity.microsoft.com/t5/azure-sql-database/azure-ad-service-principal-authentication-to-sql-db-code-sample/ba-p/481467)
- [Instanční objekty aplikace a služby v Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md)
- [Vytvoření instančního objektu Azure s použitím prostředí Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps)
- [Role čtenáři adresáře v Azure Active Directory pro Azure SQL](authentication-aad-directory-readers-role.md)