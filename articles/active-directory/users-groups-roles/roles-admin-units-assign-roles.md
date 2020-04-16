---
title: Přiřazení a seznam rolí s oborem administrativní jednotky (preview) – Služba Azure Active Directory | Dokumenty společnosti Microsoft
description: Použití jednotek pro správu k omezení rozsahu přiřazení rolí ve službě Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: a518ff070ea021726ee382169e87cca55cad36d0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428248"
---
# <a name="assign-scoped-roles-to-an-administrative-unit"></a>Přiřazení rolí s vymezeným oborem k administrativní jednotce

Ve službě Azure Active Directory (Azure AD) můžete přiřadit uživatele k roli Azure AD s oborem omezeným na jednu nebo více jednotek pro správu (AU) pro podrobnější řízení správy.

Postup přípravy na použití powershellu a microsoft graphu pro správu administrativních jednotek naleznete [v tématu Začínáme](roles-admin-units-manage.md#getting-started).

## <a name="roles-available"></a>Dostupné role

Role  |  Popis
----- |  -----------
Správce ověřování  |  Má přístup k zobrazení, nastavení a obnovení informací o metodě ověřování pro všechny uživatele, kteří nejsou správci, pouze v přiřazené jednotce pro správu.
Správce skupin  |  Může spravovat všechny aspekty nastavení skupin a skupin, jako je pojmenování a vypršení platnosti zásady v přiřazené administrativní jednotky pouze.
Správce technické podpory  |  Můžete resetovat hesla pro jiné než správce a správce technické podpory pouze v přiřazené administrativní jednotce.
Správce licencí  |  Můžete přiřazovat, odebírat a aktualizovat přiřazení licencí pouze v rámci administrativní jednotky.
Správce hesel  |  Můžete resetovat hesla pro jiné než správce a správce hesel pouze v rámci přiřazené administrativní jednotky.
Správce uživatelů  |  Může spravovat všechny aspekty uživatelů a skupin, včetně resetování hesel pro omezené správce pouze v rámci přiřazené administrativní jednotky.

## <a name="assign-a-scoped-role"></a>Přiřazení role s vymezeným oborem

### <a name="azure-portal"></a>portál Azure

Přejděte na **Azure AD > jednotky pro správu** na portálu. Vyberte administrativní jednotku, ke které chcete přiřadit roli uživateli. V levém podokně vyberte Role a správce, chcete-li vypsat všechny dostupné role.

![Vyberte administrativní jednotku, která má změnit obor role.](./media/roles-admin-units-assign-roles/select-role-to-scope.png)

Vyberte roli, která má být přiřazena, a pak vyberte **Přidat přiřazení**. Tím se otevře panel vpravo, kde můžete vybrat jednoho nebo více uživatelů, kteří mají být přiřazeni k roli.

![Vyberte roli, která má být obora a pak vyberte Přidat přiřazení](./media/roles-admin-units-assign-roles/select-add-assignment.png)

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    $AdminUser = Get-AzureADUser -ObjectId 'janedoe@fabidentity.onmicrosoft.com'
    $uaRoleMemberInfo = New-Object -TypeName Microsoft.Open.AzureAD.Model.RoleMemberInfo -Property @{ObjectId = $AdminUser.ObjectId}
    Add-AzureADScopedRoleMembership -RoleObjectId $UserAdminRole.ObjectId -ObjectId $administrative unitObj.ObjectId -RoleMemberInfo  $uaRoleMemberInfo

Zvýrazněná část může být změněna podle potřeby pro konkrétní prostředí.

### <a name="microsoft-graph"></a>Microsoft Graph

    Http request
    POST /administrativeUnits/{id}/scopedRoleMembers
    
    Request body
    {
      "roleId": "roleId-value",
      "roleMemberInfo": {
        "id": "id-value"
      }
    }

## <a name="list-the-scoped-admins-on-an-au"></a>Seznam správců s vymezeným oborem na AU

### <a name="azure-portal"></a>portál Azure

Všechna přiřazení rolí provedená s oborem jednotky pro správu lze zobrazit v [části Jednotky pro správu ve službě Azure AD](https://ms.portal.azure.com/?microsoft_aad_iam_adminunitprivatepreview=true&microsoft_aad_iam_rbacv2=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AdminUnit). Přejděte na **Azure AD > jednotky pro správu** na portálu. Vyberte jednotku správce pro přiřazení rolí, která chcete uvést. Vyberte **Role a správce** a otevřete roli pro zobrazení přiřazení v jednotce správce.

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    Get-AzureADScopedRoleMembership -ObjectId $administrative unitObj.ObjectId | fl *

Zvýrazněná část může být změněna podle potřeby pro konkrétní prostředí.

### <a name="microsoft-graph"></a>Microsoft Graph

    Http request
    GET /administrativeUnits/{id}/scopedRoleMembers
    Request body
    {}

## <a name="next-steps"></a>Další kroky

- [Oddělení pro správu pro řešení problémů a nejčastější dotazy](roles-admin-units-faq-troubleshoot.md)
