---
title: Vytvoření vlastní definice role v rámci řízení přístupu na základě role Azure AD – Azure Active Directory | Microsoft Docs
description: Vytvořte vlastní role Azure AD s oborem prostředků u prostředků Azure Active Directory.
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
ms.openlocfilehash: e0e33cf9fa1c4661dd71fc41cb667b0373c9e955
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2019
ms.locfileid: "68774819"
---
# <a name="create-a-custom-role-and-assign-at-resource-scope-in-azure-active-directory"></a>Vytvoření vlastní role a přiřazení v oboru prostředků v Azure Active Directory

Tento článek popisuje, jak vytvořit nové vlastní role v Azure Active Directory (Azure AD). Vlastní role se dají vytvořit na kartě [role a správci](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) na stránce Přehled Azure AD. Role může být přiřazena pouze v oboru na úrovni adresáře nebo v oboru prostředků registrace aplikace.

Další informace najdete v tématu [Přehled vlastních rolí](roles-custom-overview.md) pro základy vlastních rolí.

## <a name="using-the-azure-ad-portal"></a>Používání portálu Azure AD

### <a name="create-a-new-custom-role-to-grant-access-to-manage-app-registrations"></a>Vytvoření nové vlastní role pro udělení přístupu pro správu registrací aplikací

1. Přihlaste se do  [centra pro správu Azure AD](https://aad.portal.azure.com)pomocí oprávnění správce privilegované role nebo globální správce v organizaci Azure AD.
1. Vyberte **Azure Active Directory** > **role a správci** > **novou vlastní roli**.

   ![Vytvoření nebo úprava rolí na stránce role a správci](./media/roles-create-custom/new-custom-role.png)

1. Na kartě **základy** zadejte název a popis role a pak klikněte na **Další**.

   ![zadání názvu a popisu vlastní role na kartě základy](./media/roles-create-custom/basics-tab.png)

1. Na kartě **oprávnění** vyberte oprávnění nutná ke správě základních vlastností a vlastností přihlašovacích údajů pro registrace aplikací. Podrobný popis jednotlivých oprávnění naleznete [v tématu typy registrace aplikace a oprávnění v Azure Active Directory](./roles-custom-available-permissions.md).
   1. Nejdřív na panelu hledání zadejte "přihlašovací údaje" a vyberte `microsoft.directory/applications/credentials/update` oprávnění.

      ![Na kartě oprávnění vyberte oprávnění pro vlastní roli.](./media/roles-create-custom/permissions-tab.png)

   1. Potom na panelu hledání zadejte "základní", vyberte `microsoft.directory/applications/basic/update` oprávnění a pak klikněte na **Další**.
1. Na kartě **Revize + vytvořit** Zkontrolujte oprávnění a vyberte **vytvořit**.

Vaše vlastní role se zobrazí v seznamu dostupných rolí, které se mají přiřadit.

## <a name="assign-a-role-scoped-to-a-resource"></a>Přiřazení rozsahu role k prostředku

Podobně jako předdefinované role je možné přiřazovat vlastní role v oboru pro celou organizaci, aby bylo možné udělit přístup ke všem registrům aplikací. Ale vlastní role se dají přiřadit taky v oboru prostředků. To umožňuje dát nabyvateli oprávnění k aktualizaci přihlašovacích údajů a základních vlastností jedné aplikace bez nutnosti vytvořit druhou vlastní roli.

1. Pokud jste to ještě neučinili, přihlaste se do [centra pro správu Azure AD](https://aad.portal.azure.com) pomocí oprávnění vývojáře aplikace v organizaci Azure AD.
1. Vyberte **Registrace aplikací**.
1. Vyberte registraci aplikace, ke které udělujete přístup pro správu. Možná budete muset vybrat **všechny aplikace** , abyste viděli úplný seznam registrací aplikací ve vaší organizaci Azure AD.

    ![Vyberte registraci aplikace jako obor prostředku pro přiřazení role.](./media/roles-create-custom/appreg-all-apps.png)

1. V části registrace aplikace vyberte **role a správci**. Pokud jste ho ještě nevytvořili, postupujte podle pokynů v [předchozím postupu](#create-a-new-custom-role-to-grant-access-to-manage-app-registrations).

1. Výběrem role otevřete stránku **přiřazení** .
1. Vyberte **Přidat přiřazení** a přidejte uživatele. Uživateli se neudělí žádná oprávnění pro jinou registraci aplikace než vybraný.

## <a name="create-a-custom-role-using-azure-ad-powershell"></a>Vytvoření vlastní role pomocí Azure AD PowerShellu

### <a name="prepare-powershell"></a>Příprava PowerShellu

Nejdřív je nutné [Stáhnout modul Azure AD Preview PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).

K instalaci modulu Azure AD PowerShell použijte následující příkazy:

``` PowerShell
install-module azureadpreview
import-module azureadpreview
```

Chcete-li ověřit, zda je modul připravený k použití, použijte následující příkaz:

``` PowerShell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.2.31     azuread                      {Add-AzureADAdministrati...}
```

### <a name="create-the-custom-role"></a>Vytvoření vlastní role

Pomocí následujícího skriptu PowerShellu vytvořte novou roli:

``` PowerShell
# Basic role information
$description = "Application Support Administrator"
$displayName = "Can manage basic aspects of application registrations."
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/basic/update",
    "microsoft.directory/applications/credentials/update"
)
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customAdmin = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role-using-azure-ad-powershell"></a>Přiřazení vlastní role pomocí Azure AD PowerShellu

Přiřaďte roli pomocí níže uvedeného skriptu prostředí PowerShell:

``` PowerShell
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADRoleDefinition -Filter "displayName eq ' Application Registration Creator'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
$resourceScopes = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADRoleAssignment -ResourceScopes $resourceScopes -RoleDefinitionId $roleDefinition.objectId -PrincipalId $user.objectId
```

## <a name="create-a-custom-role-using-microsoft-graph-api"></a>Vytvoření vlastní role pomocí rozhraní Microsoft Graph API

1. Vytvořte definici role.

    Požadavek HTTP na vytvoření vlastní definice role.

    POST

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
    ```

    Tělo

    ``` HTTP
    {
    "description":"Can manage basic aspects of application registrations.",
    "displayName":"Application Support Administrator",
    "isEnabled":true,
    "rolePermissions":
    [
        "microsoft.directory/applications/basic/update",
        "microsoft.directory/applications/credentials/update"
    ]
    }
    ```

1. Vytvořte přiřazení role.

    Požadavek HTTP na vytvoření vlastní definice role.

    POST

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
    ```

    Tělo

    ``` HTTP
    {
    "principalId":"<GUID OF USER>",
    "roleDefinitionId":"<GUID OF ROLE DEFINITION>",
    "resourceScope":["/<GUID OF APPLICATION REGISTRATION>"]
    }
    ```

## <a name="next-steps"></a>Další postup

- Můžete s námi sdílet na [fóru role pro správu Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
- Další informace o rolích a přiřazení rolí správců najdete v tématu [přiřazení rolí správce](directory-assign-admin-roles.md).
- Pro výchozí uživatelská oprávnění si přečtěte téma [Porovnání výchozích oprávnění typu Host a uživatel](../fundamentals/users-default-permissions.md).
