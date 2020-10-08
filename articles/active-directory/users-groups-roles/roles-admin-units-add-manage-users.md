---
title: Přidání, odebrání a výpis uživatelů v jednotce pro správu – Azure Active Directory | Microsoft Docs
description: Správa uživatelů a jejich oprávnění role v jednotce pro správu v Azure Active Directory
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
ms.openlocfilehash: 0d29f4ef5806eb8ed9385696dea78f4ae0992b93
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2020
ms.locfileid: "91818249"
---
# <a name="add-and-manage-users-in-an-administrative-unit-in-azure-active-directory"></a>Přidat a spravovat uživatele v jednotce pro správu v Azure Active Directory

V Azure Active Directory (Azure AD) můžete přidat uživatele do jednotky pro správu (AU) pro přesnější administrativní rozsah řízení.

Postup přípravy na používání PowerShellu a Microsoft Graph pro správu jednotek správy najdete v tématu [Začínáme](roles-admin-units-manage.md#get-started).

## <a name="add-users-to-an-au"></a>Přidat uživatele do AU

### <a name="azure-portal"></a>portál Azure

Můžete přiřadit uživatele k jednotkám pro správu jednotlivě nebo hromadnou operací.

- Individuální přiřazení z profilu uživatele

   1. Přihlaste se k [centru pro správu Azure AD](https://portal.azure.com) pomocí oprávnění správce privilegovaných rolí.
   1. Vyberte možnost **Uživatelé** a vyberte uživatele, který má být přiřazen k jednotce pro správu, aby bylo možné otevřít profil uživatele.
   1. Vyberte **jednotky pro správu**. Uživatel může být přiřazen k jedné nebo více jednotkám správy výběrem možnosti **přiřadit k jednotce pro správu** a výběrem jednotek pro správu, ve kterých má být uživatel přiřazen.

       ![Vyberte Přidat a potom zadejte název jednotky pro správu.](./media/roles-admin-units-add-manage-users/assign-users-individually.png)

- Individuální přiřazení z jednotky pro správu

   1. Přihlaste se k [centru pro správu Azure AD](https://portal.azure.com) pomocí oprávnění správce privilegovaných rolí.
   1. Vyberte **jednotky pro správu** a potom vyberte jednotku pro správu, do které mají být uživatelé přiřazeni.
   1. Vyberte **Všichni uživatelé** a pak vyberte **Přidat člena** a vyberte jednoho nebo víc uživatelů, které chcete přiřadit k jednotce pro správu v podokně **Přidat člena** .

        ![Vyberte jednotku pro správu a pak vyberte Přidat člena.](./media/roles-admin-units-add-manage-users/assign-to-admin-unit.png)

- Hromadné přiřazení

   1. Přihlaste se k [centru pro správu Azure AD](https://portal.azure.com) pomocí oprávnění správce privilegovaných rolí.
   1. Vyberte **jednotky pro správu**.
   1. Vyberte jednotku pro správu, do které chcete přidat uživatele.
   1. Otevře **všechny uživatele**  >  **přidat členy ze souboru. csv**. Pak můžete stáhnout šablonu hodnot oddělených čárkami (CSV) a soubor upravit. Formát je jednoduchý a vyžaduje, aby se do každého řádku přidal jeden hlavní název uživatele. Jakmile je soubor připravený, uložte ho na vhodné místo a pak ho nahrajte jako součást tohoto kroku.

    ![hromadně přiřazovat uživatele k jednotce pro správu](./media/roles-admin-units-add-manage-users/bulk-assign-to-admin-unit.png)

### <a name="powershell"></a>PowerShell

```powershell
$administrativeunitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$UserObj = Get-AzureADUser -Filter "UserPrincipalName eq 'billjohn@fabidentity.onmicrosoft.com'"
Add-AzureADMSAdministrativeUnitMember -Id $administrativeunitObj.ObjectId -RefObjectId $UserObj.ObjectId
```

V předchozím příkladu rutina Add-AzureADAdministrativeUnitMember slouží k přidání uživatele do jednotky pro správu. ID objektu jednotky pro správu, do které se má přidat uživatel a ID objektu uživatele, který se má přidat, se považuje za argument. Zvýrazněný oddíl se může změnit podle potřeby pro konkrétní prostředí.

### <a name="microsoft-graph"></a>Microsoft Graph

```http
Http request
POST /administrativeUnits/{Admin Unit id}/members/$ref
Request body
{
  "@odata.id":"https://graph.microsoft.com/v1.0/users/{id}"
}
```

Příklad:

```http
{
  "@odata.id":"https://graph.microsoft.com/v1.0/users/johndoe@fabidentity.com"
}
```

## <a name="list-administrative-units-for-a-user"></a>Seznam jednotek pro správu pro uživatele

### <a name="azure-portal"></a>portál Azure

V Azure Portal můžete otevřít profil uživatele:

1. Otevírají se uživatelé **Azure AD**  >  **Users**.

1. Výběrem uživatele otevřete profil uživatele.

1. Vyberte **jednotky pro správu** a zobrazí se seznam jednotek pro správu, ke kterým byl uživatel přiřazen.

   ![Seznam jednotek pro správu pro uživatele](./media/roles-admin-units-add-manage-users/list-user-admin-units.png)

### <a name="powershell"></a>PowerShell

```powershell
Get-AzureADMSAdministrativeUnit | where { Get-AzureADMSAdministrativeUnitMember -Id $_.ObjectId | where {$_.RefObjectId -eq $userObjId} }
```
Poznámka: ve výchozím nastavení Get-AzureADAdministrativeUnitMember vrací pouze členy 100, můžete přidat "-All $true" pro načtení dalších členů.

### <a name="microsoft-graph"></a>Microsoft Graph

```http
https://graph.microsoft.com/v1.0/users/{id}/memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-single-user-from-an-au"></a>Odebrání jednoho uživatele z AU

### <a name="azure-portal"></a>portál Azure

Existují dva způsoby, jak můžete odebrat uživatele z jednotky pro správu. V Azure Portal můžete otevřít profil uživatele tak, že kliknete na uživatelé **Azure AD**  >  **Users**. Výběrem uživatele otevřete profil uživatele. Vyberte jednotku pro správu, ze které chcete uživatele odebrat, a vyberte možnost **Odebrat z jednotky pro správu**.

![Odebrání uživatele z jednotky pro správu z profilu uživatele](./media/roles-admin-units-add-manage-users/user-remove-admin-units.png)

Uživatele můžete také odebrat v části **Azure AD**  >  **jednotky pro správu** Azure AD tak, že vyberete jednotku správy, ze které chcete odebrat uživatele. Vyberte uživatele a vyberte **odebrat člena**.
  
![Odebrání uživatele na úrovni jednotky pro správu](./media/roles-admin-units-add-manage-users/admin-units-remove-user.png)

### <a name="powershell"></a>PowerShell

```powershell
Remove-AzureADMSAdministrativeUnitMember -Id $auId -MemberId $memberUserObjId
```

### <a name="microsoft-graph"></a>Microsoft Graph

   https://graph.microsoft.com/v1.0/directory/administrativeUnits/{adminunit-id}/members/{user-id}/$ref

## <a name="bulk-remove-more-than-one-user"></a>Hromadné odebrání více než jednoho uživatele

Můžete přejít na jednotky pro správu služby Azure AD > a vybrat jednotku správy, ze které chcete uživatele odebrat. Klikněte na hromadné odebrání člena. Stáhněte si šablonu CSV pro poskytnutí seznamu uživatelů, kteří mají být odebráni.

Upravte staženou šablonu sdíleného svazku clusteru pomocí příslušných uživatelských záznamů. Neodstraňujte první dva řádky šablony. Přidejte do každého řádku jeden hlavní název uživatele (UPN).

![Úprava souboru CSV pro odebrání hromadného uživatele z jednotek pro správu](./media/roles-admin-units-add-manage-users/bulk-user-entries.png)

Po uložení záznamů do souboru nahrajte soubor a vyberte **Odeslat**.

![Odeslat soubor hromadného nahrání](./media/roles-admin-units-add-manage-users/bulk-user-remove.png)

## <a name="next-steps"></a>Další kroky

- [Přiřazení role k jednotce pro správu](roles-admin-units-assign-roles.md)
- [Přidání skupin do jednotky pro správu](roles-admin-units-add-manage-groups.md)
