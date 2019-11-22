---
title: Zakázání přihlášení uživatelů pro podnikovou aplikaci ve službě Azure AD
description: Jak zakázat podnikovou aplikaci, aby se k ní uživatelé nemuseli přihlašovat v Azure Active Directory
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: mimart
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10553898376c4b9236ee62718fffccd45b12d70b
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74274095"
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>Zakázání přihlášení uživatelů pro podnikovou aplikaci v Azure Active Directory

Podniková aplikace se dá snadno zakázat, takže se k ní uživatelé nemůžou přihlašovat v Azure Active Directory (Azure AD). Pro správu podnikové aplikace potřebujete příslušná oprávnění. A musíte být globálním správcem adresáře.

## <a name="how-do-i-disable-user-sign-ins"></a>Návody zakázat přihlášení uživatelů?

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com) prostřednictvím účtu, který má k adresáři oprávnění globálního správce.
1. Vyberte **všechny služby**, do textového pole zadejte **Azure Active Directory** a pak vyberte **ENTER**.
1. V podokně **Azure Active Directory** -  ***Directory*** (to znamená podokno Azure AD pro adresář, který spravujete) vyberte **podnikové aplikace**.
1. V podokně **podnikové aplikace – všechny aplikace** se zobrazí seznam aplikací, které můžete spravovat. Vyberte aplikaci.
1. V podokně ***AppName*** (to znamená podokno s názvem vybrané aplikace v názvu) vyberte **vlastnosti**.
1. V podokně **vlastnosti** - ***AppName*** vyberte možnost **ne** , pokud **chcete uživatelům povolit přihlášení?** .
1. Vyberte příkaz **Uložit** .

## <a name="use-azure-ad-powershell-to-disable-an-unlisted-app"></a>Použití Azure AD PowerShellu k zakázání aplikace, které nejsou v seznamu

Pokud znáte AppId aplikace, která se nezobrazuje v seznamu podnikových aplikací (například proto, že jste aplikaci odstranili nebo pokud jste ještě nevytvořili objekt služby, protože aplikace je předem autorizována společností Microsoft), můžete instanční objekt ručně vytvořit pro aplikace a pak ji zakažte pomocí [rutiny prostředí PowerShell AzureAD](https://docs.microsoft.com/powershell/module/azuread/New-AzureADServicePrincipal?view=azureadps-2.0).

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
* [Odebrání přiřazení uživatele nebo skupiny z podnikové aplikace](remove-user-or-group-access-portal.md)
* [Změna názvu nebo loga podnikové aplikace](change-name-or-logo-portal.md)
