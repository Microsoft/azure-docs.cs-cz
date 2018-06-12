---
title: Přiřadit uživatele nebo skupinu enterprise aplikace v Azure Active Directory | Microsoft Docs
description: Jak vybrat aplikaci enterprise přiřadit uživatele nebo skupinu k němu v Azure Active Directory
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2018
ms.author: barbkess
ms.reviewer: luleon
ms.openlocfilehash: 487312cb79c5c278849668a472acfda7823e4bc1
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35303285"
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>Přiřadit uživatele nebo skupinu enterprise aplikace v Azure Active Directory
Přiřadit uživatele nebo skupiny do podnikové aplikace, musí mít příslušná oprávnění ke správě firemní aplikace a musí být globální správce adresáře.

> [!NOTE]
> Funkce popsané v tomto článku vyžadují licenci Azure Active Directory Premium P1 nebo Premium P2. Další informace najdete v tématu [Azure Active Directory, na stránce s cenami](https://azure.microsoft.com/pricing/details/active-directory).

> [!NOTE]
> Pro Microsoft Applications (například aplikace Office 365) pomocí prostředí PowerShell přiřazovat uživatele k podnikové aplikace.


## <a name="how-do-i-assign-user-access-to-an-enterprise-app-in-the-azure-portal"></a>Přiřazení přístupu uživatelů k aplikaci podnikové na portálu Azure
1. Přihlaste se k [portál Azure](https://portal.azure.com) pomocí účtu, který je globální správce adresáře.
2. Vyberte **všechny služby**, zadejte do textového pole Azure Active Directory a potom vyberte **Enter**.
3. Na **Azure Active Directory – *directoryname***  okno (to znamená, Azure AD okna pro adresář spravujete), vyberte **podnikové aplikace, které**.

    ![Otevírání podnikové aplikace](./media/assign-user-or-group-access-portal/open-enterprise-apps.png)
4. Na **podnikové aplikace, které** vyberte **všechny aplikace**. Tento seznam aplikací, které můžete spravovat.
5. Na **podnikové aplikace – všechny aplikace** okně, vyberte aplikaci.
6. Na ***appname*** okno (to znamená, v okně s názvem vybranou aplikaci v názvu), vyberte **uživatelé a skupiny**.

    ![Výběr příkaz všechny aplikace](./media/assign-user-or-group-access-portal/select-app-users.png)
7. Na ***appname*** **-uživatele & přiřazení skupiny** okně, vyberte **přidat** příkaz.
8. Na **přidat přiřazení** vyberte **uživatelů a skupin**.

    ![Přiřazení uživatele nebo skupiny do aplikace](./media/assign-user-or-group-access-portal/assign-users.png)
9. Na **uživatelů a skupin** okně vyberte jeden nebo více uživatelů nebo skupin ze seznamu a pak vyberte **vyberte** tlačítko v dolní části okna.
10. Na **přidat přiřazení** vyberte **Role**. Potom na **vybrat roli** okně, vyberte roli, které chcete použít na vybrané uživatele nebo skupiny a pak vyberte **OK** tlačítko v dolní části okna.
11. Na **přidat přiřazení** okně, vyberte **přiřadit** tlačítko v dolní části okna. Přiřazené uživatele nebo skupiny, nemá oprávnění definovaná podle vybrané role pro tuto aplikaci enterprise.

## <a name="how-do-i-assign-a-user-to-an-enterprise-app-using-powershell"></a>Přiřazení uživatele k podnikové aplikace pomocí prostředí PowerShell

1. Otevřete příkazový řádek se zvýšenými oprávněními prostředí Windows PowerShell.

    >[!NOTE] 
    > Potřebujete nainstalovat modul AzureAD (použijte příkaz `Install-Module -Name AzureAD`). Pokud se zobrazí výzva k instalaci modulu NuGet nebo nového modulu Azure Active Directory V2 PowerShell, zadejte Y a stiskněte klávesu ENTER.

2. Spustit `Connect-AzureAD` a přihlaste se pomocí uživatelského účtu globálního správce.
3. Pomocí následujícího skriptu přiřadit uživatele a role k aplikaci:

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

Další informace o tom, jak přiřadit uživatele k roli aplikace naleznete v dokumentaci k [New-AzureADUserAppRoleAssignment](https://docs.microsoft.com/powershell/module/azuread/new-azureaduserapproleassignment?view=azureadps-2.0)

Pokud chcete přiřadit skupinu firemní aplikace, je třeba nahradit `Get-AzureADUser` s `Get-AzureADGroup`.

### <a name="example"></a>Příklad:

Tento příklad přiřadí uživatele Britta Simon k [Microsoft Analytics síti na pracovišti](https://products.office.com/business/workplace-analytics) aplikace pomocí prostředí PowerShell.

1. V prostředí PowerShell přiřaďte odpovídající hodnoty pro proměnné $username, $app_name a $app_role_name. 

    ```powershell
    # Assign the values to the variables
    $username = "britta.simon@contoso.com"
    $app_name = "Workplace Analytics"
    ```

2. V tomto příkladu nevíme, co je přesný název role aplikace, které chcete přiřadit Britta Simon. Spusťte následující příkazy a získat uživatele ($user) a objektu zabezpečení služby ($sp) pomocí uživatele (UPN) a objekt služby zobrazované názvy.

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    ```
        
3. Spusťte příkaz `$sp.AppRoles` zobrazíte rolí, které jsou k dispozici pro aplikaci Analytics síti na pracovišti. V tomto příkladu jsme chcete přiřadit Britta Simon roli analytik (omezený přístup).
    
    ![Role Analytics síti na pracovišti](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)

4. Přiřadit název role k `$app_role_name` proměnné.
        
    ```powershell
    # Assign the values to the variables
    $app_role_name = "Analyst (Limited access)"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    ```

5. Spusťte následující příkaz k přiřazení uživatele k roli aplikace:

    ```powershell
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

## <a name="next-steps"></a>Další postup
* [Zobrazit všechny moje skupin](../active-directory-groups-view-azure-portal.md)
* [Odebrat uživatele nebo skupinu přiřazení z podnikové aplikace.](remove-user-or-group-access-portal.md)
* [Zakázat přihlášení uživatele pro aplikaci, enterprise](disable-user-sign-in-portal.md)
* [Změna názvu nebo logo aplikace enterprise](change-name-or-logo-portal.md)
