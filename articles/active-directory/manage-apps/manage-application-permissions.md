---
title: Správa oprávnění uživatelů a správců – Azure Active Directory | Microsoft Docs
description: Přečtěte si, jak kontrolovat a spravovat oprávnění pro aplikaci v Azure AD. Například pokud chcete odvolat všechna oprávnění udělená aplikaci.
services: active-directory
author: mimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 7/10/2020
ms.author: mimart
ms.reviewer: luleonpla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00d878c7b2f78d037e89235f3bb30c02fd11a7ae
ms.sourcegitcommit: 0b2367b4a9171cac4a706ae9f516e108e25db30c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2020
ms.locfileid: "86277630"
---
# <a name="take-action-on-overpriviledged-or-suspicious-application-in-azure-active-directory"></a>Provedení akce na overpriviledged nebo podezřelé aplikace v Azure Active Directory

Přečtěte si, jak kontrolovat a spravovat oprávnění aplikace. V závislosti na scénáři vám tento článek poskytne různé akce, které můžete provést k zabezpečení aplikace. To platí pro všechny aplikace, které byly přidány do tenanta Azure Active Directory (Azure AD) prostřednictvím souhlasu uživatele nebo správce.

Další informace o tom, jak se předávají aplikacím, najdete v tématu [Azure Active Directory souhlasu Framework](../develop/consent-framework.md).

## <a name="prerequisites"></a>Předpoklady

Schopnost provádět následující akce vyžaduje, abyste se přihlásili jako globální správce, správce aplikace nebo správce cloudové aplikace.

Chcete-li omezit přístup k aplikacím, je nutné vyžadovat přiřazení uživatele a pak přiřadit uživatele nebo skupiny k aplikaci.  Další informace najdete v tématu [metody přiřazení uživatelů a skupin](methods-for-assigning-users-and-groups.md).

K portálu Azure AD můžete přistupovat a získat tak kontextové skripty PowerShellu k provedení těchto akcí.
 
1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako globální správce, správce aplikace nebo správce cloudové aplikace.
2. Vyberte **Azure Active Directory**  >  **podnikových aplikací**.
3. Vyberte aplikaci, pro kterou chcete omezit přístup.
4. Vyberte **oprávnění**. Na panelu příkazů vyberte **zkontrolovat oprávnění**.

![Zkontrolovat oprávnění](./media/manage-application-permissions/review-permissions.png)

## <a name="i-want-to-control-access-to-an-application"></a>Chci řídit přístup k aplikaci

Doporučujeme, abyste omezení přístupu k této aplikaci omezili zapnutím nastavení přiřazení uživatelů na.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako globální správce, správce aplikace nebo správce cloudové aplikace.
2. Vyberte **Azure Active Directory**  >  **podnikových aplikací**.
3. Vyberte aplikaci, pro kterou chcete omezit přístup.
4. Vyberte **vlastnosti** a pak nastavte nastavení požadováno pro uživatele na Ano.
5. Vyberte **uživatele a skupiny** a potom odeberte nechtěné uživatele přiřazené k aplikaci.
6. Přiřaďte aplikaci uživatele nebo skupiny.

Volitelně můžete odebrat všechny uživatele přiřazené k aplikaci pomocí prostředí PowerShell.

## <a name="i-want-to-revoke-all-permissions-for-an-application"></a>Chci odvolat všechna oprávnění pro aplikaci

Použití PowerShellu odvolá všechna oprávnění udělená této aplikaci.

> [!NOTE]
> Odvolání aktuálně uděleného oprávnění neukončí uživatele reconseing k aplikacím. Pokud chcete uživatelům zablokovat souhlas s aplikací, přečtěte si téma [Konfigurace způsobu souhlasu koncových uživatelů s aplikacemi](configure-user-consent.md).

Volitelné, aplikaci můžete zakázat, pokud chcete uživatelům zablokovat přístup k aplikaci a aplikaci pro přístup k vašim datům.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako globální správce, správce aplikace nebo správce cloudové aplikace.
2. Vyberte **Azure Active Directory**  >  **podnikových aplikací**.
3. Vyberte aplikaci, pro kterou chcete omezit přístup.
4. Vyberte **vlastnosti** a pak nastavte možnost povoleno pro uživatele, aby se přihlásili? na ne.

## <a name="application-is-suspicious-and-i-want-to-investigate"></a>Aplikace je podezřelá a chci ji prozkoumat.

Doporučujeme, abyste omezili přístup k této aplikaci tím, že zapnete nastavení přiřazení uživatelů na a zkontrolujete oprávnění, která uživatelé a správci udělili aplikaci.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako globální správce, správce aplikace nebo správce cloudové aplikace.
3. Vyberte **Azure Active Directory**  >  **podnikových aplikací**.
5. Vyberte aplikaci, pro kterou chcete omezit přístup.
6. Vyberte **vlastnosti** a pak nastavte nastavení požadováno pro uživatele na Ano.
7. Vyberte **oprávnění** a zkontrolujte oprávnění správce a uživatel se souhlasem.

Volitelné, můžete:

- Pomocí PowerShellu odeberte všechny přiřazené uživatele, aby je nemuseli přihlašovat k aplikaci.
- Pomocí PowerShellu zruší platnost tokenů aktualizace pro uživatele, kteří mají přístup k aplikaci.
- Pomocí PowerShellu Odvolejte všechna oprávnění pro tuto aplikaci.
- Zakažte aplikaci, aby blokovala přístup uživatelů, a zastavte těmto aplikacím přístup k vašim datům.


## <a name="application-is-malicious-and-im-compromised"></a>Aplikace je škodlivá a došlo k ohrožení zabezpečení.

Doporučujeme, abyste aplikaci zakázali, aby uživatelům zablokovala přístup k aplikaci a aplikaci z přístupu k vašim datům. Pokud aplikaci odstraníte, koncoví uživatelé budou moci znovu vyjádřit souhlas s aplikací a udělit přístup k vašim datům.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako globální správce, správce aplikace nebo správce cloudové aplikace.
2. Vyberte **Azure Active Directory**  >  **podnikových aplikací**.
3. Vyberte aplikaci, pro kterou chcete omezit přístup.
4. Vyberte **vlastnosti** a pak zkopírujte ID objektu.

### <a name="powershell-commands"></a>Příkazy prostředí PowerShell


Načíst ID instančního objektu služby

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako globální správce, správce aplikace nebo správce cloudové aplikace.
2. Vyberte **Azure Active Directory**  >  **podnikových aplikací**.
3. Vyberte aplikaci, pro kterou chcete omezit přístup.
4. Vyberte **vlastnosti** a pak zkopírujte ID objektu.

```powershell
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    $sp.ObjectId
```
Odebere všechny uživatele přiřazené k aplikaci.
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

Odvolat oprávnění udělená aplikaci

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
Zrušit platnost obnovovacích tokenů
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
