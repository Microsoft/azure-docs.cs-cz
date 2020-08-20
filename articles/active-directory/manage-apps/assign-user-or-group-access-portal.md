---
title: Přiřazení uživatele nebo skupiny k podnikové aplikaci v Azure AD
description: Jak vybrat podnikovou aplikaci, do které se přiřadí uživatel nebo skupina v Azure Active Directory
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7875bfc55d4530f7f56a96599491cab4a98ced04
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2020
ms.locfileid: "88642023"
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>Přiřazení uživatele nebo skupiny k podnikové aplikaci v Azure Active Directory

V tomto článku se dozvíte, jak přiřadit uživatele nebo skupiny k podnikovým aplikacím v Azure Active Directory (Azure AD) v rámci Azure Portal nebo pomocí prostředí PowerShell. Když přiřadíte uživatele k aplikaci, zobrazí se aplikace v [mých aplikacích](https://myapps.microsoft.com/) uživatele, aby bylo možné získat snadný přístup. Pokud aplikace zveřejňuje role, můžete uživateli přiřadit také konkrétní roli.

Pro lepší kontrolu je možné nakonfigurovat určité typy podnikových aplikací tak, aby [vyžadovaly přiřazení uživatele](#configure-an-application-to-require-user-assignment). 

Pokud chcete [přiřadit uživatele nebo skupinu k podnikové aplikaci](#assign-users-or-groups-to-an-app-via-the-azure-portal), musíte se přihlásit jako globální správce, správce aplikace, správce cloudové aplikace nebo přiřazený vlastník podnikové aplikace.

> [!IMPORTANT]
> Když přiřadíte skupinu k aplikaci, budou mít přístup jenom uživatelé ve skupině. Přiřazení se neprovádí kaskádovitě pro vnořené skupiny.

> [!NOTE]
> Přiřazení na základě skupin vyžaduje Azure Active Directory Premium edici P1 nebo P2. Přiřazení na základě skupin se podporuje jenom pro skupiny zabezpečení. Vnořené členství ve skupinách a skupiny Office 365 se aktuálně nepodporují. Další licenční požadavky na funkce popsané v tomto článku najdete na [stránce s cenami Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory). 

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

## <a name="assign-users-or-groups-to-an-app-via-the-azure-portal"></a>Přiřazení uživatelů nebo skupin k aplikaci prostřednictvím Azure Portal

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí účtu globálního správce, správce aplikace nebo účtu správce cloudové aplikace nebo jako přiřazeného vlastníka podnikové aplikace.
2. Vyberte **Azure Active Directory**. V navigační nabídce vlevo vyberte **podnikové aplikace**.
3. Vyberte aplikaci ze seznamu. Pokud aplikaci nevidíte, začněte psát její název do vyhledávacího pole. Nebo použijte ovládací prvky filtru k výběru typu aplikace, stavu nebo viditelnosti a pak vyberte **použít**.
4. V navigační nabídce vlevo vyberte **Uživatelé a skupiny**.
   > [!NOTE]
   > Pokud chcete přiřadit uživatele k aplikacím Microsoftu, jako jsou aplikace Office 365, některé z těchto aplikací používají PowerShell. 
5. Klikněte na tlačítko **Přidat uživatele** .
6. V podokně **Přidat přiřazení** vyberte **Uživatelé a skupiny**.
7. Vyberte uživatele nebo skupinu, které chcete aplikaci přiřadit, nebo zadejte jméno uživatele nebo skupiny do vyhledávacího pole. Můžete zvolit více uživatelů a skupin a vaše výběry se zobrazí v části **vybrané položky**.
8. Po dokončení klikněte na **Vybrat**.

   ![Přiřazení uživatele nebo skupiny k aplikaci](./media/assign-user-or-group-access-portal/assign-users.png)

9. V podokně **Uživatelé a skupiny** vyberte jednoho nebo více uživatelů nebo skupin ze seznamu a pak klikněte na tlačítko **Vybrat** v dolní části podokna.
10. Pokud je aplikace podporuje, můžete uživateli nebo skupině přiřadit roli. V podokně **Přidat přiřazení** zvolte **Vybrat roli**. Pak v podokně **Vybrat roli** zvolte roli, kterou chcete použít pro vybrané uživatele nebo skupiny, a potom v dolní části podokna vyberte **OK** . 

    > [!NOTE]
    > Pokud aplikace nepodporuje výběr rolí, přiřadí se výchozí role přístupu. V takovém případě aplikace spravuje úroveň přístupu uživatelů.

2. V podokně **Přidat přiřazení** vyberte v dolní části podokna tlačítko **přiřadit** .

## <a name="assign-users-or-groups-to-an-app-via-powershell"></a>Přiřazení uživatelů nebo skupin k aplikaci prostřednictvím PowerShellu

1. Otevřete příkazový řádek Windows PowerShellu se zvýšenými oprávněními.

   > [!NOTE]
   > Je potřeba nainstalovat modul AzureAD (použijte příkaz `Install-Module -Name AzureAD` ). Pokud se zobrazí výzva k instalaci modulu NuGet nebo nového modulu Azure Active Directory v2 PowerShell, zadejte Y a stiskněte ENTER.

1. Spusťte `Connect-AzureAD` a přihlaste se pomocí uživatelského účtu globálního správce.
1. K přiřazení uživatele a role k aplikaci použijte následující skript:

    ```powershell
    # Assign the values to the variables
    $username = "<You user's UPN>"
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

1. V tomto příkladu nevím, jaký je přesný název aplikační role, kterou chceme přiřadit k Britta Simon. Spusťte následující příkazy, abyste získali uživatele ($user) a instanční objekt ($sp) pomocí hlavního názvu uživatele (UPN) a zobrazovaného názvu objektu služby.

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    ```

1. Spusťte příkaz `$sp.AppRoles` pro zobrazení rolí dostupných pro aplikaci pracoviště Analytics. V tomto příkladu chceme přiřadit Britta Simon role analytik (s omezeným přístupem).

   ![Zobrazuje role dostupné uživateli pomocí role Analytics na pracovišti.](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)

1. Přiřaďte k proměnné název role `$app_role_name` .

    ```powershell
    # Assign the values to the variables
    $app_role_name = "Analyst (Limited access)"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    ```

1. Spusťte následující příkaz, který uživateli přiřadí roli aplikace:

    ```powershell
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
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
