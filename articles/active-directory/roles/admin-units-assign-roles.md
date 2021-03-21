---
title: Přiřazení a výpis rolí s rozsahem jednotky pro správu – Azure Active Directory | Microsoft Docs
description: Pomocí jednotek pro správu Omezte rozsah přiřazení rolí v Azure Active Directory.
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1fc0c4bf9f71a8fe7e8cf49b83d32ac594dbe062
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103011377"
---
# <a name="assign-scoped-roles-to-an-administrative-unit"></a>Přiřazení vymezených rolí k jednotce pro správu

V Azure Active Directory (Azure AD) můžete pro podrobnější řízení správy přiřadit uživatele k roli Azure AD s oborem, který je omezený na jednu nebo více jednotek pro správu.

Informace o přípravě na používání PowerShellu a Microsoft Graph pro správu jednotek správy najdete v [tématu Začínáme.](admin-units-manage.md#get-started)

## <a name="available-roles"></a>Dostupné role

Role  |  Popis
----- |  -----------
Správce ověřování  |  Má přístup k informacím o způsobu ověřování, nastavení a resetování pro libovolného uživatele bez oprávnění správce v přiřazené jednotce pro správu.
Správce skupin  |  Může spravovat všechny aspekty nastavení skupin a skupin, jako jsou zásady pojmenování a vypršení platnosti, jenom v přiřazené jednotce pro správu.
Správce helpdesku  |  Může resetovat hesla správců bez oprávnění správce a helpdesku pouze v přiřazené jednotce pro správu.
Správce licencí  |  Může přiřazovat, odebírat a aktualizovat přiřazení licencí pouze v rámci jednotky pro správu.
Správce hesel  |  Může resetovat hesla správců, kteří nejsou správci a hesla, jenom v rámci přiřazené jednotky pro správu.
Správce uživatelů  |  Může spravovat všechny aspekty uživatelů a skupin, včetně resetování hesel pro omezené správce v rámci přiřazené jednotky pro správu.

## <a name="security-principals-that-can-be-assigned-to-a-scoped-role"></a>Objekty zabezpečení, které je možné přiřadit k vymezené roli

K roli s rozsahem jednotky pro správu je možné přiřadit následující objekty zabezpečení:

* Uživatelé
* Role – přiřaditelné cloudové skupiny (Preview)
* Hlavní název služby (SPN)

## <a name="assign-a-scoped-role"></a>Přiřazení vymezené role

Můžete přiřadit vymezenou roli pomocí Azure Portal, PowerShellu nebo Microsoft Graph.

### <a name="use-the-azure-portal"></a>Použití webu Azure Portal

1. V Azure Portal přejdete do **služby Azure AD**.

1. Vyberte **jednotky pro správu** a potom vyberte jednotku správy, ke které chcete přiřadit obor role uživatele. 

1. V levém podokně vyberte **role a správci** a seznamte se se všemi dostupnými rolemi.

   ![Snímek obrazovky s podoknem role a správci pro výběr jednotky pro správu, jejíž rozsah role chcete přiřadit.](./media/admin-units-assign-roles/select-role-to-scope.png)

1. Vyberte roli, kterou chcete přiřadit, a pak vyberte **Přidat přiřazení**. 

1. V podokně **Přidat přiřazení** vyberte jednoho nebo více uživatelů, kteří mají být přiřazeni k roli.

   ![Vyberte roli pro obor a pak vyberte přidat přiřazení.](./media/admin-units-assign-roles/select-add-assignment.png)

> [!Note]
> Postup přiřazení role v jednotce pro správu pomocí Azure AD Privileged Identity Management (PIM) najdete v tématu [přiřazení rolí Azure AD v PIM](../privileged-identity-management/pim-how-to-add-role-to-user.md?tabs=new#assign-a-role-with-restricted-scope).

### <a name="use-powershell"></a>Použití prostředí PowerShell

```powershell
$adminUser = Get-AzureADUser -ObjectId "Use the user's UPN, who would be an admin on this unit"
$role = Get-AzureADDirectoryRole | Where-Object -Property DisplayName -EQ -Value "User Account Administrator"
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
$roleMember = New-Object -TypeName Microsoft.Open.AzureAD.Model.RoleMemberInfo
$roleMember.ObjectId = $adminUser.ObjectId
Add-AzureADMSScopedRoleMembership -ObjectId $adminUnitObj.ObjectId -RoleObjectId $role.ObjectId -RoleMemberInfo $roleMember
```

Zvýrazněný oddíl můžete změnit podle potřeby konkrétního prostředí.

### <a name="use-microsoft-graph"></a>Použít Microsoft Graph

Žádost

```http
POST /directory/administrativeUnits/{admin-unit-id}/scopedRoleMembers
```
    
Text

```http
{
  "roleId": "roleId-value",
  "roleMemberInfo": {
    "id": "id-value"
  }
}
```

## <a name="view-a-list-of-the-scoped-admins-in-an-administrative-unit"></a>Zobrazení seznamu správců s vymezeným oborem v jednotce pro správu

Seznam správců s vymezeným oborem můžete zobrazit pomocí Azure Portal, PowerShellu nebo Microsoft Graph.

### <a name="use-the-azure-portal"></a>Použití webu Azure Portal

V [části jednotky pro správu Azure AD](https://ms.portal.azure.com/?microsoft_aad_iam_adminunitprivatepreview=true&microsoft_aad_iam_rbacv2=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AdminUnit)můžete zobrazit všechna přiřazení rolí vytvořená s oborem jednotky pro správu. 

1. V Azure Portal přejdete do **služby Azure AD**.

1. V levém podokně vyberte **jednotky pro správu** a potom vyberte jednotku pro správu seznamu přiřazení rolí, která chcete zobrazit. 

1. Vyberte **role a správci** a pak otevřete roli pro zobrazení přiřazení v jednotce pro správu.

### <a name="use-powershell"></a>Použití prostředí PowerShell

```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
Get-AzureADMSScopedRoleMembership -ObjectId $adminUnitObj.ObjectId | fl *
```

Zvýrazněný oddíl můžete změnit podle potřeby konkrétního prostředí.

### <a name="use-microsoft-graph"></a>Použít Microsoft Graph

Žádost

```http
GET /directory/administrativeUnits/{admin-unit-id}/scopedRoleMembers
```

Text

```http
{}
```

## <a name="next-steps"></a>Další kroky

- [Využití cloudových skupin ke správě přiřazení rolí](groups-concept.md)
- [Řešení potíží s rolemi přiřazenými ke skupinám cloudu](groups-faq-troubleshooting.md)
