---
title: Odebrat uživatele nebo skupinu přiřazení z podnikové aplikace v Azure Active Directory | Microsoft Docs
description: Postup přiřazení přístup uživatele nebo skupinu odebrat z podnikové aplikace v Azure Active Directory
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
ms.topic: article
ms.date: 02/14/2018
ms.author: barbkess
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: b6da8eed16b67db098ceb90079b7da7dfadcd5e3
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35303311"
---
# <a name="remove-a-user-or-group-assignment-from-an-enterprise-app-in-azure-active-directory"></a>Odebrat uživatele nebo skupinu přiřazení z podnikové aplikace v Azure Active Directory
Je snadno odebrat uživatele nebo skupiny z se přiřadí přístup k jednomu z vaší podnikové aplikace v Azure Active Directory (Azure AD). Musí mít příslušná oprávnění ke správě firemní aplikace a musí být globální správce adresáře.

> [!NOTE]
> Pro Microsoft Applications (například aplikace Office 365) pomocí prostředí PowerShell odstranit uživatelům podnikové aplikace.

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-in-the-azure-portal"></a>Jak odebrat uživatele nebo skupiny přiřazení firemní aplikace na portálu Azure?
1. Přihlaste se k [portál Azure](https://portal.azure.com) pomocí účtu, který je globální správce adresáře.
2. Vyberte **další služby**, zadejte **Azure Active Directory** v textovém poli a potom vyberte **Enter**.
3. Na **Azure Active Directory - *directoryname***  (to znamená, Azure AD stránce pro adresář spravujete), vyberte **podnikové aplikace, které**.

    ![Otevírání podnikové aplikace](./media/remove-user-or-group-access-portal/open-enterprise-apps.png)
4. Na **podnikové aplikace, které** vyberte **všechny aplikace**. Zobrazí seznam aplikací, které můžete spravovat.
5. Na **podnikové aplikace – všechny aplikace** vyberte aplikaci.
6. Na ***appname*** (to znamená, že stránka s názvem vybranou aplikaci v názvu), vyberte **uživatelé a skupiny**.

    ![Výběr uživatelů nebo skupin](./media/remove-user-or-group-access-portal/remove-app-users.png)
7. Na ***appname*** **-uživatele & přiřazení skupiny** stránky, vyberte jednu z další uživatele nebo skupiny a pak vyberte **odebrat** příkaz. Zkontrolujte vaše rozhodnutí příkazového řádku.

    ![Vyberte příkaz odebrat](./media/remove-user-or-group-access-portal/remove-users.png)

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-using-powershell"></a>Jak odebrat uživatele nebo skupiny přiřazení podnikové aplikace pomocí prostředí PowerShell?
1. Otevřete příkazový řádek se zvýšenými oprávněními prostředí Windows PowerShell.

    >[!NOTE] 
    > Potřebujete nainstalovat modul AzureAD (použijte příkaz `Install-Module -Name AzureAD`). Pokud se zobrazí výzva k instalaci modulu NuGet nebo nového modulu Azure Active Directory V2 PowerShell, zadejte Y a stiskněte klávesu ENTER.

2. Spustit `Connect-AzureAD` a přihlaste se pomocí uživatelského účtu globálního správce.
3. Pomocí následujícího skriptu přiřadit uživatele a role k aplikaci:

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

- [Zobrazit všechny moje skupin](../active-directory-groups-view-azure-portal.md)
- [Přiřazení uživatele nebo skupiny do aplikace enterprise](assign-user-or-group-access-portal.md)
- [Zakázat přihlášení uživatele pro aplikaci, enterprise](disable-user-sign-in-portal.md)
- [Změna názvu nebo logo aplikace enterprise](change-name-or-logo-portal.md)
