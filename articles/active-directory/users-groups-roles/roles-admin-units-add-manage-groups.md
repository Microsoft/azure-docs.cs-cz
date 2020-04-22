---
title: Přidání, odebrání a zařazení skupin do administrativní jednotky (preview) – Azure Active Directory | Dokumenty společnosti Microsoft
description: Správa skupin a jejich oprávnění k rolím v administrativní jednotce ve službě Azure Active Directory
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
ms.openlocfilehash: 1f0b0c0a7b666aed56ad24625c80c0a57683b998
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683259"
---
# <a name="add-and-manage-groups-in-administrative-units-in-azure-active-directory"></a>Přidání a správa skupin v administrativních jednotkách ve službě Azure Active Directory

Ve službě Azure Active Directory (Azure AD) můžete přidat skupiny do jednotky pro správu (AU) pro podrobnější rozsah správy řízení.

Postup přípravy na použití powershellu a microsoft graphu pro správu administrativních jednotek naleznete [v tématu Začínáme](roles-admin-units-manage.md#get-started).

## <a name="add-groups-to-an-au"></a>Přidání skupin do AU

### <a name="azure-portal"></a>portál Azure

Ve verzi Preview můžete přiřadit skupiny pouze jednotlivě k administrativní jednotce. Neexistuje žádná možnost hromadného přiřazení skupin do správní jednotky. Skupinu můžete přiřadit k administrativní jednotce jedním ze dvou způsobů na portálu:

1. Na stránce **skupiny > Azure AD**

    Otevřete stránku Přehled skupin ve službě Azure AD a vyberte skupinu, která se má přiřadit k administrativní jednotce. Na levé straně vyberte **administrativní jednotky,** chcete-li vypsat administrativní jednotky, ke kterým je skupina přiřazena. Na vrcholu najdete možnost Přiřadit k administrativní jednotce a kliknutím na něj dá panel na pravé straně vybrat správní jednotku.

    ![přiřazení skupiny jednotlivě správní jednotce](./media/roles-admin-units-add-manage-groups/assign-to-group-1.png)

1. Ze stránky **Azure AD > pro správu > všech skupin**

    Otevřete okno Všechny skupiny ve službě Azure AD > jednotek pro správu. Pokud jsou již k administrativní jednotce přiřazeny skupiny, zobrazí se na pravé straně. Vyberte **Přidat** nahoře a pravý panel se posune v seznamu skupin dostupných ve vaší organizaci Azure AD. Vyberte jednu nebo více skupin, které mají být přiřazeny k jednotkám pro správu.

    ![vyberte administrativní jednotku a pak vyberte Přidat člena](./media/roles-admin-units-add-manage-groups/assign-to-admin-unit.png)

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    $GroupObj = Get-AzureADGroup -Filter "displayname eq 'TestGroup'"
    Add-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId -RefObjectId $GroupObj.ObjectId

V tomto příkladu rutina Add-AzureADAdministrativeUnitMember se používá k přidání skupiny do jednotky pro správu. ID objektu správní jednotky a ID objektu skupiny, která má být přidána, jsou považovány za argument. Zvýrazněná část může být změněna podle potřeby pro konkrétní prostředí.

### <a name="microsoft-graph"></a>Microsoft Graph

    Http request
    POST /administrativeUnits/{Admin Unit id}/members/$ref

    Request body
    {
      "@odata.id":"https://graph.microsoft.com/beta/groups/{id}"
    }

Příklad:

    {
      "@odata.id":"https://graph.microsoft.com/beta/users/ 871d21ab-6b4e-4d56-b257-ba27827628f3"
    }

## <a name="list-groups-in-an-au"></a>Seznam skupin v AU

### <a name="azure-portal"></a>portál Azure

Přejděte na **Azure AD > jednotky pro správu** na portálu. Vyberte administrativní jednotku, pro kterou chcete vypsat seznam uživatelů. Ve výchozím nastavení jsou **všichni uživatelé** vybráni již na levém panelu. Vyberte **Všechny skupiny** a vpravo najdete seznam skupin, které jsou členy vybrané administrativní jednotky.

![Vyberte administrativní jednotku, která má být odstraněna.](./media/roles-admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    Get-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId

To vám pomůže získat všechny členy správní jednotky. Pokud chcete zobrazit všechny skupiny, které jsou členy administrativní jednotky, můžete použít níže uvedený fragment kódu:

    foreach ($member in (Get-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId)) 
    {
    if($member.ObjectType -eq "Group")
    {
    Get-AzureADGroup -ObjectId $member.ObjectId
    }
    }

### <a name="microsoft-graph"></a>Microsoft Graph

    HTTP request
    GET /administrativeUnits/{Admin id}/members/$/microsoft.graph.group
    Request body
    {}

## <a name="list-aus-for-a-group"></a>Seznam au pro skupinu

### <a name="azure-portal"></a>portál Azure

Na portálu Azure AD můžete otevřít podrobnosti o skupině otevřením **skupiny**. Vyberte skupinu, chcete-li otevřít profil skupiny. Výběrem **položky Administrativní jednotky** zobrazíte seznam všech jednotek pro správu, ve kterých je skupina členem.

![Vypsat administrativní jednotky pro skupinu](./media/roles-admin-units-add-manage-groups/list-group-au.png)

### <a name="powershell"></a>PowerShell

    Get-AzureADAdministrativeUnit | where { Get-AzureADAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $groupObjId} }

### <a name="microsoft-graph"></a>Microsoft Graph

    https://graph.microsoft.com/beta/groups/<group-id>/memberOf/$/Microsoft.Graph.AdministrativeUnit

## <a name="remove-a-group-from-an-au"></a>Odebrání skupiny z AU

### <a name="azure-portal"></a>portál Azure

Existují dva způsoby, jak můžete odebrat skupinu z jednotky pro správu na webu Azure Portal.

Otevřete**skupiny** **Azure AD** > a otevřete profil pro skupinu, kterou chcete odebrat z jednotky pro správu. Výběrem **položky Administrativní jednotky** v levém panelu zobrazíte seznam všech jednotek pro správu, ve kterých je skupina členem. Vyberte administrativní jednotku, ze které chcete skupinu odebrat, a pak vyberte **Odebrat z administrativní jednotky**.

![Odebrání skupiny ze správní jednotky](./media/roles-admin-units-add-manage-groups/group-au-remove.png)

Případně můžete přejít na**jednotky pro správu Služby** **Azure AD** > a vybrat administrativní jednotku, kde je skupina členem. Výběrem **možnosti Skupiny** v levém panelu zobrazíte seznam skupin členů. Vyberte skupinu, která má být odebrána z jednotky správce, a pak vyberte **Odebrat skupiny**.

![Seznam skupin ve správní jednotce](./media/roles-admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

    Remove-AzureADAdministrativeUnitMember -ObjectId $auId -MemberId $memberGroupObjId

### <a name="microsoft-graph"></a>Microsoft Graph

    https://graph.microsoft.com/beta/administrativeUnits/<adminunit-id>/members/<group-id>/$ref

## <a name="next-steps"></a>Další kroky

- [Přiřazení role správní jednotce](roles-admin-units-assign-roles.md)
- [Správa uživatelů ve správní jednotce](roles-admin-units-add-manage-users.md)
