---
title: Azure Active Directory instančního objektu se službou Azure SQL
description: Aplikace Azure AD (instanční objekty) podporují vytváření uživatelů Azure AD v Azure SQL Database, Managed instance Azure SQL a Azure synapse Analytics.
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 10/21/2020
ms.openlocfilehash: 2ded60f8c57d8c9db374bf77efe6dfd1a71690bc
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92482923"
---
# <a name="azure-active-directory-service-principal-with-azure-sql"></a>Azure Active Directory instančního objektu se službou Azure SQL

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Podpora pro vytváření uživatelů Azure Active Directory (Azure AD) v Azure SQL Database (SQL DB) a [Azure synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) jménem aplikací Azure AD (instanční objekty) jsou momentálně ve **verzi Public Preview**.

> [!NOTE]
> Tato funkce je už pro spravovanou instanci SQL podporovaná.

## <a name="service-principal-azure-ad-applications-support"></a>Podpora instančního objektu (aplikace služby Azure AD)

Tento článek se týká aplikací, které jsou integrované s Azure AD, a jsou součástí registrace Azure AD. Tyto aplikace často potřebují ověřování a přístup k autorizaci do Azure SQL, aby mohli provádět různé úlohy. Tato funkce ve **verzi Public Preview** teď umožňuje instančním objektům vytvořit uživatele služby Azure AD v SQL Database a Azure synapse. Nastala omezení zabraňující tvorbě objektů Azure AD jménem aplikací Azure AD, které se odebraly.

Při registraci aplikace Azure AD pomocí Azure Portal nebo příkazu PowerShellu se v tenantovi Azure AD vytvoří dva objekty:

- aplikační objekt,
- instanční objekt.

Další informace o aplikacích služby Azure AD najdete v tématu [aplikace a objekty zabezpečení služby v Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md) a [Vytvoření instančního objektu Azure pomocí Azure PowerShell](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps).

SQL Database, služba Azure synapse a spravovaná instance SQL podporují následující objekty služby Azure AD:

- Uživatelé Azure AD (spravované, federované a Host)
- Skupiny Azure AD (spravované a federované)
- Aplikace Azure AD 

`CREATE USER [Azure_AD_Object] FROM EXTERNAL PROVIDER`Pro SQL Database a Azure synapse se teď podporuje příkaz T-SQL jménem aplikace služby Azure AD.

## <a name="functionality-of-azure-ad-user-creation-using-service-principals"></a>Funkce vytváření uživatelů Azure AD pomocí instančních objektů

Podpora této funkce je užitečná v procesech automatizace aplikací Azure AD, kde se vytvářejí a udržují objekty služby Azure AD v SQL Database a Azure synapse bez zásahu člověka. Instanční objekty můžou být správcem služby Azure AD pro logický server SQL, a to jako součást skupiny nebo jednotlivého uživatele. Aplikace může automatizovat vytváření objektů Azure AD v SQL Database a Azure synapse při spuštění jako správce systému a nevyžaduje žádná další oprávnění SQL. To umožňuje úplnou automatizaci vytvoření databázového uživatele. Tato funkce je také podporována pro spravovanou identitu přiřazenou systémem a uživatelem přiřazenou identitu. Další informace najdete v tématu [co jsou spravované identity pro prostředky Azure?](../../active-directory/managed-identities-azure-resources/overview.md)

## <a name="enable-service-principals-to-create-azure-ad-users"></a>Povolit instančním objektům vytváření uživatelů Azure AD

Pokud chcete povolit vytvoření objektu Azure AD v SQL Database a Azure synapse jménem aplikace služby Azure AD, vyžadují se následující nastavení:

1. Přiřazení identity serveru
    - Pro nový logický Server Azure SQL spusťte následující příkaz PowerShellu:
    
    ```powershell
    New-AzSqlServer -ResourceGroupName <resource group> -Location <Location name> -ServerName <Server name> -ServerVersion "12.0" -SqlAdministratorCredentials (Get-Credential) -AssignIdentity
    ```

    Další informace najdete v příkazu [New-AzSqlServer](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlserver) .

    - U stávajících logických serverů Azure SQL spusťte následující příkaz:
    
    ```powershell
    Set-AzSqlServer -ResourceGroupName <resource group> -ServerName <Server name> -AssignIdentity
    ```

    Další informace najdete v příkazu [set-AzSqlServer](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlserver) .

    - Pokud chcete zjistit, jestli je identita serveru přiřazená k serveru, spusťte příkaz Get-AzSqlServer.

    > [!NOTE]
    > Identitu serveru lze přiřadit také pomocí příkazů rozhraní příkazového řádku. Další informace najdete v tématu [AZ SQL Server Create](https://docs.microsoft.com/cli/azure/sql/server?view=azure-cli-latest#az-sql-server-create&preserve-view=true) a [AZ SQL Server Update](https://docs.microsoft.com/cli/azure/sql/server?view=azure-cli-latest#az-sql-server-update&preserve-view=true).

2. Udělte [**čtenářům adresáře**](../../active-directory/roles/permissions-reference.md#directory-readers) Azure AD oprávnění k vytvoření nebo přiřazení serveru k identitě serveru.
    - Pokud chcete toto oprávnění udělit, postupujte podle popisu použitého pro spravovanou instanci SQL, který je k dispozici v následujícím článku: [zřízení správce Azure AD (spravovaná instance SQL)](authentication-aad-configure.md?tabs=azure-powershell#provision-azure-ad-admin-sql-managed-instance)
    - Uživatel služby Azure AD, který uděluje toto oprávnění, musí být součástí role správce **globálního správce** nebo **privilegované role** služby Azure AD.

> [!IMPORTANT]
> Kroky 1 a 2 je nutné provést v uvedeném pořadí. Nejdřív vytvořte nebo přiřaďte identitu serveru a pak udělení oprávnění [**čtenářům adresáře**](../../active-directory/roles/permissions-reference.md#directory-readers) . Při vynechání jednoho z těchto kroků dojde k chybě spuštění během vytváření objektu Azure AD v Azure SQL jménem aplikace služby Azure AD.
>
> Pokud k nastavení nebo zrušení správy služby Azure AD používáte instanční objekt, musí mít aplikace taky oprávnění [Directory. Read. All](https://docs.microsoft.com/graph/permissions-reference#application-permissions-18) Application API v Azure AD. Další informace o [oprávněních požadovaných k nastavení správce Azure AD](authentication-aad-service-principal-tutorial.md#permissions-required-to-set-or-unset-the-azure-ad-admin)a podrobné pokyny k vytvoření uživatele Azure AD jménem aplikace Azure AD najdete v tématu [kurz: vytvoření uživatelů Azure AD pomocí aplikací Azure AD](authentication-aad-service-principal-tutorial.md).
>
> Ve **verzi Public Preview**můžete přiřadit roli **čtenáři adresáře** ke skupině ve službě Azure AD. Vlastníci skupiny pak můžou přidat spravovanou identitu jako člena této skupiny, což by obejít nutnost správce **globálních správců** nebo **privilegovaných rolí** , aby udělili roli **čtenářů adresáře** . Další informace o této funkci najdete v tématu [role čtečky adresářů v Azure Active Directory pro Azure SQL](authentication-aad-directory-readers-role.md).

## <a name="troubleshooting-and-limitations-for-public-preview"></a>Řešení potíží a omezení pro verzi Public Preview

- Při vytváření objektů Azure AD v Azure SQL jménem aplikace Azure AD bez povolení identity serveru a udělení oprávnění **čtenářům adresářů** se operace nezdaří a zobrazí se následující možné chyby. Ukázková chyba je určena pro spuštění příkazu prostředí PowerShell pro vytvoření SQL Databaseho uživatele `myapp` v článku [kurz: vytvoření uživatelů Azure AD pomocí aplikací Azure AD](authentication-aad-service-principal-tutorial.md).
    - `Exception calling "ExecuteNonQuery" with "0" argument(s): "'myapp' is not a valid login or you do not have permission. Cannot find the user 'myapp', because it does not exist, or you do not have permission."`
    - `Exception calling "ExecuteNonQuery" with "0" argument(s): "Principal 'myapp' could not be resolved.`
    - `User or server identity does not have permission to read from Azure Active Directory.`
      - U výše uvedených chyb postupujte podle pokynů pro [přiřazení identity k logickému serveru Azure SQL](authentication-aad-service-principal-tutorial.md#assign-an-identity-to-the-azure-sql-logical-server) a [přiřazení oprávnění čtenářům adresáře k identitě logického serveru SQL](authentication-aad-service-principal-tutorial.md#assign-directory-readers-permission-to-the-sql-logical-server-identity).
    > [!NOTE]
    > Výše uvedené chybové zprávy budou změněny před funkcí GA, aby jasně identifikovaly chybějící požadavky na instalaci pro podporu aplikací Azure AD.
- Nastavení aplikace Azure AD jako správce Azure AD pro spravovanou instanci SQL se podporuje jenom pomocí příkazu CLI a příkazu PowerShellu s [AZ. SQL 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) nebo novějším. Další informace najdete v tématu [AZ SQL mi AD-admin Create](https://docs.microsoft.com/cli/azure/sql/mi/ad-admin?view=azure-cli-latest#az-sql-mi-ad-admin-create&preserve-view=true) a [set-AzSqlInstanceActiveDirectoryAdministrator](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstanceactivedirectoryadministrator) Commands. 
    - Pokud chcete použít službu Azure Portal pro spravovanou instanci SQL k nastavení správce Azure AD, možná alternativní řešení je vytvoření skupiny Azure AD. Pak do této skupiny přidejte instanční objekt (aplikace Azure AD) a nastavte tuto skupinu jako správce Azure AD pro spravovanou instanci SQL.
    - Nastavení instančního objektu (aplikace Azure AD) jako správce Azure AD pro SQL Database a Azure synapse se podporuje pomocí příkazů Azure Portal, [PowerShellu](authentication-aad-configure.md?tabs=azure-powershell#powershell-for-sql-database-and-azure-synapse)a rozhraní příkazového [řádku](authentication-aad-configure.md?tabs=azure-cli#powershell-for-sql-database-and-azure-synapse) .
- Použití aplikace Azure AD s instančním objektem z jiného tenanta Azure AD selže při přístupu k SQL Database nebo spravované instanci SQL, která se vytvořila v jiném tenantovi. Instanční objekt přiřazený k této aplikaci musí být ze stejného tenanta jako logický server SQL nebo spravovaná instance.
- [AZ. SQL 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) Module nebo vyšší je potřeba při použití prostředí PowerShell k nastavení individuální aplikace Azure AD jako správce Azure AD pro Azure SQL. Ujistěte se, že jste provedli upgrade na nejnovější modul.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: vytvoření uživatelů Azure AD pomocí aplikací Azure AD](authentication-aad-service-principal-tutorial.md)


