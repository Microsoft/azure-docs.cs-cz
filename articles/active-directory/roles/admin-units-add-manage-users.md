---
title: Přidání, odebrání a výpis uživatelů v jednotce pro správu – Azure Active Directory | Microsoft Docs
description: Správa uživatelů a jejich oprávnění role v jednotce pro správu v Azure Active Directory
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
ms.openlocfilehash: b490e03ea7ac0a3bc780fa731629217126b6828a
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2020
ms.locfileid: "93395525"
---
# <a name="add-and-manage-users-in-an-administrative-unit-in-azure-active-directory"></a>Přidat a spravovat uživatele v jednotce pro správu v Azure Active Directory

V Azure Active Directory (Azure AD) můžete přidat uživatele do jednotky pro správu a podrobnějšího administrativního rozsahu řízení.

Informace o přípravě na používání PowerShellu a Microsoft Graph pro správu jednotek správy najdete v [tématu Začínáme.](admin-units-manage.md#get-started)

## <a name="add-users-to-an-administrative-unit"></a>Přidání uživatelů do jednotky pro správu

### <a name="use-the-azure-portal"></a>Použití webu Azure Portal

Uživatele můžete přiřadit k jednotkám pro správu individuálně nebo jako hromadnou operaci.

- Přiřaďte jednotlivé uživatele z profilu uživatele:

   1. Přihlaste se k [centru pro správu Azure AD](https://portal.azure.com) pomocí oprávnění správce privilegovaných rolí.

   1. Vyberte **Uživatelé** a potom otevřete profil uživatele tak, že vyberete uživatele, který chcete přiřadit jednotce pro správu.
   
   1. Vyberte **jednotky pro správu**. 
   
   1. Chcete-li uživatele přiřadit k jedné nebo více jednotkám pro správu, vyberte možnost **přiřadit k jednotce pro správu** a potom v pravém podokně vyberte jednotky pro správu, ke kterým chcete přiřadit uživatele.

       ![Snímek obrazovky s podoknem "jednotky správy" pro přiřazení uživatele k jednotce pro správu.](./media/admin-units-add-manage-users/assign-users-individually.png)

- Přiřaďte jednotlivé uživatele z jednotky pro správu:

   1. Přihlaste se k [centru pro správu Azure AD](https://portal.azure.com) pomocí oprávnění správce privilegovaných rolí.
   1. Vyberte **jednotky pro správu** a potom vyberte jednotku pro správu, do které má být uživatel přiřazen.
   1. Vyberte **Všichni uživatelé** , vyberte **Přidat člena** a potom v podokně **Přidat člena** vyberte jednoho nebo více uživatelů, které chcete přiřadit k jednotce pro správu.

        ![Snímek obrazovky s podoknem administrativní jednotka pro přiřazení uživatele k jednotce pro správu](./media/admin-units-add-manage-users/assign-to-admin-unit.png)

- Přiřadit uživatele jako hromadnou operaci:

   1. Přihlaste se k [centru pro správu Azure AD](https://portal.azure.com) pomocí oprávnění správce privilegovaných rolí.

   1. Vyberte **jednotky pro správu**.

   1. Vyberte jednotku správy, ke které chcete přidat uživatele.

   1. Vyberte **Uživatelé**  >  **hromadné aktivity**  >  **hromadně přidat členy**. Pak můžete stáhnout šablonu hodnot oddělených čárkami (CSV) a soubor upravit. Formát je jednoduchý a vyžaduje, aby se na každý řádek přidal jeden hlavní název uživatele. Až bude soubor připravený, uložte ho do příslušného umístění a pak ho nahrajte jako součást tohoto kroku.

      ![Snímek obrazovky s podoknem uživatelé pro přiřazení uživatelů k jednotce pro správu jako hromadnou operaci.](./media/admin-units-add-manage-users/bulk-assign-to-admin-unit.png)

### <a name="use-powershell"></a>Použití prostředí PowerShell

V prostředí PowerShell pomocí `Add-AzureADAdministrativeUnitMember` rutiny v následujícím příkladu přidejte uživatele do jednotky pro správu. ID objektu jednotky pro správu, do které chcete přidat uživatele, a ID objektu uživatele, který chcete přidat, se považují za argumenty. Změňte zvýrazněný oddíl podle potřeby pro konkrétní prostředí.

```powershell
$administrativeunitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$UserObj = Get-AzureADUser -Filter "UserPrincipalName eq 'billjohn@fabidentity.onmicrosoft.com'"
Add-AzureADMSAdministrativeUnitMember -Id $administrativeunitObj.ObjectId -RefObjectId $UserObj.ObjectId
```


### <a name="use-microsoft-graph"></a>Použít Microsoft Graph

Zástupný text nahraďte testovacími informacemi a spusťte následující příkaz:

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

## <a name="view-a-list-of-administrative-units-for-a-user"></a>Zobrazit seznam jednotek pro správu pro uživatele

### <a name="use-the-azure-portal"></a>Použití webu Azure Portal

V Azure Portal můžete otevřít profil uživatele následujícím způsobem:

1. Přejít na **Azure AD** a pak vyberte **Uživatelé**.

1. Vyberte uživatele, jehož profil chcete zobrazit.

1. Vyberte **jednotky pro správu** a zobrazte tak seznam jednotek pro správu, ke kterým byl uživatel přiřazen.

   ![Snímek obrazovky jednotek pro správu, ke kterým byl přiřazen uživatel.](./media/admin-units-add-manage-users/list-user-admin-units.png)

### <a name="use-powershell"></a>Použití prostředí PowerShell

Spusťte následující příkaz:

```powershell
Get-AzureADMSAdministrativeUnit | where { Get-AzureADMSAdministrativeUnitMember -Id $_.ObjectId | where {$_.RefObjectId -eq $userObjId} }
```
> [!NOTE]
> Ve výchozím nastavení `Get-AzureADAdministrativeUnitMember` vrátí pouze 100 členů jednotky pro správu. Chcete-li načíst více členů, můžete přidat `"-All $true"` .

### <a name="use-microsoft-graph"></a>Použít Microsoft Graph

Zástupný text nahraďte testovacími informacemi a spusťte následující příkaz:

```http
https://graph.microsoft.com/v1.0/users/{id}/memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-single-user-from-an-administrative-unit"></a>Odebrání jednoho uživatele z jednotky pro správu

### <a name="use-the-azure-portal"></a>Použití webu Azure Portal

Uživatele můžete odebrat z jednotky pro správu jedním ze dvou způsobů: 

* V Azure Portal přejdete na **Azure AD** a pak vyberte **Uživatelé**. 
  1. Výběrem uživatele otevřete profil uživatele. 
  1. Vyberte jednotku správy, ze které chcete uživatele odebrat, a pak vyberte **Odebrat z jednotky pro správu**.

     ![Snímek obrazovky ukazující, jak odebrat uživatele z jednotky pro správu z podokna profilu uživatele](./media/admin-units-add-manage-users/user-remove-admin-units.png)

* V Azure Portal otevřete **službu Azure AD** a vyberte **jednotky pro správu**.
  1. Vyberte jednotku správy, ze které chcete uživatele odebrat. 
  1. Vyberte uživatele a pak vyberte **odebrat člena**.
  
     ![Snímek obrazovky ukazující, jak odebrat uživatele na úrovni jednotky pro správu](./media/admin-units-add-manage-users/admin-units-remove-user.png)

### <a name="use-powershell"></a>Použití prostředí PowerShell

Spusťte následující příkaz:

```powershell
Remove-AzureADMSAdministrativeUnitMember -Id $auId -MemberId $memberUserObjId
```

### <a name="use-microsoft-graph"></a>Použít Microsoft Graph

Zástupné symboly nahraďte testovacími informacemi a spusťte následující příkaz:

`https://graph.microsoft.com/v1.0/directory/administrativeUnits/{adminunit-id}/members/{user-id}/$ref`

## <a name="remove-multiple-users-as-a-bulk-operation"></a>Odebrat více uživatelů jako hromadnou operaci

Chcete-li odebrat více uživatelů z jednotky pro správu, postupujte takto:

1. V Azure Portal přejdete do **služby Azure AD**.

1. Vyberte **jednotky pro správu** a potom vyberte jednotku správy, ze které chcete odebrat uživatele. 

1. Vyberte **hromadné odebrání členů** a pak stáhněte šablonu sdíleného svazku clusteru, kterou použijete k vypsání uživatelů, které chcete odebrat.

   ![Snímek obrazovky s odkazem hromadným odebráním členů v podokně Uživatelé](./media/admin-units-add-manage-users/bulk-user-remove.png)

1. Upravte staženou šablonu sdíleného svazku clusteru pomocí příslušných uživatelských záznamů. Neodstraňujte první dva řádky šablony. Přidejte jeden hlavní název uživatele (UPN) do každého řádku.

   ![Snímek obrazovky upravovaného souboru CSV pro hromadnou odebrání uživatelů z jednotky pro správu](./media/admin-units-add-manage-users/bulk-user-entries.png)

1. Uložte změny, nahrajte soubor a pak vyberte **Odeslat**.

## <a name="next-steps"></a>Další kroky

- [Přiřazení role k jednotce pro správu](admin-units-assign-roles.md)
- [Přidání skupin do jednotky pro správu](admin-units-add-manage-groups.md)
