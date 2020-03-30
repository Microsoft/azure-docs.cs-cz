---
title: Zobrazení přiřazení vlastních rolí na portálu Azure AD | Dokumenty společnosti Microsoft
description: Teď můžete zobrazit a spravovat členy role správce Azure AD v Centru pro správu Azure AD.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f04bd7788a9cc9657e14aedfb153182d6e53eb95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259705"
---
# <a name="view-custom-role-assignments-in-azure-active-directory"></a>Zobrazení přiřazení vlastních rolí ve službě Azure Active Directory

Tento článek popisuje, jak zobrazit vlastní role, které jste přiřadili ve službě Azure Active Directory (Azure AD). Ve službě Azure Active Directory (Azure AD) lze role přiřadit v oboru pro celou organizaci nebo s oborem jedné aplikace.

- Přiřazení rolí v oboru celé organizace jsou přidány do seznamu přiřazení rolí jedné aplikace a lze je vidět v seznamu přiřazení rolí jedné aplikace.
- Přiřazení rolí v oboru jedné aplikace nejsou přidány do seznamu přiřazení s rozsahem celé organizace a nelze je však nevidět.

## <a name="view-role-assignments-in-the-azure-portal"></a>Zobrazení přiřazení rolí na webu Azure Portal

Tento postup popisuje zobrazení přiřazení role s rozsahem celé organizace.

1. Přihlaste se do  [Centra pro správu Azure AD](https://aad.portal.azure.com)s oprávněními správce privilegovaných rolí nebo globálního správce v organizaci Azure AD.
1. Vyberte **Službu Azure Active Directory**, vyberte **Role a správce**a pak vyberte roli, která ji otevře a zobrazí její vlastnosti.
1. Výběrem **možnosti Přiřazení** zobrazíte přiřazení pro roli.

    ![Zobrazení přiřazení a oprávnění rolí při otevření role ze seznamu](./media/roles-view-assignments/role-assignments.png)

## <a name="view-role-assignments-using-azure-ad-powershell"></a>Zobrazení přiřazení rolí pomocí Prostředí Azure AD PowerShell

Tato část popisuje zobrazení přiřazení role s rozsahem celé organizace. Tento článek používá modul [Azure Active Directory PowerShell verze 2.](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#directory_roles) Chcete-li zobrazit přiřazení oboru jedné aplikace pomocí prostředí PowerShell, můžete použít rutiny v [části Přiřadit vlastní role pomocí prostředí PowerShell](roles-assign-powershell.md).

### <a name="prepare-powershell"></a>Příprava prostředí PowerShell

Nejprve je nutné [stáhnout modul Azure AD preview PowerShell](https://www.powershellgallery.com/packages/AzureAD/).

Chcete-li nainstalovat modul Azure AD PowerShell, použijte následující příkazy:

``` PowerShell
install-module azureadpreview
import-module azureadpreview
```

Chcete-li ověřit, zda je modul připraven k použití, použijte následující příkaz:

``` PowerShell
get-module azuread
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}
```

### <a name="view-the-assignments-of-a-role"></a>Zobrazení přiřazení role

Příklad zobrazení přiřazení role.

``` PowerShell
# Fetch list of all directory roles with object ID
Get-AzureADDirectoryRole

# Fetch a specific directory role by ID
$role = Get-AzureADDirectoryRole -ObjectId "5b3fe201-fa8b-4144-b6f1-875829ff7543"

# Fetch role membership for a role
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId | Get-AzureADUser
```

## <a name="view-role-assignments-using-microsoft-graph-api"></a>Zobrazení přiřazení rolí pomocí rozhraní Microsoft Graph API

Tato část popisuje zobrazení přiřazení role s rozsahem celé organizace.  Chcete-li zobrazit přiřazení oboru jedné aplikace pomocí rozhraní Graph API, můžete použít operace v [přiřadit vlastní role s rozhraním Graph API](roles-assign-graph.md).

Požadavek HTTP pro získání přiřazení role pro danou definici role.

GET

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments&$filter=roleDefinitionId eq ‘<object-id-or-template-id-of-role-definition>’
```

Odpověď

``` HTTP
HTTP/1.1 200 OK
{
    "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"3671d40a-1aac-426c-a0c1-a3821ebd8218",
    "resourceScopes":["/"]
}
```

## <a name="view-assignments-of-single-application-scope"></a>Zobrazit přiřazení oboru jedné aplikace

Tato část popisuje zobrazení přiřazení role s rozsahem jedné aplikace. Tato funkce je aktuálně ve verzi Public Preview.

1. Přihlaste se do  [Centra pro správu Azure AD](https://aad.portal.azure.com)s oprávněními správce privilegovaných rolí nebo globálního správce v organizaci Azure AD.
1. Vyberte **Registrace aplikací**a pak vyberte registraci aplikace, chcete-li zobrazit její vlastnosti. Možná budete muset vybrat **všechny aplikace,** abyste viděli úplný seznam registrací aplikací ve vaší organizaci Azure AD.

    ![Vytváření nebo úpravy registrací aplikací na stránce Registrace aplikací](./media/roles-create-custom/appreg-all-apps.png)

1. V registraci aplikace vyberte **Role a správce**a pak vyberte roli, která chcete zobrazit její vlastnosti.

    ![Zobrazení přiřazení rolí registrace aplikací na stránce Registrace aplikací](./media/roles-view-assignments/appreg-assignments.png)

1. Výběrem **možnosti Přiřazení** zobrazíte přiřazení pro roli. Otevření zobrazení přiřazení v rámci registrace aplikace zobrazí přiřazení, které jsou vymezeny na tento prostředek Azure AD.

    ![Zobrazení přiřazení rolí registrace aplikace z vlastností registrace aplikace](./media/roles-view-assignments/appreg-assignments-2.png)

## <a name="next-steps"></a>Další kroky

* Neváhejte se s námi podělit na [fóru rolí azure ad pro správu](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Další informace o rolích a přiřazení rolí správce najdete v tématu [Přiřazení rolí správce](directory-assign-admin-roles.md).
* Výchozí uživatelská oprávnění naleznete v [porovnání výchozích oprávnění hosta a člena uživatele](../fundamentals/users-default-permissions.md).
