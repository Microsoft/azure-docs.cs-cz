---
title: Odebrání omezení při vytváření registrací aplikací – Azure AD | Microsoft Docs
description: Přiřazení vlastní role pro udělení neomezených registrací aplikací v Azure AD Active Directory
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: quickstart
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro, devx-track-azurepowershell
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48740c52e6713735957e1ebf00e8996800eabec2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103015773"
---
# <a name="quickstart-grant-permission-to-create-unlimited-app-registrations"></a>Rychlý Start: udělení oprávnění k vytvoření neomezených registrací aplikací

V této úvodní příručce vytvoříte vlastní roli s oprávněním k vytvoření neomezeného počtu registrací aplikací a potom přiřadíte tuto roli uživateli. Přiřazený uživatel pak může k vytváření registrací aplikací použít portál Azure AD, Azure AD PowerShell nebo rozhraní Microsoft Graph API. Na rozdíl od předdefinované role vývojář aplikace umožňuje tato vlastní role vytvářet neomezený počet registrací aplikací. Role vývojář aplikace uděluje možnost, ale celkový počet vytvořených objektů je omezen na 250, aby se zabránilo dosažení [kvóty objektu pro celé adresáře](../enterprise-users/directory-service-limits-restrictions.md). Nejnižší privilegovaná role požadovaná k vytvoření a přiřazení vlastních rolí Azure AD je správcem privilegovaných rolí.

Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="create-a-custom-role-using-the-azure-ad-portal"></a>Vytvoření vlastní role pomocí portálu Azure AD

1. Přihlaste se do [centra pro správu Azure AD](https://aad.portal.azure.com) pomocí oprávnění správce privilegované role nebo globální správce v organizaci Azure AD.
1. Vyberte **Azure Active Directory**, vyberte **role a správci** a pak vyberte **Nová vlastní role**.

    ![Vytvoření nebo úprava rolí na stránce role a správci](./media/quickstart-app-registration-limits/new-custom-role.png)

1. Na kartě **základy** zadejte "autor registrace aplikace" pro název role a "může vytvořit neomezený počet registrací aplikací" pro popis role a pak vybrat **Další**.

    ![zadání názvu a popisu vlastní role na kartě základy](./media/quickstart-app-registration-limits/basics-tab.png)

1. Na kartě **oprávnění** zadejte do vyhledávacího pole "Microsoft. Directory/Applications/Create" a potom zaškrtněte políčka vedle požadovaných oprávnění a pak vyberte **Další**.

    ![Na kartě oprávnění vyberte oprávnění pro vlastní roli.](./media/quickstart-app-registration-limits/permissions-tab.png)

1. Na kartě **Revize + vytvořit** Zkontrolujte oprávnění a vyberte **vytvořit**.

### <a name="assign-the-role-in-the-azure-ad-portal"></a>Přiřazení role na portálu Azure AD

1. Přihlaste se do [centra pro správu Azure AD](https://aad.portal.azure.com) pomocí oprávnění správce privilegovaných rolí nebo globální správce ve vaší organizaci Azure AD.
1. Vyberte **Azure Active Directory** a pak vyberte **role a správci**.
1. Vyberte roli tvůrce registrace aplikace a vyberte **Přidat přiřazení**.
1. Vyberte požadovaného uživatele a kliknutím na tlačítko **Vybrat** přidejte uživatele do role.

Hotovo! V tomto rychlém startu jste úspěšně vytvořili vlastní roli s oprávněním k vytvoření neomezeného počtu registrací aplikací a pak tuto roli přiřadíte uživateli.

> [!TIP]
> Pokud chcete přiřadit roli k aplikaci pomocí portálu Azure AD, zadejte název aplikace do vyhledávacího pole na stránce přiřazení. Ve výchozím nastavení se aplikace nezobrazuje v seznamu, ale ve výsledcích hledání se vrátí.

### <a name="app-registration-permissions"></a>Oprávnění registrace aplikací

Existují dvě oprávnění, která jsou k dispozici pro udělení možnosti vytvářet registrace aplikací, z nichž každá má jiné chování.

- Microsoft. Directory/Applications/createAsOwner: přiřadíte-li toto oprávnění, bude autor přičtený jako první vlastník vytvořené registrace aplikace a tato vytvořená registrace aplikace se bude počítat s kvótou vytvořených objektů autora 250.
- Microsoft. Directory/Applications/Create: přiřadíte-li toto oprávnění, nepřidá se autor jako první vlastník vytvořené registrace aplikace a registrace vytvořené aplikace se nebude počítat s kvótou vytvořených objektů autora 250. Toto oprávnění používejte opatrně, protože nebrání žádnému neoprávněnému nabyvateli v vytváření registrací aplikací, dokud se nedosáhne kvóty na úrovni adresáře. Pokud jsou přiřazena obě oprávnění, má tato oprávnění přednost.

## <a name="create-a-custom-role-in-azure-ad-powershell"></a>Vytvoření vlastní role v Azure AD PowerShellu

### <a name="prepare-powershell"></a>Příprava PowerShellu

Nejdřív z [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17)nainstalujte modul Azure AD PowerShell. Pak importujte modul Azure AD PowerShell Preview pomocí následujícího příkazu:

```powershell
Import-Module -Name AzureADPreview
```

Chcete-li ověřit, zda je modul připravený k použití, odpovídá verzi vrácené následujícím příkazem následujícímu, který je uveden zde:

```powershell
Get-Module -Name AzureADPreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    AzureADPreview               {Add-AzureADAdministrati...}
```

### <a name="create-the-custom-role-in-azure-ad-powershell"></a>Vytvoření vlastní role v Azure AD PowerShellu

Pomocí následujícího skriptu PowerShellu vytvořte novou roli:

```powershell

# Basic role information
$displayName = "Application Registration Creator"
$description = "Can create an unlimited number of application registrations."
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/create"
    "microsoft.directory/applications/createAsOwner"
)
$rolePermissions = @{'allowedResourceActions'= $allowedResourceAction}

# Create new custom admin role
$customRole = New-AzureAdMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-role-in-azure-ad-powershell"></a>Přiřazení role v Azure AD PowerShellu

Přiřaďte roli pomocí následujícího skriptu prostředí PowerShell:

```powershell
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'Adam@contoso.com'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Registration Creator'"

# Get resource scope for assignment
$resourceScope = '/'

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

## <a name="create-a-custom-role-in-the-microsoft-graph-api"></a>Vytvoření vlastní role v rozhraní Microsoft Graph API

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
                    "microsoft.directory/applications/createAsOwner"
                ]
            },
            "condition":null
        }
    ],
    "templateId":"<PROVIDE NEW GUID HERE>",
    "version":"1"
}
```

### <a name="assign-the-role-in-the-microsoft-graph-api"></a>Přiřazení role v rozhraní Microsoft Graph API

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
- Další informace o přiřazení rolí Azure AD najdete v tématu [přiřazení rolí správce](permissions-reference.md).
- Další informace o výchozích uživatelských oprávněních najdete v tématu [Porovnání výchozích uživatelských oprávnění hostů a členů](../fundamentals/users-default-permissions.md).
