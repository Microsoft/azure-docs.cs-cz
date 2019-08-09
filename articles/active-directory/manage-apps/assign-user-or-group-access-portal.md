---
title: Přiřazení uživatele nebo skupiny k podnikové aplikaci v Azure Active Directory | Microsoft Docs
description: Jak vybrat podnikovou aplikaci, do které se přiřadí uživatel nebo skupina v Azure Active Directory
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
ms.openlocfilehash: 15f7e830079c224e9e15dd45d14c1741376f8762
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2019
ms.locfileid: "68851698"
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>Přiřazení uživatele nebo skupiny k podnikové aplikaci v Azure Active Directory

Pokud chcete přiřadit uživatele nebo skupinu k podnikové aplikaci, musíte mít příslušná oprávnění ke správě podnikové aplikace a musíte být globálním správcem pro daný adresář. Pro aplikace Microsoftu (například aplikace Office 365) přiřaďte uživatele do podnikové aplikace pomocí PowerShellu.

> [!NOTE]
> Licenční požadavky na funkce popsané v tomto článku najdete na [stránce s cenami Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory).

## <a name="assign-a-user-to-an-app---portal"></a>Přiřazení uživatele k aplikaci – Portál

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com) prostřednictvím účtu, který má k adresáři oprávnění globálního správce.
1. Vyberte **všechny služby**, do textového pole zadejte Azure Active Directory a pak vyberte **ENTER**.
1. Vyberte **podnikové aplikace**.
1. V podokně **podnikové aplikace – všechny aplikace** se zobrazí seznam aplikací, které můžete spravovat. Vyberte aplikaci.
1. V podokně ***AppName*** (to znamená podokno s názvem vybrané aplikace v názvu) vyberte **Uživatelé & skupiny**.
1. V podokně ***AppName*** **– uživatel a skupiny** vyberte **Přidat uživatele**.
1. V podokně **Přidat přiřazení** vyberte **Uživatelé a skupiny**.

   ![Přiřazení uživatele nebo skupiny k aplikaci](./media/assign-user-or-group-access-portal/assign-users.png)

1. V podokně **Uživatelé a skupiny** vyberte jednoho nebo více uživatelů nebo skupin ze seznamu a pak klikněte na tlačítko **Vybrat** v dolní části podokna.
1. V podokně **Přidat přiřazení** vyberte **role**. Pak v podokně **Vybrat roli** vyberte roli, kterou chcete použít pro vybrané uživatele nebo skupiny, a potom v dolní části podokna vyberte **OK** .
1. V podokně **Přidat přiřazení** vyberte v dolní části podokna tlačítko **přiřadit** . Přiřazení uživatelé nebo skupiny mají oprávnění definovaná vybranou rolí pro tuto podnikovou aplikaci.

## <a name="allow-all-users-to-access-an-app---portal"></a>Povolení přístupu všech uživatelů k portálu pro aplikace

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com) prostřednictvím účtu, který má k adresáři oprávnění globálního správce.
1. Vyberte **všechny služby**, do textového pole zadejte Azure Active Directory a pak vyberte **ENTER**.
1. Vyberte **podnikové aplikace**.
1. V podokně **podnikové aplikace** vyberte **všechny aplikace**. Zobrazí se seznam aplikací, které můžete spravovat.
1. V podokně **podnikové aplikace – všechny aplikace** vyberte aplikaci.
1. V podokně ***AppName*** vyberte možnost **vlastnosti**.
1. V podokně  ***AppName* -Properties** nastavte **požadované přiřazení uživatele?** nastavení na **ne**.

Je **vyžadováno přiřazení uživatele?** možnost:

- Pokud je tato možnost nastavená na hodnotu Ano, uživatelé musí být nejprve přiřazeni k této aplikaci, aby k nim měli přístup.
- Pokud je tato možnost nastavená na ne, pak se udělí všem uživatelům, kteří mají přímý odkaz na adresu URL nebo URL aplikace, získají přístup.
- Nemá vliv na to, zda se aplikace zobrazuje na panelu přístupu aplikace. Chcete-li zobrazit aplikaci na přístupovém panelu, musíte aplikaci přiřadit vhodného uživatele nebo skupinu.
- Jenom aplikace s cloudovým aplikacím, které jsou nakonfigurované pro jednotné přihlašování SAML, aplikace proxy aplikací, které používají Azure Active Directory předběžné ověřování nebo aplikace sestavené přímo na platformě aplikace Azure AD používající OAuth 2,0/ OpenID připojit ověřování poté, co uživatel nebo správce souhlasí s touto aplikací. Viz [jednotné přihlašování pro aplikace](what-is-single-sign-on.md). Viz téma [Konfigurace způsobu, jakým koncoví uživatelé přisouhlasí k aplikaci](configure-user-consent.md).
- Tato možnost nemá žádný vliv, pokud je aplikace nakonfigurovaná pro jiné režimy jednotného přihlašování.

## <a name="assign-a-user-to-an-app---powershell"></a>Přiřazení uživatele k aplikaci – PowerShell

1. Otevřete příkazový řádek Windows PowerShellu se zvýšenými oprávněními.

   > [!NOTE]
   > Je potřeba nainstalovat modul AzureAD (použijte příkaz `Install-Module -Name AzureAD`). Pokud se zobrazí výzva k instalaci modulu NuGet nebo nového modulu Azure Active Directory v2 PowerShell, zadejte Y a stiskněte ENTER.

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

Další informace o tom, jak přiřadit uživatele k roli aplikace, najdete v dokumentaci pro [New-AzureADUserAppRoleAssignment](https://docs.microsoft.com/powershell/module/azuread/new-azureaduserapproleassignment?view=azureadps-2.0) .

Pokud chcete přiřadit skupinu k podnikové aplikaci, musíte ji nahradit `Get-AzureADUser`. `Get-AzureADGroup`

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

1. Přiřaďte k `$app_role_name` proměnné název role.

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

## <a name="next-steps"></a>Další postup

- [Zobrazit všechny moje skupiny](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Odebrání přiřazení uživatele nebo skupiny z podnikové aplikace](remove-user-or-group-access-portal.md)
- [Zakázání přihlášení uživatelů pro podnikovou aplikaci](disable-user-sign-in-portal.md)
- [Změna názvu nebo loga podnikové aplikace](change-name-or-logo-portal.md)
