---
title: Odebrání přiřazení uživatelů nebo skupin z aplikace ve službě Azure AD
description: Odebrání přiřazení přístupu uživatele nebo skupiny z podnikové aplikace ve službě Azure Active Directory
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
ms.openlocfilehash: edf918b57212cf2adfbffb358a0257d9dbea85e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275869"
---
# <a name="remove-a-user-or-group-assignment-from-an-enterprise-app-in-azure-active-directory"></a>Odebrání přiřazení uživatele nebo skupiny z podnikové aplikace ve službě Azure Active Directory

Je snadné odebrat uživatele nebo skupinu z přiřazený přístup k jedné z vašich podnikových aplikací ve službě Azure Active Directory (Azure AD). Ke správě podnikové aplikace potřebujete příslušná oprávnění. A musíte být globální správce pro adresář.

> [!NOTE]
> Pro aplikace Microsoft (například aplikace Office 365) můžete odebrat uživatele do podnikové aplikace pomocí PowerShellu.

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-in-the-azure-portal"></a>Jak na webu Azure Portal odeberu přiřazení uživatele nebo skupiny k podnikové aplikaci?

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com) prostřednictvím účtu, který má k adresáři oprávnění globálního správce.
1. Vyberte **Všechny služby**, do textového pole zadejte **Službu Azure Active Directory** a pak vyberte **Enter**.
1. Na stránce **Azure Active Directory – název *adresáře* ** (tj. stránka Azure AD pro adresář, který spravujete), vyberte **podnikové aplikace**.
1. Na stránce **Podnikové aplikace – všechny aplikace** se zobrazí seznam aplikací, které můžete spravovat. Vyberte aplikaci.
1. Na stránce s přehledem ***názvu aplikace*** (tj. stránka s názvem vybrané aplikace v názvu) vyberte **Uživatelé & skupiny**.
1. Na stránce ***Název aplikace*** – Uživatel & **Přiřazení skupiny** vyberte jednoho z více uživatelů nebo skupin a pak vyberte **příkaz Odebrat.** Na výzvu potvrďte své rozhodnutí.

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-using-powershell"></a>Jak můžu odebrat přiřazení uživatele nebo skupiny k podnikové aplikaci pomocí PowerShellu?

1. Otevřete příkazový řádek prostředí Windows PowerShell se zvýšenými oprávněními.

   > [!NOTE]
   > Musíte nainstalovat modul AzureAD (použijte `Install-Module -Name AzureAD`příkaz ). Pokud se zobrazí výzva k instalaci modulu NuGet nebo nového modulu PowerShell azure active directory V2, zadejte Y a stiskněte klávesu ENTER.

1. Spouštět `Connect-AzureAD` a přihlašovat se pomocí uživatelského účtu globálního správce.
1. K odebrání uživatele a role z aplikace použijte následující skript:

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

## <a name="next-steps"></a>Další kroky

- [Zobrazit všechny mé skupiny](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Přiřazení uživatele nebo skupiny k podnikové aplikaci](assign-user-or-group-access-portal.md)
- [Zakázání přihlášení uživatelů k podnikové aplikaci](disable-user-sign-in-portal.md)
- [Změna názvu nebo loga podnikové aplikace](change-name-or-logo-portal.md)
