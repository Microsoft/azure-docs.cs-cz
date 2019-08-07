---
title: Přiřazení vlastních rolí k oboru prostředků pomocí Azure PowerShell-Azure Active Directory | Microsoft Docs
description: Spravujte členy vlastní role správce Azure AD pomocí Azure PowerShell.
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 08/05/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2ed8df92ac6a43a6cbf935bdb564f6cf0658608a
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812784"
---
# <a name="assign-custom-roles-with-resource-scope-using-powershell-in-azure-active-directory"></a>Přiřazení vlastních rolí k oboru prostředků pomocí PowerShellu v Azure Active Directory

Tento článek popisuje, jak vytvořit přiřazení role v oboru Azure Active Directory (Azure AD v rámci organizace). Přiřazení role v oboru pro celou organizaci uděluje přístup napříč organizací Azure AD. Pokud chcete vytvořit přiřazení role s oborem jednoho prostředku služby Azure AD, přečtěte si téma [Vytvoření vlastní role a její přiřazení v oboru prostředků](roles-create-custom.md). Tento článek používá modul [Azure Active Directory PowerShell verze 2](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#directory_roles) .

Další informace o rolích správce Azure AD najdete v tématu [přiřazení rolí správce v Azure Active Directory](directory-assign-admin-roles.md).

## <a name="required-permissions"></a>Požadovaná oprávnění

Připojte se k vašemu tenantovi Azure AD pomocí globálního účtu správce a přiřaďte nebo odeberte role.

## <a name="prepare-powershell"></a>Příprava PowerShellu

Nainstalujte modul Azure AD PowerShell z [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). Pak importujte modul Azure AD PowerShell Preview pomocí následujícího příkazu:

``` PowerShell
import-module azureadpreview
```

Chcete-li ověřit, zda je modul připravený k použití, odpovídá verzi vrácené následujícím příkazem následujícímu, který je uveden zde:

``` PowerShell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azureadpreview               {Add-AzureADMSAdministrati...}
```

Teď můžete začít používat rutiny v modulu. Úplný popis rutin v modulu Azure AD najdete v online referenční dokumentaci pro [modul Azure AD Preview](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17).

## <a name="assign-a-role-to-a-user-or-service-principal-with-resource-scope"></a>Přiřazení role uživateli nebo instančnímu objektu s rozsahem prostředků

1. Otevřete modul Azure AD Preview PowerShellu.
1. Přihlaste se spuštěním příkazu `Connect-AzureAD`.
1. Pomocí následujícího skriptu PowerShellu vytvořte novou roli.

``` PowerShell
# Get the user and role definition you want to link
 $user = Get-AzureADMSUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Support Administrator'"

# Get app registration and construct resource scope for assignment.
    "displayName eq 'f/128 Filter Photos'"
$resourceScopes = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScopes $resourceScopes -RoleDefinitionId $roleDefinition.objectId -PrincipalId $user.objectId
```

K přiřazení role k instančnímu objektu místo uživatele použijte [rutinu Get-AzureADMSServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0).

## <a name="operations-on-roledefinition"></a>Operace na rutiny roledefinition

Objekty definice role obsahují definici předdefinované nebo vlastní role společně s oprávněním uděleným přiřazením této role. Tento prostředek zobrazuje jak definice vlastních rolí, tak integrované directoryRoles (které se zobrazují ve formě ekvivalentu rutiny roledefinition). V současné době může organizace Azure AD mít definovanou maximálně 30 jedinečných vlastních RoleDefinitions.

### <a name="create-operations-on-roledefinition"></a>Vytvoření operací na rutiny roledefinition

``` PowerShell
# Basic information

$description = "Application Registration Credential Administrator"
$displayName = "Custom Demo Admin"
$resourceScopes = @('/')
$templateId = "355aed8a-864b-4e2b-b225-ea95482e7570"

# Set of actions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/default/read",
    "microsoft.directory/applications/credentials/update"
)
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customAdmin = New-AzureADMSRoleDefinitions -RolePermissions $rolePermissions -ResourceScopes $resourceScopes -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="read-operations-on-roledefinition"></a>Operace čtení na rutiny roledefinition

``` PowerShell
# Get all role definitions
Get-AzureADMSRoleDefinitions

# Get single role definition by objectId
$customAdmin = Get-AzureADMSRoleDefinitions -ObjectId '86593cfc-114b-4a15-9954-97c3494ef49b'

# Get single role definition by templateId
$customAdmin = Get-AzureADMSRoleDefinitions -Filter "templateId eq '355aed8a-864b-4e2b-b225-ea95482e757not
```

### <a name="update-operations-on-roledefinition"></a>Operace aktualizace na rutiny roledefinition

``` PowerShell
# Update role definition
# This works for any writable property on role definition. You can replace display name with other
# valid properties.
Set-AzureADMSRoleDefinitions -ObjectId $customAdmin.ObjectId -DisplayName "Updated DisplayName"
```

### <a name="delete-operations-on-roledefinition"></a>Odstranění operací na rutiny roledefinition

``` PowerShell
# Delete role definition
Remove-AzureADMSRoleDefinitions -ObjectId $customAdmin.ObjectId
```

## <a name="operations-on-roleassignment"></a>Operace na RoleAssignment

Přiřazení rolí obsahují informace, které propojuje daný objekt zabezpečení (uživatel nebo instanční objekt aplikace) s definicí role. V případě potřeby můžete pro přiřazená oprávnění přidat obor jednoho prostředku služby Azure AD.  Omezení rozsahu oprávnění je podporováno pro předdefinované a vlastní role.

### <a name="create-operations-on-roleassignment"></a>Vytvoření operací na RoleAssignment

``` PowerShell
# Scopes to scope granted permissions to
$resourceScopes = @('/')

# IDs of principal and role definition you want to link
$principalId = "27c8ca78-ab1c-40ae-bd1b-eaeebd6f68ac"
$roleDefinitionId = $customKeyCredAdmin.ObjectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignments -ResourceScopes $resourceScopes -RoleDefinitionId -PrincipalId $principalId
```

### <a name="read-operations-on-roleassignment"></a>Operace čtení na RoleAssignment

``` PowerShell
# Get role assignments for a given principal
Get-AzureADMSRoleAssignments -Filter "principalId eq '27c8ca78-ab1c-40ae-bd1b-eaeebd6f68ac'"

# Get role assignments for a given role definition 
Get-AzureADMSRoleAssignments -Filter "principalId eq '355aed8a-864b-4e2b-b225-ea95482e7570'"
```

### <a name="delete-operations-on-roleassignment"></a>Odstranění operací na RoleAssignment

``` PowerShell
# Delete role assignment
Remove-AzureADMSRoleAssignments -ObjectId $roleAssignment.ObjectId
```

## <a name="next-steps"></a>Další kroky

- Sdílejte s námi na [fóru o rolích pro správu Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
- Další informace o rolích a přiřazení rolí správců služby Azure AD najdete v tématu [přiřazení rolí správce](directory-assign-admin-roles.md).
- Pro výchozí uživatelská oprávnění si přečtěte téma [Porovnání výchozích oprávnění typu Host a uživatel](../fundamentals/users-default-permissions.md).
