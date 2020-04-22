---
title: Přidání, odebrání a zařazení uživatelů do administrativní jednotky (preview) – Azure Active Directory | Dokumenty společnosti Microsoft
description: Správa uživatelů a jejich oprávnění k rolím v administrativní jednotce ve službě Azure Active Directory
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
ms.openlocfilehash: 9c2c5c083115440e1e4da203f39f2b32734458c3
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684962"
---
# <a name="add-and-manage-users-in-an-administrative-unit-in-azure-active-directory"></a>Přidání a správa uživatelů v administrativní jednotce ve službě Azure Active Directory

Ve službě Azure Active Directory (Azure AD) můžete přidat uživatele do jednotky pro správu (AU) pro podrobnější rozsah správy řízení.

Postup přípravy na použití powershellu a microsoft graphu pro správu administrativních jednotek naleznete [v tématu Začínáme](roles-admin-units-manage.md#get-started).

## <a name="add-users-to-an-au"></a>Přidání uživatelů do AU

### <a name="azure-portal"></a>portál Azure

Uživatelům můžete přiřadit administrativní jednotky dvěma způsoby.

1. Individuální zadání

    1. Můžete přejít na Azure AD na portálu a vyberte uživatel a vyberte uživatele, který má být přiřazen k administrativní jednotce. V levém panelu pak můžete vybrat administrativní jednotky. Uživateli lze přiřadit jednu nebo více administrativních jednotek kliknutím na Přiřadit k administrativní jednotce a výběrem administrativních jednotek, kam má být uživatel přiřazen.

       ![Vyberte Přidat a zadejte název pro správní jednotku.](./media/roles-admin-units-add-manage-users/assign-users-individually.png)

    1. Můžete přejít na Azure AD na portálu a vybrat administrativní jednotky v levém podokně a pak vybrat administrativní jednotku, kde mají být uživatelé přiřazeni. V levém podokně vyberte Všichni uživatelé a pak vyberte Přidat člena. Potom můžete pokračovat a vybrat jednoho nebo více uživatelů, kteří mají být přiřazeni k administrativní jednotce z pravého podokna.

        ![vyberte administrativní jednotku a pak vyberte Přidat člena](./media/roles-admin-units-add-manage-users/assign-to-admin-unit.png)

1. Hromadné přiřazení

    Přejděte na Azure AD na portálu a vyberte jednotky pro správu. Vyberte administrativní jednotku, do které mají být uživatelé přidáni. Pokračujte kliknutím na všechny uživatele -> Přidat členy ze souboru .csv. Poté si můžete stáhnout šablonu CSV a upravit soubor. Formát je jednoduchý a potřebuje jeden hlavní název on-line, které mají být přidány do každého řádku. Jakmile je soubor připraven, uložte jej na vhodné místo a nahrajte jej v kroku 3, jak je zvýrazněno ve snímku.

    ![hromadné přiřazení uživatelů k administrativní jednotce](./media/roles-admin-units-add-manage-users/bulk-assign-to-admin-unit.png)

### <a name="powershell"></a>PowerShell

    $administrativeunitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    $UserObj = Get-AzureADUser -Filter "UserPrincipalName eq 'billjohn@fabidentity.onmicrosoft.com'"
    Add-AzureADAdministrativeUnitMember -ObjectId $administrativeunitObj.ObjectId -RefObjectId $UserObj.ObjectId

Ve výše uvedeném příkladu rutina Add-AzureADAdministrativeUnitMember se používá k přidání uživatele do jednotky pro správu. ID objektu správní jednotky, kde má být uživatel přidán, a ID objektu uživatele, který má být přidán, jsou považovány za argument. Zvýrazněná část může být změněna podle potřeby pro konkrétní prostředí.

### <a name="microsoft-graph"></a>Microsoft Graph

    Http request
    POST /administrativeUnits/{Admin Unit id}/members/$ref
    Request body
    {
      "@odata.id":"https://graph.microsoft.com/beta/users/{id}"
    }

Příklad:

    {
      "@odata.id":"https://graph.microsoft.com/beta/users/johndoe@fabidentity.com"
    }

## <a name="list-administrative-units-for-a-user"></a>Seznam jednotek pro správu pro uživatele

### <a name="azure-portal"></a>portál Azure

Na webu Azure Portal můžete otevřít profil uživatele tak, že přejdete na Azure AD > uživatelé. Kliknutím na uživatele otevřete profil uživatele.

![Otevření profilu uživatele ve službě Azure Active Directory](./media/roles-admin-units-add-manage-users/user-profile-admin-units.png)

Výběrem **položky Administrativní jednotky** na levém panelu zobrazíte seznam jednotek pro správu, kterým byl uživatel přiřazen.

![Seznam jednotek pro správu pro uživatele](./media/roles-admin-units-add-manage-users/list-user-admin-units.png)

### <a name="powershell"></a>PowerShell

    Get-AzureADAdministrativeUnit | where { Get-AzureADAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $userObjId} }

### <a name="microsoft-graph"></a>Microsoft Graph

    https://graph.microsoft.com/beta/users//memberOf/$/Microsoft.Graph.AdministrativeUnit

## <a name="remove-a-single-user-from-an-au"></a>Odebrání jednoho uživatele z AU

### <a name="azure-portal"></a>portál Azure

Uživatele můžete odebrat z administrativní jednotky dvěma způsoby. Na webu Azure Portal můžete otevřít profil uživatele tak, že přejdete na Azure **AD** > **Users**. Vyberte uživatele, chcete-li otevřít profil uživatele. Vyberte administrativní jednotku, ze které má být uživatel odebrán, a vyberte **odebrat z administrativní jednotky**.

![Odebrání uživatele z administrativní jednotky z profilu uživatele](./media/roles-admin-units-add-manage-users/user-remove-admin-units.png)

Uživatele v**jednotkách pro správu Služby** **Azure AD** > můžete také odebrat výběrem administrativní jednotky, ze které chcete uživatele odebrat. Vyberte uživatele a vyberte **Odebrat člena**.
  
![Odebrání uživatele na úrovni správní jednotky](./media/roles-admin-units-add-manage-users/admin-units-remove-user.png)

### <a name="powershell"></a>PowerShell

    Remove-AzureADAdministrativeUnitMember -ObjectId $auId -MemberId $memberUserObjId

### <a name="microsoft-graph"></a>Microsoft Graph

   https://graph.microsoft.com/beta/administrativeUnits/<adminunit-id>/members/<user-id>/$ref

## <a name="bulk-remove-more-than-one-user"></a>Hromadné odebrání více než jednoho uživatele

Můžete přejít na Azure AD > jednotky pro správu a vybrat administrativní jednotku, ze které chcete odebrat uživatele. Klikněte na Hromadné odebrat člena. Stáhněte si šablonu CSV pro poskytnutí seznamu uživatelů, kteří mají být odebráni.

Upravte staženou šablonu CSV s příslušnými uživatelskými položkami. Neodstraňujte první dva řádky šablony. Do každého řádku přidejte jeden uživatelský upn.

![Úprava souboru CSV pro hromadné odebrání uživatele z jednotek pro správu](./media/roles-admin-units-add-manage-users/bulk-user-entries.png)

Po uložení položek v souboru nahrajte soubor a vyberte **Odeslat**.

![Odeslání souboru hromadného nahrání](./media/roles-admin-units-add-manage-users/bulk-user-remove.png)

## <a name="next-steps"></a>Další kroky

- [Přiřazení role správní jednotce](roles-admin-units-assign-roles.md)
- [Přidání skupin do administrativní jednotky](roles-admin-units-add-manage-groups.md)
