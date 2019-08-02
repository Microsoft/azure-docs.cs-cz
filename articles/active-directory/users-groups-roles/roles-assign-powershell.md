---
title: Přiřazení rolí správce a jejich odebírání pomocí Azure PowerShell-Azure Active Directory | Microsoft Docs
description: Pro ty, kteří často spravují přiřazení rolí, teď můžete spravovat členy role správce Azure AD pomocí Azure PowerShell.
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa4bddf84720265afe361dff665f10ff8184f6f6
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706490"
---
# <a name="assign-azure-active-directory-admin-roles-using-powershell"></a>Přiřazení rolí správce Azure Active Directory pomocí prostředí PowerShell

Můžete automatizovat způsob přiřazování rolí uživatelským účtům pomocí Azure PowerShell. Tento článek používá modul [Azure Active Directory PowerShell verze 2](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#directory_roles) .

## <a name="prepare-powershell"></a>Příprava PowerShellu

Nejdřív je nutné [Stáhnout modul Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/).

## <a name="install-the-azure-ad-powershell-module"></a>Instalace modulu Azure AD Powershellu

K instalaci modulu Azure AD PowerShell použijte následující příkazy:

```powershell
install-module azuread
import-module azuread
```

Chcete-li ověřit, zda je modul připravený k použití, použijte následující příkaz:

```powershell
get-module azuread
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}
```

Teď můžete začít používat rutiny v modulu. Úplný popis rutin v modulu Azure AD najdete v online referenční dokumentaci pro [Azure Active Directory PowerShell verze 2](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#directory_roles).

## <a name="permissions-required"></a>Požadovaná oprávnění

Připojte se k vašemu tenantovi Azure AD pomocí globálního účtu správce a přiřaďte nebo odeberte role.

## <a name="assign-a-single-role"></a>Přiřazení jedné role

Pokud chcete přiřadit roli, musíte nejdřív získat její zobrazovaný název a název role, kterou přiřazujete. Pokud máte zobrazovaný název účtu a název role, k přiřazení role uživateli použijte následující rutiny.

``` PowerShell
# Fetch user to assign to role
$roleMember = Get-AzureADUser -ObjectId "username@contoso.com"

# Fetch User Account Administrator role instance
$role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}

# If role instance does not exist, instantiate it based on the role template
if ($role -eq $null) {
    # Instantiate an instance of the role template
    $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq 'User Account Administrator'}
    Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId

    # Fetch User Account Administrator role instance again
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}
}

# Add user to role
Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId $roleMember.ObjectId

# Fetch role membership for role to confirm
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId | Get-AzureADUser
```

## <a name="assign-a-role-to-a-service-principal"></a>Přiřazení role k instančnímu objektu

Příklad přiřazení instančního objektu k roli

```powershell
# Fetch a service principal to assign to role
$roleMember = Get-AzureADServicePrincipal -ObjectId "00221b6f-4387-4f3f-aa85-34316ad7f956"
 
#Fetch list of all directory roles with object ID
Get-AzureADDirectoryRole
 
# Fetch a directory role by ID
$role = Get-AzureADDirectoryRole -ObjectId "5b3fe201-fa8b-4144-b6f1-875829ff7543"
 
# Add user to role
Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId $roleMember.ObjectId
 
# Fetch the assignment for the role to confirm
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId | Get-AzureADServicePrincipal
```

## <a name="multiple-role-assignments"></a>Přiřazení více rolí

Příklady přiřazení a odebrání více rolí najednou.

```powershell
#File name
$fileName="<file path>" 
 
$input_Excel = New-Object -ComObject Excel.Application
$input_Workbook = $input_Excel.Workbooks.Open($fileName)
$input_Worksheet = $input_Workbook.Sheets.Item(1)
 
        #Count number of users who have to be assigned to role
$count = $input_Worksheet.UsedRange.Rows.Count
 
#Loop through each line of the csv file starting from line 2 (assuming first line is title)
for ($i=2; $i -le $count; $i++)
{
       #Fetch user display name
       $displayName = $input_Worksheet.Cells.Item($i,1).Text
       
       #Fetch role name
       $roleName = $input_Worksheet.Cells.Item($i,2).Text
 
       #Assign role
       Add-AzureADDirectoryRoleMember -ObjectId (Get-AzureADDirectoryRole | Where-Object DisplayName -eq $roleName).ObjectId -RefObjectId (Get-AzureADUser | Where-Object DisplayName -eq $displayName).ObjectId
}
 
#Remove multiple role assignments
for ($i=2; $i -le $count; $i++)
{
       $displayName = $input_Worksheet.Cells.Item($i,1).Text
       $roleName = $input_Worksheet.Cells.Item($i,2).Text
 
       Remove-AzureADDirectoryRoleMember -ObjectId (Get-AzureADDirectoryRole | Where-Object DisplayName -eq $roleName).ObjectId -MemberId (Get-AzureADUser | Where-Object DisplayName -eq $displayName).ObjectId
}
```

## <a name="remove-a-role-assignment"></a>Odebrání přiřazení role

Tento příklad odebere přiřazení role pro zadaného uživatele.

```powershell
# Fetch user to assign to role
$roleMember = Get-AzureADUser -ObjectId "username@contoso.com"

#Fetch list of all directory roles with object id
Get-AzureADDirectoryRole

# Fetch a directory role by id
$role = Get-AzureADDirectoryRole -ObjectId "5b3fe201-fa8b-4144-b6f1-875829ff7543"

# Remove user from role
Remove-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -MemberId $roleMember.ObjectId 

# Fetch role membership for role to confirm
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId | Get-AzureADUser

```

## <a name="next-steps"></a>Další postup

* Můžete s námi sdílet na [fóru role pro správu Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Další informace o rolích a přiřazení rolí správců najdete v tématu [přiřazení rolí správce](directory-assign-admin-roles.md).
* Pro výchozí uživatelská oprávnění si přečtěte téma [Porovnání výchozích oprávnění typu Host a uživatel](../fundamentals/users-default-permissions.md).
