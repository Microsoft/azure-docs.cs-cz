---
title: Odebrání omezení pro vytváření registrací aplikací – Azure AD | Dokumenty společnosti Microsoft
description: Přiřazení vlastní role k udělení neomezených registrací aplikací ve službě Azure AD Active Directory
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
ms.openlocfilehash: 7acd76ff45f783f614b2a1d3f0d5c10d800a1ea9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77559041"
---
# <a name="quickstart-grant-permission-to-create-unlimited-app-registrations"></a>Úvodní příručka: Udělení oprávnění k vytváření neomezených registrací aplikací

V tomto rychlém startu vytvoříte vlastní roli s oprávněním k vytvoření neomezeného počtu registrací aplikací a pak tuto roli přiřadíte uživateli. Přiřazený uživatel pak může k vytvoření registrací aplikací použít portál Azure AD, Azure AD PowerShell nebo rozhraní Microsoft Graph API. Na rozdíl od předdefinované role vývojáře aplikací tato vlastní role uděluje možnost vytvořit neomezený počet registrací aplikací. Role Vývojář aplikace uděluje schopnost, ale celkový počet vytvořených objektů je omezen na 250, aby se zabránilo [přístupu na kvótu objektů pro celý adresář](directory-service-limits-restrictions.md).

Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisite"></a>Požadavek

Nejméně privilegovanou rolí potřebnou k vytvoření a přiřazení vlastních rolí Azure AD je správce privilegovaných rolí.

## <a name="create-a-new-custom-role-using-the-azure-ad-portal"></a>Vytvoření nové vlastní role pomocí portálu Azure AD

1. Přihlaste se do  [Centra pro správu Azure AD](https://aad.portal.azure.com)s oprávněními správce privilegovaných rolí nebo globálního správce v organizaci Azure AD.
1. Vyberte **Azure Active Directory**, vyberte Role a **správce**a pak vyberte Nová **vlastní role**.

    ![Vytvoření nebo úprava rolí ze stránky Role a správci](./media/roles-create-custom/new-custom-role.png)

1. Na kartě **Základy** zadejte "Application Registration Creator" pro název role a "Můžete vytvořit neomezený počet registrací aplikací" pro popis role a pak vyberte **Další**.

    ![Zadejte název a popis vlastní role na kartě Základy](./media/roles-quickstart-app-registration-limits/basics-tab.png)

1. Na kartě **Oprávnění** zadejte do vyhledávacího pole položku Microsoft.directory/applications/create , zaškrtněte políčka vedle požadovaných oprávnění a pak vyberte **další**.

    ![Výběr oprávnění pro vlastní roli na kartě Oprávnění](./media/roles-quickstart-app-registration-limits/permissions-tab.png)

1. Na kartě **Revize + vytvořit** zkontrolujte oprávnění a vyberte **Vytvořit**.

### <a name="assign-the-role-to-a-user-using-the-azure-ad-portal"></a>Přiřazení role uživateli pomocí portálu Azure AD

1. Přihlaste se do  [Centra pro správu Azure AD](https://aad.portal.azure.com)s oprávněními správce privilegovaných rolí nebo globálního správce ve vaší organizaci Azure AD.
1. Vyberte **Azure Active Directory** a pak vyberte Role a **správce**.
1. Vyberte roli Tvůrce registrace aplikace a vyberte **Přidat přiřazení**.
1. Vyberte požadovaného uživatele a kliknutím na **Vybrat** přidejte uživatele do role.

Hotovo! V tomto rychlém startu jste úspěšně vytvořili vlastní roli s oprávněním k vytvoření neomezeného počtu registrací aplikací a pak tuto roli přiřadíte uživateli.

> [!TIP]
> Chcete-li přiřadit roli aplikaci pomocí portálu Azure AD, zadejte název aplikace do vyhledávacího pole stránky přiřazení. Aplikace nejsou ve výchozím nastavení zobrazeny v seznamu, ale jsou vráceny ve výsledcích hledání.

### <a name="app-registration-permissions"></a>Oprávnění k registraci aplikace

Existují dvě oprávnění k dispozici pro udělení možnosti vytvářet registrace aplikací, z nichž každé s jiným chováním.

- Microsoft.directory/applications/createAsOwner: Přiřazení tohoto oprávnění vede k tomu, že tvůrce bude přidán jako první vlastník registrace vytvořené aplikace a vytvořená registrace aplikace se započítá v kvótu 250 vytvořených objektů tvůrce.
- Microsoft.directory/applicationPolicies/create: Přiřazení tohoto oprávnění způsobí, že autor nebude přidán jako první vlastník registrace vytvořené aplikace a vytvořená registrace aplikace se nezapočítává do kvóty 250 vytvořených objektů tvůrce. Toto oprávnění používejte opatrně, protože postupitele nic nebrání ve vytváření registrací aplikací, dokud nebude kvóta na úrovni adresáře přístupová. Pokud jsou přiřazena obě oprávnění, má toto oprávnění přednost.

## <a name="create-a-custom-role-using-azure-ad-powershell"></a>Vytvoření vlastní role pomocí Azure AD PowerShellu

Vytvořte novou roli pomocí následujícího skriptu Prostředí PowerShell:

``` PowerShell
# Basic role information
$description = "Application Registration Creator"
$displayName = "Can create an unlimited number of application registrations."
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/createAsOwner"
)
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customRole = New-AzureAdRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role-using-azure-ad-powershell"></a>Přiřazení vlastní role pomocí Prostředí Azure AD PowerShell

#### <a name="prepare-powershell"></a>Příprava prostředí PowerShell

Nejprve nainstalujte modul Azure AD PowerShell z [Galerie Prostředí PowerShell](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). Pak importujte modul Preview prostředí Azure AD PowerShell pomocí následujícího příkazu:

```powershell
import-module azureadpreview
```

Chcete-li ověřit, zda je modul připraven k použití, porovnejte verzi vrácenou následujícím příkazem s verzí uvedenou zde:

```powershell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azureadpreview               {Add-AzureADAdministrati...}
```

#### <a name="assign-the-custom-role"></a>Přiřazení vlastní role

Přiřazení role pomocí níže uvedeného skriptu prostředí PowerShell:

``` PowerShell
# Basic role information
$description = "Application Registration Creator"
$displayName = "Can create an unlimited number of application registrations."
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/create"
)
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customRole = New-AzureAdRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="create-a-custom-role-using-microsoft-graph-api"></a>Vytvoření vlastní role pomocí rozhraní Microsoft Graph API

Požadavek HTTP pro vytvoření vlastní role.

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
```

Tělo

```HTTP
{
    "description":"Can create an unlimited number of application registrations.",
    "displayName":"Application Registration Creator",
    "isEnabled":true,
    "rolePermissions":
    [
        {
            "resourceActions":
            {
                "allowedResourceActions":
                [
                    "microsoft.directory/applications/create"
                ]
            },
            "condition":null
        }
    ],
    "templateId":"<PROVIDE NEW GUID HERE>",
    "version":"1"
}
```

### <a name="assign-the-custom-role-using-microsoft-graph-api"></a>Přiřazení vlastní role pomocí rozhraní Microsoft Graph API

Přiřazení role kombinuje ID instančního objektu zabezpečení (což může být uživatel nebo instanční objekt), ID definice role (role) a obor prostředků Azure AD.

Požadavek HTTP pro přiřazení vlastní role.

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
```

Tělo

``` HTTP
{
    "principalId":"<PROVIDE OBJECTID OF USER TO ASSIGN HERE>",
    "roleDefinitionId":"<PROVIDE OBJECTID OF ROLE DEFINITION HERE>",
    "resourceScopes":["/"]
}
```

## <a name="next-steps"></a>Další kroky

- Neváhejte se s námi podělit na [fóru rolí azure ad pro správu](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
- Další informace o rolích a přiřazení rolí správce najdete v tématu [Přiřazení rolí správce](directory-assign-admin-roles.md).
- Výchozí uživatelská oprávnění naleznete v [porovnání výchozích oprávnění hosta a člena uživatele](../fundamentals/users-default-permissions.md).
