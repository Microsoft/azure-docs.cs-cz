---
title: Správa oprávnění uživatelů a správců – Azure Active Directory | Microsoft Docs
description: Přečtěte si, jak kontrolovat a spravovat oprávnění pro aplikaci v Azure AD. Odvolejte například všechna oprávnění udělená aplikaci.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 7/10/2020
ms.author: mimart
ms.reviewer: luleonpla
ms.collection: M365-identity-device-management
ms.openlocfilehash: da1284af82c0779ca57b49a0c4aef4b492a3bf20
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2021
ms.locfileid: "99054598"
---
# <a name="take-action-on-overprivileged-or-suspicious-applications-in-azure-active-directory"></a>Provedení akce na privilegovaných nebo podezřelých aplikacích v Azure Active Directory

Přečtěte si, jak kontrolovat a spravovat oprávnění aplikace. Tento článek poskytuje různé akce, které můžete provést k zabezpečení vaší aplikace podle scénáře. Tyto akce se vztahují na všechny aplikace, které byly přidány do tenanta Azure Active Directory (Azure AD) prostřednictvím souhlasu uživatele nebo správce.

Další informace o tom, jak se předávají aplikacím, najdete v tématu [Azure Active Directory souhlasu Framework](../develop/consent-framework.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li provést následující akce, musíte se přihlásit jako globální správce, správce aplikace nebo správce cloudové aplikace.

Chcete-li omezit přístup k aplikacím, je nutné vyžadovat přiřazení uživatele a pak přiřadit uživatele nebo skupiny k aplikaci.  Další informace najdete v tématu [metody přiřazení uživatelů a skupin](./assign-user-or-group-access-portal.md).

K portálu Azure AD můžete přistupovat a získat tak kontextové skripty PowerShellu k provedení těchto akcí.
 
1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako globální správce, správce aplikace nebo správce cloudové aplikace.
2. Vyberte **Azure Active Directory**  >  **podnikových aplikací**.
3. Vyberte aplikaci, ke které chcete omezit přístup.
4. Vyberte **Oprávnění**. Na panelu příkazů vyberte **zkontrolovat oprávnění**.

![Snímek obrazovky okna zkontrolovat oprávnění](./media/manage-application-permissions/review-permissions.png)


## <a name="control-access-to-an-application"></a>Řízení přístupu k aplikaci

Doporučujeme, abyste omezili přístup k aplikaci tak, že zapnete nastavení **přiřazení uživatelů** .

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako globální správce, správce aplikace nebo správce cloudové aplikace.
2. Vyberte **Azure Active Directory**  >  **podnikových aplikací**.
3. Vyberte aplikaci, ke které chcete omezit přístup.
4. Vyberte **vlastnosti** a pak nastavte **požadavky uživatele** na **Ano**.
5. Vyberte **uživatele a skupiny** a potom odeberte nechtěné uživatele, kteří jsou přiřazeni k aplikaci.
6. Přiřaďte aplikaci uživatelům nebo skupinám.

Volitelně můžete odebrat všechny uživatele, kteří jsou přiřazeni k aplikaci pomocí prostředí PowerShell.

## <a name="revoke-all-permissions-for-an-application"></a>Odvolat všechna oprávnění pro aplikaci

Použití skriptu PowerShellu odvolá všechna oprávnění udělená této aplikaci.

> [!NOTE]
> Odvolání aktuálně uděleného oprávnění neukončí uživatelům opětovné odeslání do aplikace. Pokud chcete uživatele zablokovat, přečtěte si téma [Konfigurace souhlasu uživatelů s aplikacemi](configure-user-consent.md).

Volitelně můžete aplikaci zakázat, aby uživatelé měli přístup k aplikaci a aby aplikace měla přístup k vašim datům.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako globální správce, správce aplikace nebo správce cloudové aplikace.
2. Vyberte **Azure Active Directory**  >  **podnikových aplikací**.
3. Vyberte aplikaci, ke které chcete omezit přístup.
4. Vyberte **vlastnosti** a pak nastavte možnost **povoleno pro uživatele, aby se přihlásili?** na **ne**.

## <a name="investigate-a-suspicious-application"></a>Prozkoumat podezřelou aplikaci

Doporučujeme, abyste omezili přístup k aplikaci tak, že zapnete nastavení **přiřazení uživatelů** . Pak zkontrolujte oprávnění, která uživatelé a správci udělili aplikaci.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako globální správce, správce aplikace nebo správce cloudové aplikace.
3. Vyberte **Azure Active Directory**  >  **podnikových aplikací**.
5. Vyberte aplikaci, ke které chcete omezit přístup.
6. Vyberte **vlastnosti** a pak nastavte **požadavky uživatele** na **Ano**.
7. Vyberte **oprávnění** a zkontrolujte oprávnění správce a uživatel se souhlasem.

Volitelně můžete pomocí prostředí PowerShell:

- Odeberte všechny přiřazené uživatele, abyste je zazastavili v přihlášení k aplikaci.
- Zruší platnost tokenů aktualizace pro uživatele, kteří mají přístup k aplikaci.
- Odvolat všechna oprávnění pro aplikaci.

Nebo můžete zakázat aplikaci pro blokování přístupu uživatelů a zastavení přístupu aplikace k vašim datům.


## <a name="disable-a-malicious-application"></a>Zakázat škodlivou aplikaci 

Doporučujeme, abyste aplikaci zakázali, aby blokovala přístup uživatelů a aby aplikace měla přístup k vašim datům. Pokud místo toho odstraníte aplikaci, můžou si uživatelé aplikaci znovu vyjádřit a udělit jim přístup k vašim datům.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako globální správce, správce aplikace nebo správce cloudové aplikace.
2. Vyberte **Azure Active Directory**  >  **podnikových aplikací**.
3. Vyberte aplikaci, ke které chcete omezit přístup.
4. Vyberte **vlastnosti** a pak zkopírujte ID objektu.

### <a name="powershell-commands"></a>Příkazy prostředí PowerShell


Načte ID instančního objektu služby.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako globální správce, správce aplikace nebo správce cloudové aplikace.
2. Vyberte **Azure Active Directory**  >  **podnikových aplikací**.
3. Vyberte aplikaci, ke které chcete omezit přístup.
4. Vyberte **vlastnosti** a pak zkopírujte ID objektu.

```powershell
$sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
$sp.ObjectId
```
Odeberte všechny uživatele, kteří jsou přiřazeni k aplikaci.
 ```powershell
Connect-AzureAD

# Get Service Principal using objectId
$sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

# Get Azure AD App role assignments using objectId of the Service Principal
$assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -All $true

# Remove all users and groups assigned to the application
$assignments | ForEach-Object {
    if ($_.PrincipalType -eq "User") {
        Remove-AzureADUserAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.ObjectId
    } elseif ($_.PrincipalType -eq "Group") {
        Remove-AzureADGroupAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.ObjectId
    }
}
 ```

Odvolat oprávnění udělená aplikaci.

```powershell
Connect-AzureAD

# Get Service Principal using objectId
$sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

# Get all delegated permissions for the service principal
$spOAuth2PermissionsGrants = Get-AzureADOAuth2PermissionGrant -All $true| Where-Object { $_.clientId -eq $sp.ObjectId }

# Remove all delegated permissions
$spOAuth2PermissionsGrants | ForEach-Object {
    Remove-AzureADOAuth2PermissionGrant -ObjectId $_.ObjectId
}

# Get all application permissions for the service principal
$spApplicationPermissions = Get-AzureADServiceAppRoleAssignedTo -ObjectId $sp.ObjectId -All $true | Where-Object { $_.PrincipalType -eq "ServicePrincipal" }

# Remove all delegated permissions
$spApplicationPermissions | ForEach-Object {
    Remove-AzureADServiceAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.objectId
}
```
Zruší platnost obnovovacích tokenů.
```powershell
Connect-AzureAD

# Get Service Principal using objectId
$sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

# Get Azure AD App role assignments using objectID of the Service Principal
$assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -All $true | Where-Object {$_.PrincipalType -eq "User"}

# Revoke refresh token for all users assigned to the application
$assignments | ForEach-Object {
    Revoke-AzureADUserAllRefreshToken -ObjectId $_.PrincipalId
}
```
## <a name="next-steps"></a>Další kroky
- [Správa souhlasu s aplikacemi a vyhodnocení žádosti o souhlas](manage-consent-requests.md)
- [Konfigurace souhlasu uživatele](configure-user-consent.md)
- [Pracovní postup konfigurace souhlasu správce](configure-admin-consent-workflow.md)
