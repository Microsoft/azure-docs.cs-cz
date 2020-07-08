---
title: Přiřazení a výpis rolí s rozsahem jednotky pro správu (Preview) – Azure Active Directory | Microsoft Docs
description: Omezení rozsahu přiřazování rolí v Azure Active Directory pomocí jednotek pro správu
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 578fb481ec858e65ede49bdce2d8bc26470aa2ca
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85850762"
---
# <a name="assign-scoped-roles-to-an-administrative-unit"></a>Přiřazení vymezených rolí k jednotce pro správu

V Azure Active Directory (Azure AD) můžete přiřadit uživatele k roli Azure AD s oborem omezeným na jednu nebo více jednotek pro správu (Austrálie) pro přesnější administrativní řízení.

Postup přípravy na používání PowerShellu a Microsoft Graph pro správu jednotek správy najdete v tématu [Začínáme](roles-admin-units-manage.md#get-started).

## <a name="roles-available"></a>Role k dispozici

Role  |  Description
----- |  -----------
Správce ověřování  |  Má přístup k informacím o způsobu ověřování, nastavení a resetování pro libovolného uživatele bez oprávnění správce v přiřazené jednotce pro správu.
Správce skupin  |  Může spravovat všechny aspekty nastavení skupin a skupin, jako jsou zásady pojmenování a vypršení platnosti pouze v přiřazené jednotce pro správu.
Správce helpdesku  |  Může resetovat hesla správců bez oprávnění správce a helpdesku pouze v přiřazené jednotce pro správu.
Správce licencí  |  Může přiřazovat, odebírat a aktualizovat přiřazení licencí pouze v rámci jednotky pro správu.
Správce hesel  |  Může resetovat hesla správců, kteří nejsou správci a hesla, jenom v rámci přiřazené jednotky pro správu.
Správce uživatele  |  Může spravovat všechny aspekty uživatelů a skupin, včetně resetování hesel pro omezené správce v rámci přiřazené jednotky pro správu.

## <a name="assign-a-scoped-role"></a>Přiřazení vymezené role

### <a name="azure-portal"></a>portál Azure

Na portálu můžete přejít na **jednotky pro správu Azure AD >** . Vyberte jednotku správy, ke které chcete přiřadit roli uživateli. V levém podokně vyberte role a správci a seznamte se se všemi dostupnými rolemi.

![Výběr jednotky pro správu pro změnu rozsahu role](./media/roles-admin-units-assign-roles/select-role-to-scope.png)

Vyberte roli, kterou chcete přiřadit, a pak vyberte **Přidat přiřazení**. Tím se otevře panel na pravé straně, kde můžete vybrat jednoho nebo více uživatelů, kteří mají být přiřazeni k roli.

![Vyberte roli pro obor a pak vyberte přidat přiřazení.](./media/roles-admin-units-assign-roles/select-add-assignment.png)

### <a name="powershell"></a>PowerShell

```powershell
$administrative = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$AdminUser = Get-AzureADUser -ObjectId 'janedoe@fabidentity.onmicrosoft.com'
$uaRoleMemberInfo = New-Object -TypeName Microsoft.Open.AzureAD.Model.RoleMemberInfo -Property @{ObjectId = $AdminUser.ObjectId}
Add-AzureADScopedRoleMembership -RoleObjectId $UserAdminRole.ObjectId -ObjectId $administrative unitObj.ObjectId -RoleMemberInfo  $uaRoleMemberInfo
```

Zvýrazněný oddíl se může změnit podle potřeby pro konkrétní prostředí.

### <a name="microsoft-graph"></a>Microsoft Graph

```http
Http request
POST /administrativeUnits/{id}/scopedRoleMembers
    
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
$administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
Get-AzureADScopedRoleMembership -ObjectId $administrative unitObj.ObjectId | fl *
```

Zvýrazněný oddíl se může změnit podle potřeby pro konkrétní prostředí.

### <a name="microsoft-graph"></a>Microsoft Graph

```http
Http request
GET /administrativeUnits/{id}/scopedRoleMembers
Request body
{}
```

## <a name="next-steps"></a>Další kroky

- [Řešení potíží s jednotkami pro správu a nejčastější dotazy](roles-admin-units-faq-troubleshoot.md)
