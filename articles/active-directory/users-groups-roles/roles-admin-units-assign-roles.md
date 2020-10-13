---
title: Přiřazení a výpis rolí s rozsahem jednotky pro správu – Azure Active Directory | Microsoft Docs
description: Omezení rozsahu přiřazování rolí v Azure Active Directory pomocí jednotek pro správu
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 09/22/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 112c1c6a0fbbd7e0011890d1ce92c6e21e168137
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91818003"
---
# <a name="assign-scoped-roles-to-an-administrative-unit"></a>Přiřazení vymezených rolí k jednotce pro správu

V Azure Active Directory (Azure AD) můžete přiřadit uživatele k roli Azure AD s oborem omezeným na jednu nebo více jednotek pro správu (Austrálie) pro přesnější administrativní řízení.

Postup přípravy na používání PowerShellu a Microsoft Graph pro správu jednotek správy najdete v tématu [Začínáme](roles-admin-units-manage.md#get-started).

## <a name="roles-available"></a>Role k dispozici

Role  |  Popis
----- |  -----------
Správce ověřování  |  Má přístup k informacím o způsobu ověřování, nastavení a resetování pro libovolného uživatele bez oprávnění správce v přiřazené jednotce pro správu.
Správce skupin  |  Může spravovat všechny aspekty nastavení skupin a skupin, jako jsou zásady pojmenování a vypršení platnosti pouze v přiřazené jednotce pro správu.
Správce helpdesku  |  Může resetovat hesla správců bez oprávnění správce a helpdesku pouze v přiřazené jednotce pro správu.
Správce licencí  |  Může přiřazovat, odebírat a aktualizovat přiřazení licencí pouze v rámci jednotky pro správu.
Správce hesel  |  Může resetovat hesla správců, kteří nejsou správci a hesla, jenom v rámci přiřazené jednotky pro správu.
Správce uživatelů  |  Může spravovat všechny aspekty uživatelů a skupin, včetně resetování hesel pro omezené správce v rámci přiřazené jednotky pro správu.

## <a name="security-principals-that-can-be-assigned-to-a-scoped-role"></a>Objekty zabezpečení, které je možné přiřadit k vymezené roli

K roli s rozsahem jednotky pro správu je možné přiřadit následující objekty zabezpečení:

* Uživatelé
* Role cloudových skupin s přiřazením (Preview)
* Hlavní název služby (SPN)

## <a name="assign-a-scoped-role"></a>Přiřazení vymezené role

### <a name="azure-portal"></a>portál Azure

Na portálu můžete přejít na **jednotky pro správu Azure AD >** . Vyberte jednotku správy, ke které chcete přiřadit roli uživateli. V levém podokně vyberte role a správci a seznamte se se všemi dostupnými rolemi.

![Výběr jednotky pro správu pro změnu rozsahu role](./media/roles-admin-units-assign-roles/select-role-to-scope.png)

Vyberte roli, kterou chcete přiřadit, a pak vyberte **Přidat přiřazení**. Otevře se panel na pravé straně, kde můžete vybrat jednoho nebo více uživatelů, kteří mají být přiřazeni k roli.

![Vyberte roli pro obor a pak vyberte přidat přiřazení.](./media/roles-admin-units-assign-roles/select-add-assignment.png)

> [!Note]
>
> Pokud chcete přiřadit roli v jednotce pro správu pomocí PIM [, postupujte podle těchto kroků.](/active-directory/privileged-identity-management/pim-how-to-add-role-to-user.md#assign-a-role-with-restricted-scope)

### <a name="powershell"></a>PowerShell

```powershell
$AdminUser = Get-AzureADUser -ObjectId "Use the user's UPN, who would be an admin on this unit"
$Role = Get-AzureADDirectoryRole | Where-Object -Property DisplayName -EQ -Value "User Account Administrator"
$administrativeUnit = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
$RoleMember = New-Object -TypeName Microsoft.Open.AzureAD.Model.RoleMemberInfo
$RoleMember.ObjectId = $AdminUser.ObjectId
Add-AzureADMSScopedRoleMembership -ObjectId $administrativeUnit.ObjectId -RoleObjectId $Role.ObjectId -RoleMemberInfo $RoleMember
```

Zvýrazněný oddíl se může změnit podle potřeby pro konkrétní prostředí.

### <a name="microsoft-graph"></a>Microsoft Graph

```http
Http request
POST /directory/administrativeUnits/{id}/scopedRoleMembers
    
Request body
{
  "roleId": "roleId-value",
  "roleMemberInfo": {
    "id": "id-value"
  }
}
```

## <a name="list-the-scoped-admins-on-an-au"></a>Seznam správců s vymezeným oborem na AU

### <a name="azure-portal"></a>portál Azure

Všechna přiřazení rolí prováděná s oborem administrativní jednotky se dají zobrazit v [části jednotky pro správu Azure AD](https://ms.portal.azure.com/?microsoft_aad_iam_adminunitprivatepreview=true&microsoft_aad_iam_rbacv2=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AdminUnit). Na portálu můžete přejít na **jednotky pro správu Azure AD >** . Vyberte jednotku pro správu pro přiřazení rolí, která chcete zobrazit. Vyberte **role a správce** a otevřete roli pro zobrazení přiřazení v jednotce pro správu.

### <a name="powershell"></a>PowerShell

```powershell
$administrativeUnit = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
Get-AzureADMSScopedRoleMembership -ObjectId $administrativeUnit.ObjectId | fl *
```

Zvýrazněný oddíl se může změnit podle potřeby pro konkrétní prostředí.

### <a name="microsoft-graph"></a>Microsoft Graph

```http
Http request
GET /directory/administrativeUnits/{id}/scopedRoleMembers
Request body
{}
```

## <a name="next-steps"></a>Další kroky

- [Použití skupin cloudů ke správě přiřazení rolí](roles-groups-concept.md)
- [Řešení potíží s rolemi přiřazenými ke cloudovým skupinám](roles-groups-faq-troubleshooting.md)
