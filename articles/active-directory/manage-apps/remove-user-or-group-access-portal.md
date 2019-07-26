---
title: Odebrání přiřazení uživatelů nebo skupin z aplikace v Azure Active Directory | Microsoft Docs
description: Jak odebrat přiřazení přístupu uživatele nebo skupiny z podnikové aplikace v Azure Active Directory
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
ms.openlocfilehash: 380816283156969c47f45a9b47435688df91f4ca
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/22/2019
ms.locfileid: "68381040"
---
# <a name="remove-a-user-or-group-assignment-from-an-enterprise-app-in-azure-active-directory"></a>Odebrání přiřazení uživatele nebo skupiny z podnikové aplikace v Azure Active Directory

Uživateli nebo skupině je snadné odebrat přiřazený přístup k jedné z podnikových aplikací v Azure Active Directory (Azure AD). Pro správu podnikové aplikace potřebujete příslušná oprávnění. A musíte být globálním správcem adresáře.

> [!NOTE]
> Pro aplikace Microsoftu (například aplikace Office 365) můžete odebrat uživatele do podnikové aplikace pomocí PowerShellu.

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-in-the-azure-portal"></a>Návody v Azure Portal odebrat přiřazení uživatele nebo skupiny k podnikové aplikaci?

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com) prostřednictvím účtu, který má k adresáři oprávnění globálního správce.
1. Vyberte **všechny služby**, do textového pole zadejte **Azure Active Directory** a pak vyberte **ENTER**.
1. Na stránce **Azure Active Directory- *Directory***  (tj. na stránce Azure AD pro adresář, který spravujete) vyberte **podnikové aplikace**.
1. Na stránce **podnikové aplikace – všechny aplikace** se zobrazí seznam aplikací, které můžete spravovat. Vyberte aplikaci.
1. Na stránce s přehledem ***AppName*** (to znamená Stránka s názvem vybrané aplikace v názvu) vyberte **Uživatelé & skupiny**.
1. Na stránce ***AppName*** **– User & Group Assignment** vyberte jednoho nebo více uživatelů nebo skupin a pak vyberte příkaz **Remove** . Potvrďte své rozhodnutí na příkazovém řádku.

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-using-powershell"></a>Návody odebrat přiřazení uživatele nebo skupiny do podnikové aplikace pomocí PowerShellu?

1. Otevřete příkazový řádek Windows PowerShellu se zvýšenými oprávněními.

   > [!NOTE]
   > Je potřeba nainstalovat modul AzureAD (použijte příkaz `Install-Module -Name AzureAD`). Pokud se zobrazí výzva k instalaci modulu NuGet nebo nového modulu Azure Active Directory v2 PowerShell, zadejte Y a stiskněte ENTER.

1. Spusťte `Connect-AzureAD` a přihlaste se pomocí uživatelského účtu globálního správce.
1. Pomocí následujícího skriptu Odeberte uživatele a roli z aplikace:

    ```powershell
    # Store the proper parameters
    $user = get-azureaduser -ObjectId <objectId>
    $spo = Get-AzureADServicePrincipal -ObjectId <objectId>

    #Get the ID of role assignment 
    $assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $spo.ObjectId | Where {$_.PrincipalDisplayName -eq $user.DisplayName}

    #if you run the following, it will show you what is assigned what
    $assignments | Select *

    #To remove the App role assignment run the following command.
    Remove-AzureADServiceAppRoleAssignment -ObjectId $spo.ObjectId -AppRoleAssignmentId $assignments[assignment #].ObjectId
    ```

## <a name="next-steps"></a>Další postup

- [Zobrazit všechny moje skupiny](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Přiřazení uživatele nebo skupiny k podnikové aplikaci](assign-user-or-group-access-portal.md)
- [Zakázání přihlášení uživatelů pro podnikovou aplikaci](disable-user-sign-in-portal.md)
- [Změna názvu nebo loga podnikové aplikace](change-name-or-logo-portal.md)
