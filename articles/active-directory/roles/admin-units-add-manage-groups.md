---
title: Přidání, odebrání a výpis skupin v jednotce pro správu – Azure Active Directory | Microsoft Docs
description: Spravujte skupiny a jejich oprávnění role v jednotce pro správu v Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 11/04/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 092604429cf5a527d7ee62b412e879ad9991eace
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2020
ms.locfileid: "93394743"
---
# <a name="add-and-manage-groups-in-an-administrative-unit-in-azure-active-directory"></a>Přidávání a Správa skupin v jednotce pro správu v Azure Active Directory

V Azure Active Directory (Azure AD) můžete přidat skupiny do jednotky pro správu a podrobnějšího administrativního rozsahu řízení.

Informace o přípravě na používání PowerShellu a Microsoft Graph pro správu jednotek správy najdete v [tématu Začínáme.](admin-units-manage.md#get-started)

## <a name="add-groups-to-an-administrative-unit"></a>Přidání skupin do jednotky pro správu

Skupiny můžete přidat do jednotky pro správu pomocí Azure Portal, PowerShellu nebo Microsoft Graph.

### <a name="use-the-azure-portal"></a>Použití webu Azure Portal

Jednotce pro správu můžete přiřadit pouze jednotlivé skupiny. Neexistuje možnost přiřadit skupiny jako hromadnou operaci. V Azure Portal můžete přiřadit skupinu k jednotce pro správu jedním ze dvou způsobů:

* Z podokna **skupiny** :

  1. V Azure Portal přejdete do **služby Azure AD**.
  1. Vyberte **skupiny** a pak vyberte skupinu, kterou chcete přiřadit k jednotce pro správu. 
  1. V levém podokně vyberte **jednotky pro správu** a zobrazte seznam jednotek pro správu, ke kterým je skupina přiřazená. 

     ![Snímek obrazovky s odkazem "přiřadit k jednotce správy" v podokně "jednotky pro správu".](./media/admin-units-add-manage-groups/assign-to-group-1.png)

  1. Vyberte **přiřadit k jednotce pro správu**.
  1. V pravém podokně vyberte jednotku pro správu.

* V podokně všechny skupiny **jednotek pro správu**  >  **All Groups** :

  1. V Azure Portal přejdete do **služby Azure AD**.
  
  1. V levém podokně vyberte **jednotky pro správu** a pak vyberte **všechny skupiny**. 
     V pravém podokně se zobrazí všechny skupiny, které jsou už přiřazené k jednotce pro správu. 

  1. V podokně **skupiny** vyberte **Přidat**.
    V pravém podokně se zobrazí všechny dostupné skupiny v organizaci Azure AD. 

     ![Snímek obrazovky s tlačítkem Přidat pro přidání skupiny do jednotky pro správu](./media/admin-units-add-manage-groups/assign-to-admin-unit.png)

  1. Vyberte jednu nebo více skupin, které chcete přiřadit k jednotce pro správu, a pak vyberte tlačítko **Vybrat** .

### <a name="use-powershell"></a>Použití prostředí PowerShell

V následujícím příkladu `Add-AzureADMSAdministrativeUnitMember` přidejte skupinu do jednotky pro správu pomocí rutiny. ID objektu jednotky pro správu a ID objektu skupiny, která se má přidat, se považují za argumenty. Změňte zvýrazněný oddíl podle potřeby pro konkrétní prostředí.


```powershell
$administrative unitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$GroupObj = Get-AzureADGroup -Filter "displayname eq 'TestGroup'"
Add-AzureADMSAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId -RefObjectId $GroupObj.ObjectId
```

### <a name="use-microsoft-graph"></a>Použít Microsoft Graph

Spusťte následující příkazy:

```http
Http request
POST /administrativeUnits/{Admin Unit id}/members/$ref

Request body
{
"@odata.id":"https://graph.microsoft.com/v1.0/groups/{id}"
}
```

Příklad:

```http
{
"@odata.id":"https://graph.microsoft.com/v1.0/groups/ 871d21ab-6b4e-4d56-b257-ba27827628f3"
}
```

## <a name="view-a-list-of-groups-in-an-administrative-unit"></a>Zobrazení seznamu skupin v jednotce pro správu

### <a name="use-the-azure-portal"></a>Použití webu Azure Portal

1. V Azure Portal přejdete do **služby Azure AD**.

1. V levém podokně vyberte **jednotky pro správu** a potom vyberte jednotku pro správu, jejíž skupiny chcete zobrazit. Ve výchozím nastavení se v levém podokně vybere možnost **Všichni uživatelé** . 

1. V levém podokně vyberte **skupiny**. V pravém podokně se zobrazí seznam skupin, které jsou členy vybrané jednotky pro správu.

   ![Snímek obrazovky s podoknem skupiny zobrazující seznam skupin v jednotce pro správu](./media/admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="use-powershell"></a>Použití prostředí PowerShell

Chcete-li zobrazit seznam všech členů jednotky pro správu, spusťte následující příkaz: 

```powershell
$administrative unitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
Get-AzureADMSAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId
```

Chcete-li zobrazit všechny skupiny, které jsou členy jednotky pro správu, použijte následující fragment kódu:

```http
foreach ($member in (Get-AzureADMSAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId)) 
{
if($member.ObjectType -eq "Group")
{
Get-AzureADGroup -ObjectId $member.ObjectId
}
}
```

### <a name="use-microsoft-graph"></a>Použít Microsoft Graph

Spusťte následující příkaz:

```http
HTTP request
GET /directory/administrativeUnits/{Admin id}/members/$/microsoft.graph.group
Request body
{}
```

## <a name="view-a-list-of-administrative-units-for-a-group"></a>Zobrazit seznam jednotek pro správu pro skupinu

### <a name="use-the-azure-portal"></a>Použití webu Azure Portal

1. V Azure Portal přejdete do **služby Azure AD**.

1. V levém podokně vyberte **skupiny** a zobrazte seznam skupin.

1. Výběrem skupiny otevřete profil skupiny. 

1. V levém podokně vyberte **jednotky pro správu** a seznam všech jednotek pro správu, v nichž je skupina členem.

   ![Snímek obrazovky s podoknem "jednotky pro správu", ve kterém se zobrazí jednotky pro správu seznamu, ke kterým je skupina přiřazena](./media/admin-units-add-manage-groups/list-group-au.png)

### <a name="use-powershell"></a>Použití prostředí PowerShell

Spusťte následující příkaz:

```powershell
Get-AzureADMSAdministrativeUnit | where { Get-AzureADMSAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $groupObjId} }
```

### <a name="use-microsoft-graph"></a>Použít Microsoft Graph

Spusťte následující příkaz:

```http
https://graph.microsoft.com/v1.0/groups/<group-id>/memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-group-from-an-administrative-unit"></a>Odebrání skupiny z jednotky pro správu

### <a name="use-the-azure-portal"></a>Použití webu Azure Portal

Skupinu můžete z jednotky pro správu v Azure Portal odebrat jedním ze dvou způsobů:

- Odebrání z přehledu skupiny:

  1. V Azure Portal přejdete do **služby Azure AD**.
  1. V levém podokně vyberte **skupiny** a pak otevřete profil pro skupinu, kterou chcete odebrat z jednotky pro správu.
  1. V levém podokně vyberte **jednotky pro správu** a uveďte všechny jednotky pro správu, ke kterým je skupina přiřazená. 
  1. Vyberte jednotku správy, ze které chcete skupinu odebrat, a pak vyberte **Odebrat z jednotky pro správu**.

     ![Snímek obrazovky s podoknem "administrativní jednotky" a zobrazení seznamu skupin přiřazených vybrané jednotce pro správu.](./media/admin-units-add-manage-groups/group-au-remove.png)

- Odebrat z jednotky pro správu:

  1. V Azure Portal přejdete do **služby Azure AD**.
  1. V levém podokně vyberte **jednotky pro správu** a potom vyberte jednotku správy, ke které je skupina přiřazená.
  1. V levém podokně vyberte **skupiny** a uveďte všechny skupiny, které jsou přiřazené jednotce pro správu.
  1. Vyberte skupinu, kterou chcete odebrat, a pak vyberte **odebrat skupiny**.

    ![Snímek obrazovky s podoknem skupiny, kde se zobrazí seznam skupin v jednotce pro správu](./media/admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="use-powershell"></a>Použití prostředí PowerShell

Spusťte následující příkaz:

```powershell
Remove-AzureADMSAdministrativeUnitMember -ObjectId $auId -MemberId $memberGroupObjId
```

### <a name="use-microsoft-graph"></a>Použít Microsoft Graph

Spusťte následující příkaz:

```http
https://graph.microsoft.com/v1.0/directory/AdministrativeUnits/<adminunit-id>/members/<group-id>/$ref
```

## <a name="next-steps"></a>Další kroky

- [Přiřazení role k jednotce pro správu](admin-units-assign-roles.md)
- [Správa uživatelů v jednotce pro správu](admin-units-add-manage-users.md)
