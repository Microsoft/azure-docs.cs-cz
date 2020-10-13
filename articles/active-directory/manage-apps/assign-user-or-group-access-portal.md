---
title: Správa přiřazování uživatelů pro aplikaci v Azure Active Directory
description: Naučte se, jak přiřadit a zrušit přiřazení uživatelů a skupin pro aplikaci, která používá Azure Active Directory pro správu identit.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 02/21/2020
ms.author: kenwith
ms.reviewer: luleon
ms.openlocfilehash: f49377743521e27c2312e95491762ca48d8448c4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90604321"
---
# <a name="manage-user-assignment-for-an-app-in-azure-active-directory"></a>Správa přiřazování uživatelů pro aplikaci v Azure Active Directory

V tomto článku se dozvíte, jak přiřadit uživatele a skupiny k podnikovým aplikacím v Azure Active Directory (Azure AD), a to buď z Azure Portal, nebo pomocí PowerShellu. Když přiřadíte uživatele k aplikaci, zobrazí se aplikace v [mých aplikacích](https://myapps.microsoft.com/) uživatele, aby bylo možné získat snadný přístup. Pokud aplikace zveřejňuje role, můžete uživateli přiřadit také konkrétní roli.

Pro lepší kontrolu je možné nakonfigurovat určité typy podnikových aplikací tak, aby [vyžadovaly přiřazení uživatele](#configure-an-application-to-require-user-assignment). 

> [!IMPORTANT]
> Když přiřadíte skupinu k aplikaci, budou mít přístup jenom uživatelé ve skupině. Přiřazení se neprovádí kaskádovitě pro vnořené skupiny.

> [!NOTE]
> Přiřazení na základě skupin vyžaduje Azure Active Directory Premium edici P1 nebo P2. Přiřazení na základě skupin se podporuje jenom pro skupiny zabezpečení. Vnořené členství ve skupinách a skupiny Microsoft 365 nejsou aktuálně podporovány. Další licenční požadavky na funkce popsané v tomto článku najdete na [stránce s cenami Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory). 

## <a name="configure-an-application-to-require-user-assignment"></a>Konfigurace aplikace tak, aby vyžadovala přiřazení uživatele

S následujícími typy aplikací máte možnost vyžadovat, aby se uživatelé před přístupem k aplikaci přidělili:

- Aplikace nakonfigurované pro federované jednotné přihlašování (SSO) s ověřováním založeném na SAML
- Aplikace proxy aplikací, které používají předběžné ověření Azure Active Directory
- Aplikace založené na platformě aplikace Azure AD, které používají ověřování OAuth 2,0/OpenID Connect po tom, co uživatel nebo správce souhlasí s touto aplikací.

Když se vyžaduje přiřazení uživatele, bude se moct přihlásit jenom uživatelé, které explicitně přiřadíte k aplikaci (ať už prostřednictvím přímého přiřazení uživatelů nebo podle členství ve skupině). Mají přístup k aplikaci na stránce Moje aplikace nebo pomocí přímého odkazu. 

Pokud přiřazení není *vyžadováno*, protože jste tuto možnost nastavili na hodnotu **ne** nebo protože aplikace používá jiný režim jednotného přihlašování, bude mít každý uživatel přístup k aplikaci, pokud má přímý odkaz na aplikaci nebo **adresu URL přístupu uživatele** na stránce **vlastností** aplikace. 

Toto nastavení nemá vliv na to, jestli se aplikace zobrazuje v mých aplikacích. Po přiřazení uživatele nebo skupiny k aplikaci se aplikace zobrazí na panelech uživatelů moje aplikace v aplikaci. Základní informace najdete v tématu [Správa přístupu k aplikacím](what-is-access-management.md).

Vyžadování přiřazení uživatele pro aplikaci:
1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí účtu správce nebo jako vlastník aplikace.
2. Vyberte **Azure Active Directory**. V navigační nabídce vlevo vyberte **podnikové aplikace**.
3. Vyberte aplikaci ze seznamu. Pokud aplikaci nevidíte, začněte psát její název do vyhledávacího pole. Nebo použijte ovládací prvky filtru k výběru typu aplikace, stavu nebo viditelnosti a pak vyberte **použít**.
4. V levé navigační nabídce vyberte **vlastnosti**.
5. Ujistěte se, že je **vyžadováno přiřazení uživatele?** přepínač je nastaven na **hodnotu Ano**.
   > [!NOTE]
   > Pokud je **vyžadováno přiřazení uživatele?** přepínač není k dispozici, můžete k nastavení vlastnosti appRoleAssignmentRequired u instančního objektu použít PowerShell.
6. V horní části obrazovky vyberte tlačítko **Uložit** .

## <a name="assign-or-unassign-users-and-groups-for-an-app-using-the-azure-portal"></a>Přiřazení nebo zrušení přiřazení uživatelů a skupin pro aplikaci pomocí Azure Portal
Další informace o tom, jak přiřadit nebo zrušit přiřazení uživatele nebo skupiny pomocí Azure Portal, najdete v části [rychlý start série při správě aplikací](add-application-portal-assign-users.md).

## <a name="assign-or-unassign-users-and-groups-for-an-app-using-the-graph-api"></a>Přiřazení nebo zrušení přiřazení uživatelů a skupin pro aplikaci pomocí Graph API
K přiřazení nebo zrušení přiřazení uživatelů a skupin pro aplikaci můžete použít Graph API. Další informace najdete v tématu [přiřazení rolí aplikace](https://docs.microsoft.com/graph/api/resources/approleassignment).

## <a name="assign-users-and-groups-to-an-app-using-powershell"></a>Přiřazení uživatelů a skupin k aplikaci pomocí prostředí PowerShell
1. Otevřete příkazový řádek Windows PowerShellu se zvýšenými oprávněními.
   > [!NOTE]
   > Je potřeba nainstalovat modul AzureAD (použijte příkaz `Install-Module -Name AzureAD` ). Pokud se zobrazí výzva k instalaci modulu NuGet nebo nového modulu Azure Active Directory v2 PowerShell, zadejte Y a stiskněte ENTER.
2. Spusťte `Connect-AzureAD` a přihlaste se pomocí uživatelského účtu globálního správce.
3. K přiřazení uživatele a role k aplikaci použijte následující skript:

    ```powershell
    # Assign the values to the variables
    $username = "<Your user's UPN>"
    $app_name = "<Your App's display name>"
    $app_role_name = "<App role display name>"

    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }

    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```
Další informace o tom, jak přiřadit uživatele k roli aplikace, najdete v dokumentaci k [New-AzureADUserAppRoleAssignment](https://docs.microsoft.com/powershell/module/azuread/new-azureaduserapproleassignment?view=azureadps-2.0).

Chcete-li přiřadit skupinu k podnikové aplikaci, je nutné nahradit `Get-AzureADUser` `Get-AzureADGroup` a nahradit `New-AzureADUserAppRoleAssignment` `New-AzureADGroupAppRoleAssignment` .

Další informace o tom, jak přiřadit skupinu k roli aplikace, najdete v dokumentaci k [New-AzureADGroupAppRoleAssignment](https://docs.microsoft.com/powershell/module/azuread/new-azureadgroupapproleassignment?view=azureadps-2.0).

### <a name="example"></a>Příklad

Tento příklad přiřadí uživatele Britta Simon k aplikaci [Microsoft Workplace Analytics](https://products.office.com/business/workplace-analytics) pomocí PowerShellu.

1. V PowerShellu přiřaďte příslušné hodnoty k proměnným $username $app _name a $app _role_name.

    ```powershell
    # Assign the values to the variables
    $username = "britta.simon@contoso.com"
    $app_name = "Workplace Analytics"
    ```
2. V tomto příkladu nevím, jaký je přesný název aplikační role, kterou chceme přiřadit k Britta Simon. Spusťte následující příkazy, abyste získali uživatele ($user) a instanční objekt ($sp) pomocí hlavního názvu uživatele (UPN) a zobrazovaného názvu objektu služby.

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    ```
3. Spusťte příkaz `$sp.AppRoles` pro zobrazení rolí dostupných pro aplikaci pracoviště Analytics. V tomto příkladu chceme přiřadit Britta Simon role analytik (s omezeným přístupem).
   ![Zobrazuje role dostupné uživateli pomocí role Analytics na pracovišti.](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)
4. Přiřaďte k proměnné název role `$app_role_name` .

    ```powershell
    # Assign the values to the variables
    $app_role_name = "Analyst (Limited access)"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    ```
5. Spusťte následující příkaz, který uživateli přiřadí roli aplikace:

    ```powershell
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

## <a name="unassign-users-and-groups-from-an-app-using-powershell"></a>Zrušení přiřazení uživatelů a skupin z aplikace pomocí PowerShellu

1. Otevřete příkazový řádek Windows PowerShellu se zvýšenými oprávněními.
   > [!NOTE]
   > Je potřeba nainstalovat modul AzureAD (použijte příkaz `Install-Module -Name AzureAD` ). Pokud se zobrazí výzva k instalaci modulu NuGet nebo nového modulu Azure Active Directory v2 PowerShell, zadejte Y a stiskněte ENTER.
2. Spusťte `Connect-AzureAD` a přihlaste se pomocí uživatelského účtu globálního správce.
3. Pomocí následujícího skriptu Odeberte uživatele a roli z aplikace:

    ```powershell
    # Store the proper parameters
    $user = get-azureaduser -ObjectId <objectId>
    $spo = Get-AzureADServicePrincipal -ObjectId <objectId>

    #Get the ID of role assignment 
    $assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $spo.ObjectId | Where {$_.PrincipalDisplayName -eq $user.DisplayName}

    #if you run the following, it will show you what is assigned what
    $assignments | Select *

    #To remove the App role assignment run the following command.
    Remove-AzureADServiceAppRoleAssignment -ObjectId $spo.ObjectId -AppRoleAssignmentId $assignments[assignment #].ObjectId
    ```


## <a name="related-articles"></a>Související články

- [Další informace o přístupu koncových uživatelů k aplikacím](end-user-experiences.md)
- [Plánování nasazení Azure AD moje aplikace](access-panel-deployment-plan.md)
- [Správa přístupu k aplikacím](what-is-access-management.md)
 
## <a name="next-steps"></a>Další kroky

- [Zobrazit všechny moje skupiny](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Odebrání přiřazení uživatele nebo skupiny z podnikové aplikace](remove-user-or-group-access-portal.md)
- [Zakázání přihlášení uživatelů pro podnikovou aplikaci](disable-user-sign-in-portal.md)
- [Změna názvu nebo loga podnikové aplikace](change-name-or-logo-portal.md)
