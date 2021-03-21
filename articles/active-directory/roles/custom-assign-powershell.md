---
title: Přiřazení vlastních rolí pomocí Azure AD PowerShellu – Azure AD | Microsoft Docs
description: Správa členů vlastní role správce Azure AD pomocí Azure AD PowerShellu
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f0fb81a4daa57b473e8b2b4b937426eafbf903d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103014532"
---
# <a name="assign-custom-roles-with-resource-scope-using-powershell-in-azure-active-directory"></a>Přiřazení vlastních rolí k oboru prostředků pomocí PowerShellu v Azure Active Directory

Tento článek popisuje, jak vytvořit přiřazení role v oboru Azure Active Directory (Azure AD v rámci organizace). Přiřazení role v oboru pro celou organizaci uděluje přístup napříč organizací Azure AD. Pokud chcete vytvořit přiřazení role s oborem jednoho prostředku služby Azure AD, přečtěte si téma [Vytvoření vlastní role a její přiřazení v oboru prostředků](custom-create.md). Tento článek používá modul [Azure Active Directory PowerShell verze 2](/powershell/module/azuread/#directory_roles) .

Další informace o rolích správce Azure AD najdete v tématu [přiřazení rolí správce v Azure Active Directory](permissions-reference.md).

## <a name="required-permissions"></a>Požadovaná oprávnění

Připojte se k vaší organizaci Azure AD pomocí globálního účtu správce a přiřaďte nebo odeberte role.

## <a name="prepare-powershell"></a>Příprava PowerShellu

Nainstalujte modul Azure AD PowerShell z [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/AzureADPreview). Pak importujte modul Azure AD PowerShell Preview pomocí následujícího příkazu:

``` PowerShell
Import-Module -Name AzureADPreview
```

Chcete-li ověřit, zda je modul připravený k použití, odpovídá verzi vrácené následujícím příkazem následujícímu, který je uveden zde:

``` PowerShell
Get-Module -Name AzureADPreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    AzureADPreview               {Add-AzureADMSAdministrati...}
```

Teď můžete začít používat rutiny v modulu. Úplný popis rutin v modulu Azure AD najdete v online referenční dokumentaci pro [modul Azure AD Preview](https://www.powershellgallery.com/packages/AzureADPreview).

## <a name="assign-a-directory-role-to-a-user-or-service-principal-with-resource-scope"></a>Přiřazení role adresáře uživateli nebo instančnímu objektu s rozsahem prostředků

1. Načtěte modul Azure AD PowerShell (Preview).
1. Přihlaste se spuštěním příkazu `Connect-AzureAD` .
1. Pomocí následujícího skriptu PowerShellu vytvořte novou roli.

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

K přiřazení role k instančnímu objektu místo uživatele použijte rutinu [Get-AzureADMSServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) .

## <a name="role-definitions"></a>Definice rolí

Objekty definice role obsahují definici předdefinované nebo vlastní role společně s oprávněním uděleným přiřazením této role. Tento prostředek zobrazuje definice vlastních rolí a předdefinované role adresáře (které se zobrazují ve formě ekvivalentní rutiny roledefinition). V současné době může organizace Azure AD mít definované maximálně 30 jedinečných definic vlastních rolí.

### <a name="create-a-role-definition"></a>Vytvoření definice role

``` PowerShell
# Basic information
$description = "Can manage credentials of application registrations"
$displayName = "Application Registration Credential Administrator"
$templateId = (New-Guid).Guid

# Set of actions to include
$rolePermissions = @{
    "allowedResourceActions" = @(
        "microsoft.directory/applications/standard/read",
        "microsoft.directory/applications/credentials/update"
    )
}

# Create new custom directory role
$customAdmin = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="read-and-list-role-definitions"></a>Čtení a výpis definic rolí

``` PowerShell
# Get all role definitions
Get-AzureADMSRoleDefinitions

# Get single role definition by ID
Get-AzureADMSRoleDefinition -Id 86593cfc-114b-4a15-9954-97c3494ef49b

# Get single role definition by templateId
Get-AzureADMSRoleDefinition -Filter "templateId eq 'c4e39bd9-1100-46d3-8c65-fb160da0071f'"
```

### <a name="update-a-role-definition"></a>Aktualizace definice role

``` PowerShell
# Update role definition
# This works for any writable property on role definition. You can replace display name with other
# valid properties.
Set-AzureADMSRoleDefinition -Id c4e39bd9-1100-46d3-8c65-fb160da0071f -DisplayName "Updated DisplayName"
```

### <a name="delete-a-role-definition"></a>Odstranění definice role

``` PowerShell
# Delete role definition
Remove-AzureADMSRoleDefinitions -Id c4e39bd9-1100-46d3-8c65-fb160da0071f
```

## <a name="role-assignments"></a>Přiřazení rolí

Přiřazení rolí obsahují informace, které propojuje daný objekt zabezpečení (uživatel nebo instanční objekt aplikace) s definicí role. V případě potřeby můžete pro přiřazená oprávnění přidat obor jednoho prostředku služby Azure AD.  Omezení rozsahu přiřazení role se podporuje pro předdefinované a vlastní role.

### <a name="create-a-role-assignment"></a>Vytvoření přiřazení role

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

### <a name="read-and-list-role-assignments"></a>Čtení a výpis přiřazení rolí

``` PowerShell
# Get role assignments for a given principal
Get-AzureADMSRoleAssignment -Filter "principalId eq '27c8ca78-ab1c-40ae-bd1b-eaeebd6f68ac'"

# Get role assignments for a given role definition 
Get-AzureADMSRoleAssignment -Filter "roleDefinitionId eq '355aed8a-864b-4e2b-b225-ea95482e7570'"
```

### <a name="delete-a-role-assignment"></a>Odstranění přiřazení role

``` PowerShell
# Delete role assignment
Remove-AzureADMSRoleAssignment -Id 'qiho4WOb9UKKgng_LbPV7tvKaKRCD61PkJeKMh7Y458-1'
```

## <a name="next-steps"></a>Další kroky

- Sdílejte s námi na [fóru o rolích pro správu Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)
- Další informace o rolích a přiřazení rolí správců služby Azure AD najdete v tématu [přiřazení rolí správce](permissions-reference.md) .
- Výchozí uživatelská oprávnění najdete v tématu [Porovnání výchozích oprávnění typu Host a uživatel](../fundamentals/users-default-permissions.md) .
