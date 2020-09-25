---
title: Přidání, odebrání a výpis skupin v jednotce pro správu – Azure Active Directory | Microsoft Docs
description: Správa skupin a jejich oprávnění role v jednotce pro správu v Azure Active Directory
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
ms.openlocfilehash: 324d0ff0659270c6f2e90c0456ded83344f73936
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91264993"
---
# <a name="add-and-manage-groups-in-administrative-units-in-azure-active-directory"></a>Přidávání a Správa skupin v jednotkách pro správu v Azure Active Directory

V Azure Active Directory (Azure AD) můžete přidat skupiny do jednotky pro správu (AU), kde najdete přesnější administrativní rozsah řízení.

Postup přípravy na používání PowerShellu a Microsoft Graph pro správu jednotek správy najdete v tématu [Začínáme](roles-admin-units-manage.md#get-started).

## <a name="add-groups-to-an-au"></a>Přidání skupin do AU

### <a name="azure-portal"></a>portál Azure

Skupiny můžete přiřadit pouze jednotlivě do jednotky pro správu. Neexistuje možnost hromadného přiřazování skupin do jednotky pro správu. Skupinu můžete přiřadit jednotce pro správu jedním ze dvou způsobů na portálu:

1. Na stránce **skupin Azure AD >**

    Otevřete stránku Přehled skupin v Azure AD a vyberte skupinu, která musí být přiřazená k jednotce pro správu. Na levé straně vyberte **jednotky pro správu** a uveďte jednotky pro správu, ke kterým je skupina přiřazená. V horní části najdete možnost přiřazení k jednotce pro správu a kliknutím na ni získáte panel na pravé straně, kde si vyberete jednotku správy.

    ![přiřazení skupiny jednotlivě k jednotce pro správu](./media/roles-admin-units-add-manage-groups/assign-to-group-1.png)

1. Z **administrativních jednotek > služby Azure AD > všechny skupiny**

    Otevřete okno všechny skupiny ve službě Azure AD > jednotky pro správu. Pokud jsou už skupiny přiřazené k jednotce pro správu, zobrazí se na pravé straně. V horní části vyberte **Přidat** a v pravém panelu se zobrazí seznam skupin dostupných ve vaší organizaci Azure AD. Vyberte jednu nebo více skupin, které chcete přiřadit k administrativním jednotkám.

    ![Vyberte jednotku pro správu a pak vyberte Přidat člena.](./media/roles-admin-units-add-manage-groups/assign-to-admin-unit.png)

### <a name="powershell"></a>PowerShell

```powershell
$administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$GroupObj = Get-AzureADGroup -Filter "displayname eq 'TestGroup'"
Add-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId -RefObjectId $GroupObj.ObjectId
```

V tomto příkladu rutina Add-AzureADAdministrativeUnitMember slouží k přidání skupiny do jednotky pro správu. ID objektu jednotky pro správu a ID objektu skupiny, která se má přidat, se považují za argument. Zvýrazněný oddíl se může změnit podle potřeby pro konkrétní prostředí.

### <a name="microsoft-graph"></a>Microsoft Graph

```http
Http request
POST /administrativeUnits/{Admin Unit id}/members/$ref

Request body
{
"@odata.id":"https://graph.microsoft.com/beta/groups/{id}"
}
```

Příklad:

```http
{
"@odata.id":"https://graph.microsoft.com/beta/groups/ 871d21ab-6b4e-4d56-b257-ba27827628f3"
}
```

## <a name="list-groups-in-an-au"></a>Seznam skupin v AU

### <a name="azure-portal"></a>portál Azure

Na portálu můžete přejít na **jednotky pro správu Azure AD >** . Vyberte jednotku pro správu, pro kterou chcete zobrazit seznam uživatelů. Ve výchozím nastavení je na levém panelu vybraná možnost **Všichni uživatelé** . Vyberte **všechny skupiny** a napravo se seznam skupin, které jsou členy vybrané jednotky pro správu.

![Seznam skupin v jednotce pro správu](./media/roles-admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

```powershell
$administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
Get-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId
```

To vám pomůže získat všechny členy jednotky pro správu. Pokud chcete zobrazit všechny skupiny, které jsou členy jednotky pro správu, můžete použít následující fragment kódu:

```http
foreach ($member in (Get-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId)) 
{
if($member.ObjectType -eq "Group")
{
Get-AzureADGroup -ObjectId $member.ObjectId
}
}
```
### <a name="microsoft-graph"></a>Microsoft Graph

```http
HTTP request
GET /administrativeUnits/{Admin id}/members/$/microsoft.graph.group
Request body
{}
```

## <a name="list-aus-for-a-group"></a>Seznam jednotek Austrálie pro skupinu

### <a name="azure-portal"></a>portál Azure

Na portálu Azure AD můžete otevřít podrobnosti skupiny otevřením **skupin**. Výběrem skupiny otevřete profil skupiny. Vyberte **jednotky pro správu** a seznam všech jednotek pro správu, ve kterých je skupina členem.

![Výpis jednotek pro správu pro skupinu](./media/roles-admin-units-add-manage-groups/list-group-au.png)

### <a name="powershell"></a>PowerShell

```powershell
Get-AzureADAdministrativeUnit | where { Get-AzureADAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $groupObjId} }
```

### <a name="microsoft-graph"></a>Microsoft Graph

```http
https://graph.microsoft.com/beta/groups/<group-id>/memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-group-from-an-au"></a>Odebrání skupiny z AU

### <a name="azure-portal"></a>portál Azure

Existují dva způsoby, jak skupinu odebrat z jednotky pro správu v Azure Portal.

Otevřete **skupiny Azure AD**  >  **Groups** a otevřete profil pro skupinu, kterou chcete odebrat z jednotky pro správu. Vyberte **jednotky pro správu** na levém panelu a seznam všech jednotek správy, v nichž je skupina členem. Vyberte jednotku správy, ze které chcete skupinu odebrat, a pak vyberte **Odebrat z jednotky pro správu**.

![Odebrání skupiny z jednotky pro správu](./media/roles-admin-units-add-manage-groups/group-au-remove.png)

Případně můžete přejít na **Azure AD**  >  **jednotky pro správu** Azure AD a vybrat jednotku správy, kde je skupina členem. Vyberte **skupiny** na levém panelu a seznamte se se skupinami členů. Vyberte skupinu, která se má odebrat z jednotky pro správu, a pak vyberte **odebrat skupiny**.

![Seznam skupin v jednotce pro správu](./media/roles-admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

```powershell
Remove-AzureADAdministrativeUnitMember -ObjectId $auId -MemberId $memberGroupObjId
```

### <a name="microsoft-graph"></a>Microsoft Graph

```http
https://graph.microsoft.com/beta/administrativeUnits/<adminunit-id>/members/<group-id>/$ref
```

## <a name="next-steps"></a>Další kroky

- [Přiřazení role k jednotce pro správu](roles-admin-units-assign-roles.md)
- [Správa uživatelů v jednotce pro správu](roles-admin-units-add-manage-users.md)
