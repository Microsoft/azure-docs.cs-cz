---
title: Výpis přiřazení rolí Azure AD
description: V centru pro správu Azure Active Directory teď můžete zobrazit a spravovat členy role správce Azure Active Directory.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: de546ef091b1a8e996f286b0c9af45e93488b5b4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103467645"
---
# <a name="list-azure-ad-role-assignments"></a>Výpis přiřazení rolí Azure AD

Tento článek popisuje, jak zobrazit seznam rolí, které jste přiřadili v Azure Active Directory (Azure AD). V Azure Active Directory (Azure AD) se role dají přiřadit v oboru celé organizace nebo v oboru s jednou aplikací.

- Přiřazení rolí v oboru v rámci organizace se přidávají do a dají se zobrazit v seznamu přiřazení jedné aplikační role.
- Přiřazení rolí v rámci jedné aplikace nejsou přidána do a nelze je zobrazit v seznamu přiřazení s rozsahem v rámci organizace.

## <a name="list-role-assignments-in-the-azure-portal"></a>Seznam přiřazení rolí v Azure Portal

Tento postup popisuje, jak zobrazit seznam přiřazení rolí s rozsahem v rámci organizace.

1. Přihlaste se do [centra pro správu Azure AD](https://aad.portal.azure.com) pomocí oprávnění správce privilegované role nebo globální správce v organizaci Azure AD.
1. Vyberte **Azure Active Directory**, vyberte **role a správci** a pak vyberte roli, kterou chcete otevřít, a zobrazte její vlastnosti.
1. Vyberte **přiřazení** k vypsání přiřazení rolí.

    ![Seznam přiřazení rolí a oprávnění při otevření role ze seznamu](./media/view-assignments/role-assignments.png)

## <a name="list-my-role-assignments"></a>Vypsat přiřazení mých rolí

Je snadné zobrazit také seznam vlastních oprávnění. Výběrem **role** na stránce **role a správci** zobrazíte aktuálně přiřazené role.

## <a name="download-role-assignments"></a>Stažení přiřazení rolí

Pokud chcete stáhnout všechna přiřazení pro určitou roli, na stránce **role a správci** vyberte roli a pak vyberte **Stáhnout přiřazení rolí**. Stáhne se soubor CSV se seznamem přiřazení všech oborů pro danou roli.

![Stáhnout všechna přiřazení pro roli](./media/view-assignments/download-role-assignments.png)

## <a name="list-role-assignments-using-azure-ad-powershell"></a>Vypsání přiřazení rolí pomocí Azure AD PowerShellu

Tato část popisuje zobrazení přiřazení role s rozsahem v rámci organizace. Tento článek používá modul [Azure Active Directory PowerShell verze 2](/powershell/module/azuread/#directory_roles) . Pokud chcete zobrazit přiřazení rozsahu jedné aplikace pomocí PowerShellu, můžete použít rutiny v části [přiřazení vlastních rolí k prostředí PowerShell](custom-assign-powershell.md).

### <a name="prepare-powershell"></a>Příprava PowerShellu

Nejdřív je nutné [Stáhnout modul Azure AD Preview PowerShell](https://www.powershellgallery.com/packages/AzureAD/).

K instalaci modulu Azure AD PowerShell použijte následující příkazy:

``` PowerShell
Install-Module -Name AzureADPreview
Import-Module -Name AzureADPreview
```

Chcete-li ověřit, zda je modul připravený k použití, použijte následující příkaz:

``` PowerShell
Get-Module -Name AzureADPreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    AzureADPreview               {Add-AzureADAdministrati...}
```

### <a name="list-role-assignments"></a>Zobrazení seznamu přiřazení rolí

Příklad výpisu přiřazení rolí

``` PowerShell
# Fetch list of all directory roles with object ID
Get-AzureADDirectoryRole

# Fetch a specific directory role by ID
$role = Get-AzureADDirectoryRole -ObjectId "5b3fe201-fa8b-4144-b6f1-875829ff7543"

# Fetch role membership for a role
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId | Get-AzureADUser
```

## <a name="list-role-assignments-using-microsoft-graph-api"></a>Vypsání přiřazení rolí pomocí rozhraní Microsoft Graph API

Tato část popisuje, jak zobrazit seznam přiřazení rolí s rozsahem v rámci organizace.  K vypsání přiřazení rolí oboru jedné aplikace pomocí Graph API můžete použít operace v části [přiřazení vlastních rolí s Graph API](custom-assign-graph.md).

Požadavek HTTP na získání přiřazení role pro danou definici role

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

## <a name="list-role-assignments-with-single-application-scope"></a>Seznam přiřazení rolí s oborem jedné aplikace

Tato část popisuje, jak zobrazit seznam přiřazení rolí s rozsahem jedné aplikace. Tato funkce je aktuálně ve verzi Public Preview.

1. Přihlaste se do [centra pro správu Azure AD](https://aad.portal.azure.com) pomocí oprávnění správce privilegované role nebo globální správce v organizaci Azure AD.
1. Vyberte **Registrace aplikací** a pak vyberte registraci aplikace a zobrazte její vlastnosti. Možná budete muset vybrat **všechny aplikace** , abyste viděli úplný seznam registrací aplikací ve vaší organizaci Azure AD.

    ![Vytvoření nebo úprava registrací aplikací ze stránky Registrace aplikací](./media/view-assignments/app-reg-all-apps.png)

1. V registraci aplikace vyberte **role a správci** a pak výběrem role zobrazíte její vlastnosti.

    ![Seznam přiřazení rolí registrace aplikací ze stránky Registrace aplikací](./media/view-assignments/app-reg-assignments.png)

1. Vyberte **přiřazení** k vypsání přiřazení rolí. Při otevření stránky přiřazení v rámci registrace aplikace se zobrazí přiřazení rolí, která jsou vymezená na tento prostředek služby Azure AD.

    ![Seznam přiřazení rolí registrace aplikace z vlastností registrace aplikace](./media/view-assignments/app-reg-assignments-2.png)

## <a name="next-steps"></a>Další kroky

* Můžete s námi sdílet na [fóru role pro správu Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Další informace o rolích a přiřazení rolí správců najdete v tématu [přiřazení rolí správce](permissions-reference.md).
* Pro výchozí uživatelská oprávnění si přečtěte téma [Porovnání výchozích oprávnění typu Host a uživatel](../fundamentals/users-default-permissions.md).
