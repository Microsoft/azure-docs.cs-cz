---
title: Zakázání přihlášení uživatelů pro podnikovou aplikaci ve službě Azure AD
description: Jak zakázat podnikovou aplikaci, aby se k ní uživatelé nemuseli přihlašovat v Azure Active Directory
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 04/12/2019
ms.author: iangithinji
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9af580ef8b3691a5f188ac15069dda00a5f5802
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374246"
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>Zakázání přihlášení uživatelů pro podnikovou aplikaci v Azure Active Directory

Podniková aplikace se dá snadno zakázat, takže se k ní uživatelé nemůžou přihlašovat v Azure Active Directory (Azure AD). Pro správu podnikové aplikace potřebujete příslušná oprávnění. A musíte být globálním správcem adresáře.

## <a name="how-do-i-disable-user-sign-ins"></a>Návody zakázat přihlášení uživatelů?

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com) prostřednictvím účtu, který má k adresáři oprávnění globálního správce.
1. Vyberte **všechny služby**, do textového pole zadejte **Azure Active Directory** a pak vyberte **ENTER**.
1. V podokně **Azure Active Directory**  -   **_adresář_*_ (to znamená podokno Azure AD pro adresář, který spravujete) vyberte _* Enterprise Applications**.
1. V podokně **podnikové aplikace – všechny aplikace** se zobrazí seznam aplikací, které můžete spravovat. Vyberte aplikaci.
1. V podokně ***AppName** _ (to znamená podokno s názvem vybrané aplikace v názvu) vyberte _ * vlastnosti * *.
1. V podokně ***AppName** _-_ *vlastnosti** vyberte možnost **ne** , pokud **chcete uživatelům povolit přihlášení?**.
1. Vyberte příkaz **Uložit** .

## <a name="use-azure-ad-powershell-to-disable-an-unlisted-app"></a>Použití Azure AD PowerShellu k zakázání aplikace, které nejsou v seznamu

Pokud znáte AppId aplikace, která se nezobrazuje v seznamu podnikových aplikací (třeba když jste odstranili aplikaci nebo instanční objekt ještě nevytvořili kvůli předběžnému autorizaci aplikace Microsoft), můžete instanční objekt pro aplikaci vytvořit ručně a pak ho deaktivovat pomocí [rutiny prostředí PowerShell AzureAD](/powershell/module/azuread/New-AzureADServicePrincipal).

```PowerShell
# The AppId of the app to be disabled
$appId = "{AppId}"

# Check if a service principal already exists for the app
$servicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$appId'"
if ($servicePrincipal) {
    # Service principal exists already, disable it
    Set-AzureADServicePrincipal -ObjectId $servicePrincipal.ObjectId -AccountEnabled $false
} else {
    # Service principal does not yet exist, create it and disable it at the same time
    $servicePrincipal = New-AzureADServicePrincipal -AppId $appId -AccountEnabled $false
}
```

## <a name="next-steps"></a>Další kroky

* [Zobrazit všechny moje skupiny](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](assign-user-or-group-access-portal.md)
* [Odebrání přiřazení uživatele nebo skupiny z podnikové aplikace](./assign-user-or-group-access-portal.md)
* [Změna názvu nebo loga podnikové aplikace](./add-application-portal-configure.md)
