---
title: Odebrání omezení při vytváření registrací aplikací – Azure AD | Microsoft Docs
description: Přiřazení vlastní role pro udělení neomezených registrací aplikací v Azure AD Active Directory
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
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/22/2020
ms.locfileid: "77559041"
---
# <a name="quickstart-grant-permission-to-create-unlimited-app-registrations"></a>Rychlý Start: udělení oprávnění k vytvoření neomezených registrací aplikací

V tomto rychlém startu vytvoříte vlastní roli s oprávněním k vytvoření neomezeného počtu registrací aplikací a potom tuto roli přiřadíte uživateli. Přiřazený uživatel pak může k vytváření registrací aplikací použít portál Azure AD, Azure AD PowerShell nebo rozhraní Microsoft Graph API. Na rozdíl od předdefinované role vývojář aplikace umožňuje tato vlastní role vytvářet neomezený počet registrací aplikací. Role vývojář aplikace uděluje možnost, ale celkový počet vytvořených objektů je omezen na 250, aby se zabránilo dosažení [kvóty objektu pro celé adresáře](directory-service-limits-restrictions.md).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisite"></a>Požadavek

Nejnižší privilegovaná role požadovaná k vytvoření a přiřazení vlastních rolí Azure AD je správcem privilegovaných rolí.

## <a name="create-a-new-custom-role-using-the-azure-ad-portal"></a>Vytvoření nové vlastní role pomocí portálu Azure AD

1. Přihlaste se k [centru pro správu Azure ad](https://aad.portal.azure.com) s oprávněním správce privilegované role nebo globálním správcem v organizaci Azure AD.
1. Vyberte **Azure Active Directory**, vyberte **role a správci**a pak vyberte **Nová vlastní role**.

    ![Vytvoření nebo úprava rolí na stránce role a správci](./media/roles-create-custom/new-custom-role.png)

1. Na kartě **základy** zadejte "autor registrace aplikace" pro název role a "může vytvořit neomezený počet registrací aplikací" pro popis role a pak vybrat **Další**.

    ![zadání názvu a popisu vlastní role na kartě základy](./media/roles-quickstart-app-registration-limits/basics-tab.png)

1. Na kartě **oprávnění** zadejte do vyhledávacího pole "Microsoft. Directory/Applications/Create" a potom zaškrtněte políčka vedle požadovaných oprávnění a pak vyberte **Další**.

    ![Na kartě oprávnění vyberte oprávnění pro vlastní roli.](./media/roles-quickstart-app-registration-limits/permissions-tab.png)

1. Na kartě **Revize + vytvořit** Zkontrolujte oprávnění a vyberte **vytvořit**.

### <a name="assign-the-role-to-a-user-using-the-azure-ad-portal"></a>Přiřazení role uživateli pomocí portálu Azure AD

1. Přihlaste se k [centru pro správu Azure ad](https://aad.portal.azure.com) s oprávněním správce privilegované role nebo globálním správcem ve vaší organizaci Azure AD.
1. Vyberte **Azure Active Directory** a pak vyberte **role a správci**.
1. Vyberte roli tvůrce registrace aplikace a vyberte **Přidat přiřazení**.
1. Vyberte požadovaného uživatele a kliknutím na tlačítko **Vybrat** přidejte uživatele do role.

Hotovo! V tomto rychlém startu jste úspěšně vytvořili vlastní roli s oprávněním k vytvoření neomezeného počtu registrací aplikací a pak tuto roli přiřadíte uživateli.

> [!TIP]
> Pokud chcete přiřadit roli k aplikaci pomocí portálu Azure AD, zadejte název aplikace do vyhledávacího pole na stránce přiřazení. Ve výchozím nastavení se aplikace nezobrazuje v seznamu, ale ve výsledcích hledání se vrátí.

### <a name="app-registration-permissions"></a>Oprávnění k registraci aplikace

Existují dvě oprávnění, která jsou k dispozici pro udělení možnosti vytvářet registrace aplikací, z nichž každá má jiné chování.

- Microsoft. Directory/Applications/createAsOwner: přiřadíte-li toto oprávnění, bude autor přičtený jako první vlastník vytvořené registrace aplikace a tato vytvořená registrace aplikace se bude počítat s kvótou vytvořených objektů autora 250.
- Microsoft. Directory/applicationPolicies/Create: přiřadíte-li toto oprávnění, nepřidá se autor jako první vlastník vytvořené registrace aplikace a registrace vytvořené aplikace se nebude počítat s kvótou vytvořených objektů autora 250. Toto oprávnění používejte opatrně, protože nebrání žádnému neoprávněnému nabyvateli v vytváření registrací aplikací, dokud se nedosáhne kvóty na úrovni adresáře. Pokud jsou přiřazena obě oprávnění, má tato oprávnění přednost.

## <a name="create-a-custom-role-using-azure-ad-powershell"></a>Vytvoření vlastní role pomocí Azure AD PowerShellu

Pomocí následujícího skriptu PowerShellu vytvořte novou roli:

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

### <a name="assign-the-custom-role-using-azure-ad-powershell"></a>Přiřazení vlastní role pomocí Azure AD PowerShellu

#### <a name="prepare-powershell"></a>Příprava PowerShellu

Nejdřív z [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17)nainstalujte modul Azure AD PowerShell. Pak importujte modul Azure AD PowerShell Preview pomocí následujícího příkazu:

```powershell
import-module azureadpreview
```

Chcete-li ověřit, zda je modul připravený k použití, odpovídá verzi vrácené následujícím příkazem následujícímu, který je uveden zde:

```powershell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azureadpreview               {Add-AzureADAdministrati...}
```

#### <a name="assign-the-custom-role"></a>Přiřazení vlastní role

Přiřaďte roli pomocí níže uvedeného skriptu prostředí PowerShell:

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

Požadavek HTTP na vytvoření vlastní role

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
```

Text

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

Přiřazení role kombinuje ID objektu zabezpečení (může to být uživatel nebo instanční objekt), definice role (role) a obor prostředků služby Azure AD.

Požadavek HTTP na přiřazení vlastní role

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
```

Text

``` HTTP
{
    "principalId":"<PROVIDE OBJECTID OF USER TO ASSIGN HERE>",
    "roleDefinitionId":"<PROVIDE OBJECTID OF ROLE DEFINITION HERE>",
    "resourceScopes":["/"]
}
```

## <a name="next-steps"></a>Další kroky

- Můžete s námi sdílet na [fóru role pro správu Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
- Další informace o rolích a přiřazení rolí správců najdete v tématu [přiřazení rolí správce](directory-assign-admin-roles.md).
- Pro výchozí uživatelská oprávnění si přečtěte téma [Porovnání výchozích oprávnění typu Host a uživatel](../fundamentals/users-default-permissions.md).
