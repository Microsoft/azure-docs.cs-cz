---
title: Zakázání přihlášení uživatelů k podnikové aplikaci ve službě Azure AD
description: Jak zakázat podnikovou aplikaci, aby se k ní ve službě Azure Active Directory nepřihlašují žádní uživatelé.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74274095"
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>Zakázání přihlášení uživatelů k podnikové aplikaci ve službě Azure Active Directory

Je snadné zakázat podnikovou aplikaci, aby se k ní žádný uživatel nemohl přihlásit ve službě Azure Active Directory (Azure AD). Ke správě podnikové aplikace potřebujete příslušná oprávnění. A musíte být globální správce pro adresář.

## <a name="how-do-i-disable-user-sign-ins"></a>Jak zakážu přihlášení uživatelů?

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com) prostřednictvím účtu, který má k adresáři oprávnění globálního správce.
1. Vyberte **Všechny služby**, do textového pole zadejte **Službu Azure Active Directory** a pak vyberte **Enter**.
1. V podokně***název adresáře*** **služby Azure Active Directory** -  (tj. podokno Azure AD pro adresář, který spravujete) vyberte **podnikové aplikace**.
1. V podokně **Podnikové aplikace – všechny aplikace** se zobrazí seznam aplikací, které můžete spravovat. Vyberte aplikaci.
1. V podokně ***název aplikace*** (tj. podokno s názvem vybrané aplikace v názvu) vyberte **Vlastnosti**.
1. V podokně**Vlastnosti** ***názvu*** - aplikace vyberte možnost **Ne** pro **povoleno, aby se uživatelé přihlásili?**.
1. Vyberte příkaz **Uložit.**

## <a name="use-azure-ad-powershell-to-disable-an-unlisted-app"></a>Zakázání neuvedené aplikace pomocí Prostředí Azure AD PowerShell

Pokud znáte AppId aplikace, která se nezobrazuje v seznamu podnikových aplikací (například proto, že jste aplikaci odstranili nebo instanční objekt ještě nebyl vytvořen z důvodu, že aplikace je předem autorizovaná společností Microsoft), můžete ručně vytvořit instanční objekt pro aplikaci a pak ji zakázat pomocí [rutiny AzureAD PowerShell](https://docs.microsoft.com/powershell/module/azuread/New-AzureADServicePrincipal?view=azureadps-2.0).

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

* [Zobrazit všechny mé skupiny](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](assign-user-or-group-access-portal.md)
* [Odebrání přiřazení uživatele nebo skupiny z podnikové aplikace](remove-user-or-group-access-portal.md)
* [Změna názvu nebo loga podnikové aplikace](change-name-or-logo-portal.md)
