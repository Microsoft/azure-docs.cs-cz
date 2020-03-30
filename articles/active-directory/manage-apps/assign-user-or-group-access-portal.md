---
title: Přiřazení uživatele nebo skupiny k podnikové aplikaci ve službě Azure AD
description: Jak vybrat podnikovou aplikaci pro přiřazení uživatele nebo skupiny ve službě Azure Active Directory
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: mimart
ms.reviewer: luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 186e36e4625a60362c54972b16b53f0f3e6753fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409188"
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>Přiřazení uživatele nebo skupiny k podnikové aplikaci ve službě Azure Active Directory

Tento článek ukazuje, jak přiřadit uživatele nebo skupiny k podnikovým aplikacím ve službě Azure Active Directory (Azure AD), buď z webu Azure Portal, nebo pomocí PowerShellu. Když přiřadíte uživatele k aplikaci, aplikace se zobrazí na [přístupovém panelu Moje aplikace](https://myapps.microsoft.com/) pro snadný přístup. Pokud aplikace zpřístupňuje role, můžete také přiřadit konkrétní roli uživateli.

Pro větší kontrolu lze určité typy podnikových aplikací nakonfigurovat tak, aby [vyžadovaly přiřazení uživatele](#configure-an-application-to-require-user-assignment). 

Pokud chcete [k podnikové aplikaci přiřadit uživatele nebo skupinu](#assign-users-or-groups-to-an-app-via-the-azure-portal), musíte se přihlásit jako globální správce, správce aplikací, správce cloudových aplikací nebo přiřazený vlastník podnikové aplikace.

> [!NOTE]
> Přiřazení na základě skupiny vyžaduje edici Azure Active Directory Premium P1 nebo P2. Přiřazení založené na skupině je podporováno pouze pro skupiny zabezpečení. Vnořená členství ve skupinách a skupiny Office 365 momentálně nejsou podporované. Další požadavky na licencování funkcí popsaných v tomto článku najdete na [stránce s cenami služby Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory). 

## <a name="configure-an-application-to-require-user-assignment"></a>Konfigurace aplikace tak, aby vyžadovala přiřazení uživatele

S následujícími typy aplikací máte možnost vyžadovat, aby uživatelé byli přiřazeni k aplikaci před tím, než k ní budou mít přístup:

- Aplikace nakonfigurované pro federované jednotné přihlašování (SSO) s ověřováním na základě SAML
- Aplikace proxy aplikací, které používají předběžné ověřování služby Azure Active Directory
- Aplikace postavené na platformě aplikace Azure AD, které používají Ověřování OAuth 2.0 / OpenID Connect poté, co uživatel nebo správce s tímto uživatelem souhlasil.

Pokud je vyžadováno přiřazení uživatele, budou se moci přihlásit pouze uživatelé, které explicitně přiřadíte k aplikaci. Přístup k aplikaci mohou přistupovat na stránce Moje aplikace nebo pomocí přímého odkazu. 

Pokud přiřazení *není vyžadováno*, protože jste nastavili tuto možnost na **ne,** nebo proto, že aplikace používá jiný režim přihlašování, bude mít každý uživatel přístup k aplikaci, pokud má přímý odkaz na aplikaci nebo **adresu URL přístupu uživatele** na stránce **Vlastnosti** aplikace. 

Toto nastavení nemá vliv na to, zda se aplikace zobrazí na přístupovém panelu Moje aplikace. Aplikace se zobrazí na panelech Přístup ke aplikacím Moje aplikace, jakmile k aplikaci přiřadíte uživatele nebo skupinu. Informace o pozadí najdete [v tématu Správa přístupu k aplikacím](what-is-access-management.md).


Vyžadování přiřazení uživatele pro aplikaci:

1. Přihlaste se k [portálu Azure](https://portal.azure.com) pomocí účtu správce nebo jako vlastník aplikace.

2. Vyberte **Azure Active Directory**. V levé navigační nabídce vyberte **možnost Podnikové aplikace**.

3. Vyberte aplikaci ze seznamu. Pokud aplikaci nevidíte, začněte do vyhledávacího pole psát její název. Nebo pomocí ovládacích prvků filtru vyberte typ, stav nebo viditelnost aplikace a pak vyberte **Použít**.

4. V levé navigační nabídce vyberte **Vlastnosti**.

5. Ujistěte se, že **je vyžadováno přiřazení uživatele?** **Yes**

   > [!NOTE]
   > Pokud není k dispozici **přepínač Přiřazení uživatele?**

6. V horní části obrazovky vyberte tlačítko **Uložit.**

## <a name="assign-users-or-groups-to-an-app-via-the-azure-portal"></a>Přiřazení uživatelů nebo skupin k aplikaci prostřednictvím portálu Azure

1. Přihlaste se k [portálu Azure](https://portal.azure.com) pomocí globálního správce, správce aplikací nebo účtu správce cloudových aplikací nebo jako přiřazený vlastník podnikové aplikace.
2. Vyberte **Azure Active Directory**. V levé navigační nabídce vyberte **možnost Podnikové aplikace**.
3. Vyberte aplikaci ze seznamu. Pokud aplikaci nevidíte, začněte do vyhledávacího pole psát její název. Nebo pomocí ovládacích prvků filtru vyberte typ, stav nebo viditelnost aplikace a pak vyberte **Použít**.
4. V levé navigační nabídce vyberte **Možnost Uživatelé a skupiny**.
   > [!NOTE]
   > Pokud chcete přiřadit uživatele k aplikacím Microsoft, jako jsou aplikace Office 365, některé z těchto aplikací používají PowerShell. 
5. Vyberte tlačítko **Přidat uživatele.**
6. V podokně **Přidat přiřazení** vyberte **Možnost Uživatelé a skupiny**.
7. Vyberte uživatele nebo skupinu, kterou chcete aplikaci přiřadit, nebo začněte do vyhledávacího pole zadávat jméno uživatele nebo skupiny. Můžete vybrat více uživatelů a skupin a výběry se zobrazí v části **Vybrané položky**.
8. Po dokončení klepněte na **tlačítko Vybrat**.

   ![Přiřazení uživatele nebo skupiny k aplikaci](./media/assign-user-or-group-access-portal/assign-users.png)

9. V podokně **Uživatelé a skupiny** vyberte ze seznamu jednoho nebo více uživatelů nebo skupin a v dolní části podokna zvolte tlačítko **Vybrat.**
10. Pokud to aplikace podporuje, můžete přiřadit roli uživateli nebo skupině. V podokně **Přidat přiřazení** zvolte **Vybrat roli**. Potom v podokně **Vybrat roli** zvolte roli, která se má použít pro vybrané uživatele nebo skupiny, a v dolní části podokna vyberte **OK.** 

    > [!NOTE]
    > Pokud aplikace nepodporuje výběr rolí, je přiřazena výchozí přístupová role. V tomto případě aplikace spravuje úroveň přístupu, který mají uživatelé.

2. V podokně **Přidat přiřazení** vyberte tlačítko **Přiřadit** v dolní části podokna.

## <a name="assign-users-or-groups-to-an-app-via-powershell"></a>Přiřazení uživatelů nebo skupin k aplikaci přes PowerShell

1. Otevřete příkazový řádek prostředí Windows PowerShell se zvýšenými oprávněními.

   > [!NOTE]
   > Musíte nainstalovat modul AzureAD (použijte `Install-Module -Name AzureAD`příkaz ). Pokud se zobrazí výzva k instalaci modulu NuGet nebo nového modulu PowerShell azure active directory V2, zadejte Y a stiskněte klávesu ENTER.

1. Spouštět `Connect-AzureAD` a přihlašovat se pomocí uživatelského účtu globálního správce.
1. Pomocí následujícího skriptu přiřaďte uživateli a roli k aplikaci:

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

Další informace o tom, jak přiřadit uživatele k roli aplikace, naleznete v dokumentaci k [new-AzureADUserAppRoleAssignment](https://docs.microsoft.com/powershell/module/azuread/new-azureaduserapproleassignment?view=azureadps-2.0)

Chcete-li přiřadit skupinu k podnikové `Get-AzureADUser` aplikaci, je třeba nahradit . `Get-AzureADGroup`

### <a name="example"></a>Příklad

Tento příklad přiřadí uživatele Britta Simon k aplikaci [Microsoft Workplace Analytics](https://products.office.com/business/workplace-analytics) pomocí prostředí PowerShell.

1. V Prostředí PowerShell přiřaďte odpovídající hodnoty proměnným $username, $app_name a $app_role_name.

    ```powershell
    # Assign the values to the variables
    $username = "britta.simon@contoso.com"
    $app_name = "Workplace Analytics"
    ```

1. V tomto příkladu nevíme, jaký je přesný název role aplikace, kterou chceme přiřadit Brittě Simonové. Spusťte následující příkazy, abyste získali uživatele ($user) a instanční objekt ($sp) pomocí hlavního uživatelského názvy uživatelů a hlavního uživatele.

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    ```

1. Spuštěním `$sp.AppRoles` příkazu zobrazíte role dostupné pro aplikaci Workplace Analytics. V tomto příkladu chceme přiřadit Britta Simon analytik (omezený přístup) role.

   ![Zobrazuje role, které má uživatel k dispozici pomocí role Analýzy na workplace.](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)

1. Přiřaďte `$app_role_name` proměnné název role.

    ```powershell
    # Assign the values to the variables
    $app_role_name = "Analyst (Limited access)"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    ```

1. Chcete-li přiřadit uživatele k roli aplikace, spusťte následující příkaz:

    ```powershell
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

## <a name="related-articles"></a>Související články

- [Další informace o přístupu koncových uživatelů k aplikacím](end-user-experiences.md)
- [Plánování nasazení přístupového panelu Azure AD](access-panel-deployment-plan.md)
- [Správa přístupu k aplikacím](what-is-access-management.md)
 
## <a name="next-steps"></a>Další kroky

- [Zobrazit všechny mé skupiny](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Odebrání přiřazení uživatele nebo skupiny z podnikové aplikace](remove-user-or-group-access-portal.md)
- [Zakázání přihlášení uživatelů k podnikové aplikaci](disable-user-sign-in-portal.md)
- [Změna názvu nebo loga podnikové aplikace](change-name-or-logo-portal.md)
