---
title: Přiřadit uživatele nebo skupiny k podnikové aplikace v Azure Active Directory | Dokumentace Microsoftu
description: Jak vybrat podnikové aplikace přiřadit uživatele nebo skupiny k němu ve službě Azure Active Directory
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: mimart
ms.reviewer: luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 249dfeeb231c61b05af2e89f0dc02822cc18e627
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67702172"
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>Přiřadit uživatele nebo skupiny k podnikové aplikace v Azure Active Directory

Přiřadit uživatele nebo skupinu pro podnikové aplikace, musí mít příslušná oprávnění ke správě podnikové aplikace a musíte být globální správce adresáře. For Applications Microsoftu (jako jsou například aplikace Office 365) přiřazení uživatelů k podnikové aplikace pomocí Powershellu.

> [!NOTE]
> Licenční požadavky pro funkce popsané v tomto článku najdete v článku [Azure Active Directory, na stránce s cenami](https://azure.microsoft.com/pricing/details/active-directory).

## <a name="assign-a-user-to-an-app---portal"></a>Přiřazení uživatele k aplikaci – portál

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com) prostřednictvím účtu, který má k adresáři oprávnění globálního správce.
1. Vyberte **všechny služby**, v textovém poli zadejte Azure Active Directory a pak vyberte **Enter**.
1. Vyberte **podnikové aplikace**.
1. Na **podnikové aplikace – všechny aplikace** podokně se zobrazí seznam aplikací můžete spravovat. Vyberte aplikaci.
1. Na ***appname*** podokno (to znamená, s názvem vybranou aplikaci v názvu), vyberte **uživatelů a skupin**.
1. Na ***appname*** **– skupiny uživatelů a** vyberte **přidat uživatele**.
1. Na **přidat přiřazení** vyberte **uživatelů a skupin**.

   ![Přiřadit uživatele nebo skupiny k aplikaci](./media/assign-user-or-group-access-portal/assign-users.png)

1. Na **uživatelů a skupin** podokně, vyberte jeden nebo více uživatelů nebo skupin ze seznamu a klikněte na tlačítko **vyberte** tlačítko v dolní části podokna.
1. Na **přidat přiřazení** vyberte **Role**. Potom na **vybrat roli** podokně, vyberte roli, které chcete použít pro vybrané uživatele nebo skupiny, vyberte **OK** v dolní části podokna.
1. Na **přidat přiřazení** podokně, vyberte **přiřadit** tlačítko v dolní části podokna. Přiřazení uživatelé nebo skupiny mají oprávnění určené vybranou roli pro tuto aplikaci enterprise.

## <a name="allow-all-users-to-access-an-app---portal"></a>Povolit všem uživatelům přístup k aplikaci – portál

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com) prostřednictvím účtu, který má k adresáři oprávnění globálního správce.
1. Vyberte **všechny služby**, v textovém poli zadejte Azure Active Directory a pak vyberte **Enter**.
1. Vyberte **podnikové aplikace**.
1. Na **podnikové aplikace** vyberte **všechny aplikace**. Tady se uvádí aplikace, které můžete spravovat.
1. Na **podnikové aplikace – všechny aplikace** podokně, vyberte aplikaci.
1. Na ***appname*** vyberte **vlastnosti**.
1. Na  ***appname* – vlastnosti** podokno, nastavte **přiřazení uživatelů povinné?** nastavení **ne**.

**Přiřazení uživatelů povinné?** možnost:

- Nemá vliv, zda se zobrazí aplikace na přístupovém panelu aplikací. Zobrazit aplikace na přístupovém panelu, musíte přiřadit odpovídajícího uživatele nebo skupiny k aplikaci.
- Funkce pouze s cloudovými aplikacemi, které jsou nakonfigurované pro jednotné přihlašování SAML a místní aplikace, konfigurované pomocí Proxy aplikace. Zobrazit [jednotného přihlašování pro aplikace](what-is-single-sign-on.md).
- Vyžaduje, aby uživatelé udělit souhlas k aplikaci. Správce může udělit souhlas pro všechny uživatele.  Zobrazit [konfigurovat koncovým uživatelům způsob, jak vyjádřit souhlas aplikace](configure-user-consent.md).

## <a name="assign-a-user-to-an-app---powershell"></a>Přiřazení uživatele k aplikaci – PowerShell

1. Otevřete příkazový řádek se zvýšenými oprávněními prostředí Windows PowerShell.

   > [!NOTE]
   > Je potřeba nainstalovat modul Azure AD (použijte příkaz `Install-Module -Name AzureAD`). Pokud se zobrazí výzva k instalaci modulu NuGet nebo nový modul Powershellu pro Azure Active Directory V2, zadejte Y a stiskněte klávesu ENTER.

1. Spustit `Connect-AzureAD` a přihlaste se pomocí uživatelského účtu globálního správce.
1. Pomocí následujícího skriptu uživatele a roli přiřadit aplikace:

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

### <a name="example"></a>Příklad

Tento příklad přiřadí uživateli Britta Simon k [Microsoft Workplace Analytics](https://products.office.com/business/workplace-analytics) aplikace pomocí Powershellu.

1. V prostředí PowerShell přiřadíte hodnoty, které odpovídají proměnné $username $app_name a $app_role_name.

    ```powershell
    # Assign the values to the variables
    $username = "britta.simon@contoso.com"
    $app_name = "Workplace Analytics"
    ```

1. V tomto příkladu nevíme, co je přesný název role aplikace, kterou chcete přiřadit Britta Simon. Spuštěním následujících příkazů zobrazíte uživatele ($user) a instanční objekt služby ($sp) pomocí uživatele (UPN) a instanční objekt služby zobrazované názvy.

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    ```

1. Spusťte příkaz `$sp.AppRoles` zobrazíte dostupné pro aplikaci Workplace Analytics role. V tomto příkladu chceme Britta Simon přiřadit roli analytik (omezený přístup).

   ![Zobrazuje dostupné role uživatele s využitím Role Analytics pracoviště](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)

1. Přiřazení názvu role `$app_role_name` proměnné.

    ```powershell
    # Assign the values to the variables
    $app_role_name = "Analyst (Limited access)"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    ```

1. Spuštěním následujícího příkazu přiřaďte uživatele k roli aplikace:

    ```powershell
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

## <a name="next-steps"></a>Další postup

- [Zobrazit všechny moje skupiny](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Odebrání uživatele nebo skupiny přiřazení podnikové aplikace](remove-user-or-group-access-portal.md)
- [Zakázat přihlášení uživatele pro podnikové aplikace](disable-user-sign-in-portal.md)
- [Změnit název nebo loga podnikové aplikace](change-name-or-logo-portal.md)
