---
title: Přiřadit uživatele nebo skupiny k podnikové aplikace v Azure Active Directory | Dokumentace Microsoftu
description: Jak vybrat podnikové aplikace přiřadit uživatele nebo skupiny k němu ve službě Azure Active Directory
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
ms.topic: conceptual
ms.date: 11/05/2018
ms.author: barbkess
ms.reviewer: luleon
ms.openlocfilehash: ee0b14123e193f219e403d2608368c27f953013d
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2018
ms.locfileid: "51037970"
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>Přiřadit uživatele nebo skupiny k podnikové aplikace v Azure Active Directory
Přiřadit uživatele nebo skupinu pro podnikové aplikace, musí mít příslušná oprávnění ke správě podnikové aplikace a musíte být globální správce adresáře.

> [!NOTE]
> Licenční požadavky pro funkce popsané v tomto článku najdete v článku [Azure Active Directory, na stránce s cenami](https://azure.microsoft.com/pricing/details/active-directory).

> [!NOTE]
> For Applications Microsoftu (jako jsou například aplikace Office 365) přiřazení uživatelů k podnikové aplikace pomocí Powershellu.


## <a name="how-do-i-assign-user-access-to-an-enterprise-app-in-the-azure-portal"></a>Jak přiřadit přístup uživatelů k podnikové aplikace na webu Azure Portal?
1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com) prostřednictvím účtu, který má k adresáři oprávnění globálního správce.
2. Vyberte **všechny služby**, v textovém poli zadejte Azure Active Directory a pak vyberte **Enter**.
3. Vyberte **podnikové aplikace**.

    ![Otevření podnikové aplikace](./media/assign-user-or-group-access-portal/open-enterprise-apps.png)
4. Na **podnikové aplikace** okně vyberte **všechny aplikace**. Tady se uvádí aplikace, které můžete spravovat.
5. Na **podnikové aplikace – všechny aplikace** okno, vyberte aplikaci.
6. Na ***appname*** blade (tedy okno s názvem vybranou aplikaci v názvu), vyberte **uživatelů a skupin**.

    ![Výběrem příkazu všechny aplikace](./media/assign-user-or-group-access-portal/select-app-users.png)
7. Na ***appname*** **– uživatele a skupiny přiřazení** okno, vyberte **přidat** příkazu.
8. Na **přidat přiřazení** okně vyberte **uživatelů a skupin**.

    ![Přiřadit uživatele nebo skupiny k aplikaci](./media/assign-user-or-group-access-portal/assign-users.png)
9. Na **uživatelů a skupin** okno, vyberte jeden nebo více uživatelů nebo skupin ze seznamu a pak vyberte **vyberte** tlačítko v dolní části okna.
10. Na **přidat přiřazení** okně vyberte **Role**. Potom na **vybrat roli** okno, vyberte roli použít na vybrané uživatele nebo skupiny, a potom vyberte **OK** tlačítko v dolní části okna.
11. Na **přidat přiřazení** okno, vyberte **přiřadit** tlačítko v dolní části okna. Přiřazení uživatelé nebo skupiny mají oprávnění určené vybranou roli pro tuto aplikaci enterprise.

## <a name="how-do-i-assign-a-user-to-an-enterprise-app-using-powershell"></a>Jak přiřadit uživatele k podnikové aplikace pomocí Powershellu?

1. Otevřete příkazový řádek se zvýšenými oprávněními prostředí Windows PowerShell.

    >[!NOTE] 
    > Je potřeba nainstalovat modul Azure AD (použijte příkaz `Install-Module -Name AzureAD`). Pokud se zobrazí výzva k instalaci modulu NuGet nebo nový modul Powershellu pro Azure Active Directory V2, zadejte Y a stiskněte klávesu ENTER.

2. Spustit `Connect-AzureAD` a přihlaste se pomocí uživatelského účtu globálního správce.
3. Pomocí následujícího skriptu uživatele a roli přiřadit aplikace:

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

Další informace o tom, jak přiřadit uživatele k roli aplikace najdete v dokumentaci k [AzureADUserAppRoleAssignment nový](https://docs.microsoft.com/powershell/module/azuread/new-azureaduserapproleassignment?view=azureadps-2.0)

Pro přiřazení skupiny pro podnikové aplikace, je třeba nahradit `Get-AzureADUser` s `Get-AzureADGroup`.

### <a name="example"></a>Příklad:

Tento příklad přiřadí uživateli Britta Simon k [Microsoft Workplace Analytics](https://products.office.com/business/workplace-analytics) aplikace pomocí Powershellu.

1. V prostředí PowerShell přiřadíte hodnoty, které odpovídají proměnné $username $app_name a $app_role_name. 

    ```powershell
    # Assign the values to the variables
    $username = "britta.simon@contoso.com"
    $app_name = "Workplace Analytics"
    ```

2. V tomto příkladu nevíme, co je přesný název role aplikace, kterou chcete přiřadit Britta Simon. Spuštěním následujících příkazů zobrazíte uživatele ($user) a instanční objekt služby ($sp) pomocí uživatele (UPN) a instanční objekt služby zobrazované názvy.

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    ```
        
3. Spusťte příkaz `$sp.AppRoles` zobrazíte dostupné pro aplikaci Workplace Analytics role. V tomto příkladu chceme Britta Simon přiřadit roli analytik (omezený přístup).
    
    ![Role Analytics pracoviště](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)

4. Přiřazení názvu role `$app_role_name` proměnné.
        
    ```powershell
    # Assign the values to the variables
    $app_role_name = "Analyst (Limited access)"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    ```

5. Spuštěním následujícího příkazu přiřaďte uživatele k roli aplikace:

    ```powershell
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

## <a name="next-steps"></a>Další postup
* [Zobrazit všechny moje skupiny](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Odebrání uživatele nebo skupiny přiřazení podnikové aplikace](remove-user-or-group-access-portal.md)
* [Zakázat přihlášení uživatele pro podnikové aplikace](disable-user-sign-in-portal.md)
* [Změnit název nebo loga podnikové aplikace](change-name-or-logo-portal.md)
