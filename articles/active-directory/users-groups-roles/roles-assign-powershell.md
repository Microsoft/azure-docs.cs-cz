---
title: Přiřazení vlastních rolí pomocí Azure PowerShellu – Azure AD | Dokumenty společnosti Microsoft
description: Spravujte členy vlastní role správce Azure AD pomocí Azure PowerShellu.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a2096b7899039e7a9d3455bc0c6fb3ec84ebd1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74025324"
---
# <a name="assign-custom-roles-with-resource-scope-using-powershell-in-azure-active-directory"></a>Přiřazení vlastních rolí s oborem prostředků pomocí PowerShellu ve službě Azure Active Directory

Tento článek popisuje, jak vytvořit přiřazení role v oboru pro celou organizaci ve službě Azure Active Directory (Azure AD). Přiřazení role v oboru pro celou organizaci uděluje přístup v rámci organizace Azure AD. Pokud chcete vytvořit přiřazení role s rozsahem jednoho prostředku Azure AD, [přečtěte si](roles-create-custom.md)část Jak vytvořit vlastní roli a přiřadit ji v oboru prostředků . Tento článek používá modul [Azure Active Directory PowerShell verze 2.](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#directory_roles)

Další informace o rolích správců Azure AD najdete [v tématu Přiřazení rolí správce ve službě Azure Active Directory](directory-assign-admin-roles.md).

## <a name="required-permissions"></a>Požadovaná oprávnění

Připojte se k tenantovi Azure AD pomocí účtu globálního správce k přiřazení nebo odebrání rolí.

## <a name="prepare-powershell"></a>Příprava prostředí PowerShell

Nainstalujte modul Azure AD PowerShell z [Galerie PowerShellu](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). Pak importujte modul Preview prostředí Azure AD PowerShell pomocí následujícího příkazu:

``` PowerShell
import-module azureadpreview
```

Chcete-li ověřit, zda je modul připraven k použití, porovnejte verzi vrácenou následujícím příkazem s verzí uvedenou zde:

``` PowerShell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azureadpreview               {Add-AzureADMSAdministrati...}
```

Nyní můžete začít používat rutiny v modulu. Úplný popis rutin v modulu Azure AD najdete v online referenční dokumentaci pro [modul Azure AD preview](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17).

## <a name="assign-a-role-to-a-user-or-service-principal-with-resource-scope"></a>Přiřazení role uživateli nebo instančnímu objektu s oborem prostředků

1. Otevřete modul Azure AD preview PowerShellu.
1. Přihlaste se spuštěním příkazu `Connect-AzureAD`.
1. Vytvořte novou roli pomocí následujícího skriptu prostředí PowerShell.

``` PowerShell
## Assign a role to a user or service principal with resource scope
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Support Administrator'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

Chcete-li přiřadit roli instančního objektu namísto uživatele, použijte [rutinu Get-AzureADMSServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0).

## <a name="operations-on-roledefinition"></a>Operace na Definici role

Objekty definice role obsahují definici předdefinované nebo vlastní role spolu s oprávněními, která jsou udělena tímto přiřazením role. Tento prostředek zobrazí vlastní definice rolí a předdefinovaný adresářRole (které jsou zobrazeny v ekvivalentní podobě roleDefinition). Organizace Azure AD dnes může mít maximálně 30 jedinečných vlastních definic rolí definovaných.

### <a name="create-operations-on-roledefinition"></a>Vytvořit operace na definici role

``` PowerShell
# Basic information
$description = "Can manage credentials of application registrations"
$displayName = "Application Registration Credential Administrator"
$templateId = (New-Guid).Guid

# Set of actions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/standard/read",
    "microsoft.directory/applications/credentials/update"
)
$rolePermissions = @{'allowedResourceActions'= $allowedResourceAction}

# Create new custom admin role
$customAdmin = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="read-operations-on-roledefinition"></a>Číst operace na RoleDefinition

``` PowerShell
# Get all role definitions
Get-AzureADMSRoleDefinitions

# Get single role definition by objectId
Get-AzureADMSRoleDefinition -Id 86593cfc-114b-4a15-9954-97c3494ef49b

# Get single role definition by templateId
Get-AzureADMSRoleDefinition -Filter "templateId eq 'c4e39bd9-1100-46d3-8c65-fb160da0071f'"
```

### <a name="update-operations-on-roledefinition"></a>Aktualizovat operace na definici role

``` PowerShell
# Update role definition
# This works for any writable property on role definition. You can replace display name with other
# valid properties.
Set-AzureADMSRoleDefinition -Id c4e39bd9-1100-46d3-8c65-fb160da0071f -DisplayName "Updated DisplayName"
```

### <a name="delete-operations-on-roledefinition"></a>Odstranit operace na definici role

``` PowerShell
# Delete role definition
Remove-AzureADMSRoleDefinitions -Id c4e39bd9-1100-46d3-8c65-fb160da0071f
```

## <a name="operations-on-roleassignment"></a>Operace přiřazení role

Přiřazení rolí obsahují informace spojující daný objekt zabezpečení (instanční objekt uživatele nebo instančního objektu služby aplikace) s definicí role. V případě potřeby můžete přidat obor jednoho prostředku Azure AD pro přiřazená oprávnění.  Omezení rozsahu oprávnění je podporováno pro předdefinované a vlastní role.

### <a name="create-operations-on-roleassignment"></a>Vytvořit operace na přiřazení role

``` PowerShell
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Support Administrator'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

### <a name="read-operations-on-roleassignment"></a>Číst operace při přiřazení rolí

``` PowerShell
# Get role assignments for a given principal
Get-AzureADMSRoleAssignment -Filter "principalId eq '27c8ca78-ab1c-40ae-bd1b-eaeebd6f68ac'"

# Get role assignments for a given role definition 
Get-AzureADMSRoleAssignment -Filter "roleDefinitionId eq '355aed8a-864b-4e2b-b225-ea95482e7570'"
```

### <a name="delete-operations-on-roleassignment"></a>Odstranit operace přiřazení role

``` PowerShell
# Delete role assignment
Remove-AzureADMSRoleAssignment -Id 'qiho4WOb9UKKgng_LbPV7tvKaKRCD61PkJeKMh7Y458-1'
```

## <a name="next-steps"></a>Další kroky

- Sdílejte s námi na [fóru rolí pro správu Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
- Další informace o rolích a přiřazeních rolí správce azure AD najdete v tématu [Přiřazení rolí správce](directory-assign-admin-roles.md).
- Výchozí uživatelská oprávnění naleznete v [porovnání výchozích oprávnění hosta a člena uživatele](../fundamentals/users-default-permissions.md).
