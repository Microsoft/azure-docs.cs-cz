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
ms.openlocfilehash: dcf4bc04a1c111e545d545af64f3940d3aa4585d
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707705"
---
# <a name="create-a-custom-role-and-assign-a-resource-scope-in-azure-active-directory"></a>Vytvoření vlastní role a přiřazení oboru prostředků v Azure Active Directory

Tento článek popisuje, jak vytvořit nové vlastní role v Azure Active Directory (Azure AD). Vlastní role se dají vytvořit na kartě **role a správci** na stránce Přehled služby Azure AD nebo [na stránce pro registraci aplikace](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview). Role může být přiřazena buď v oboru na úrovni adresáře, nebo vymezena pouze na registrace aplikací.

Další informace najdete v tématu [Přehled vlastních rolí](roles-custom-overview.md) pro základy vlastních rolí.

## <a name="using-the-azure-ad-portal"></a>Používání portálu Azure AD

### <a name="create-a-new-custom-role-to-grant-access-to-manage-app-registrations"></a>Vytvoření nové vlastní role pro udělení přístupu pro správu registrací aplikací

1. Přihlaste se do  [centra pro správu Azure AD](https://aad.portal.azure.com)pomocí oprávnění správce privilegované role nebo globální správce v organizaci Azure AD.
1. Vyberte **Azure Active Directory** > **role a správci** > **novou vlastní roli**.

   ![Vytvoření nebo úprava rolí na stránce role a správci](./media/roles-create-custom/new-custom-role.png)

1. Na kartě **základy** zadejte název a popis role.

   ![zadání názvu a popisu vlastní role na kartě základy](./media/roles-create-custom/basics-tab.png)

1. Pokud chcete vybrat oprávnění potřebná pro správu přihlašovacích údajů k registraci aplikací a základní vlastnosti, jako je třeba název:
   1. Na panelu hledání zadejte "přihlašovací údaje" a vyberte `microsoft.directory/applications/credentials/update` oprávnění.

      ![Na kartě oprávnění vyberte oprávnění pro vlastní roli.](./media/roles-create-custom/permissions-tab.png)

   1. Na panelu hledání zadejte "základní", vyberte `microsoft.directory/applications/basic/update` oprávnění a pak klikněte na **Další**.
1. Na kartě **Revize + vytvořit** Zkontrolujte oprávnění a vyberte **vytvořit**.

Vaše vlastní role se zobrazí v seznamu dostupných rolí, které se mají přiřadit.

## <a name="assign-a-role-scoped-to-a-resource"></a>Přiřazení rozsahu role k prostředku

Podobně jako předdefinované role je možné přiřazovat vlastní role ve výchozím oboru organizace pro udělení přístupu ke všem registrům aplikací. Ale vlastní role se dají přiřadit také v oboru objektů. To umožňuje dát nabyvateli oprávnění k aktualizaci přihlašovacích údajů a základních vlastností jedné aplikace bez nutnosti vytvořit druhou vlastní roli.

1. Přihlaste se k [centru pro správu Azure AD](https://aad.portal.azure.com) pomocí oprávnění vývojáře aplikace v organizaci Azure AD.
1. Vyberte **Registrace aplikací**.
1. Vyberte registraci aplikace, ke které udělujete přístup pro správu. Možná budete muset vybrat **všechny aplikace** , abyste viděli úplný seznam registrací aplikací ve vaší organizaci Azure AD.

    ![Vyberte registraci aplikace jako obor prostředku pro přiřazení role.](./media/roles-create-custom/appreg-all-apps.png)

1. V části registrace aplikace vyberte **role a správci**. Pokud jste ho ještě nevytvořili, postupujte podle pokynů v [předchozím postupu](#create-a-new-custom-role-to-grant-access-to-manage-app-registrations).

    Pokud tuto roli přiřadíte z kontextu registrace otevřené aplikace, potom má nabyvatel oprávnění k této registraci jedné aplikace. Role, kterou přiřazujete, se zobrazí v seznamu při každé registraci aplikace. Tento model přístupu, ve kterém je možné přiřadit vlastníkovi oprávnění ke konkrétním prostředkům Azure AD podle rolí, je podobný modelu používanému v [Azure RBAC](../../role-based-access-control/overview.md) pro řízení přístupu k prostředkům Azure.

1. Výběrem role otevřete stránku **přiřazení** .
1. Vyberte **Přidat přiřazení** a přidejte uživatele. Uživateli se neudělí žádná oprávnění pro jinou registraci aplikace než vybraný.

## <a name="create-a-custom-role-using-azure-ad-powershell"></a>Vytvoření vlastní role pomocí Azure AD PowerShellu

### <a name="prepare-powershell"></a>Příprava PowerShellu

Nejdřív je nutné [Stáhnout modul Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/).

K instalaci modulu Azure AD PowerShell použijte následující příkazy:

``` PowerShell
install-module azureadpreview
import-module azureadpreview
```

Chcete-li ověřit, zda je modul připravený k použití, použijte následující příkaz:

``` PowerShell
get-module azuread
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}
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
    "microsoft.directory/applications/allProperties/read",
    "microsoft.directory/applications/basic/update",
    "microsoft.directory/applications/credentials/update"
)
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customAdmin = New-AzureAdRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
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
        {
            "resourceActions":
            {
                "allowedResourceActions":
                [
                    "microsoft.directory/applications/basic/update",
                    "microsoft.directory/applications/credentials/update"
                ]
            },
            "condition":null
        }
    ],
    "templateId":"<GET NEW GUID AND INSERT HERE>",
    "version":"1"
    }
    ```

1. Vytvořte přiřazení role.

    Požadavek HTTP na vytvoření vlastní definice role.

    POST

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions/roleAssignments
    ```

    Tělo

    ``` HTTP
    {
    "principalId":"<GUID OF USER>",
    "roleDefinitionId":"<GUID OF ROLE DEFINITION>",
    "resourceScopes":["/<GUID OF APPLICATION REGISTRATION>"]
    }
    ```

## <a name="next-steps"></a>Další kroky

- Můžete s námi sdílet na [fóru role pro správu Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
- Další informace o rolích a přiřazení rolí správců najdete v tématu [přiřazení rolí správce](directory-assign-admin-roles.md).
- Pro výchozí uživatelská oprávnění si přečtěte téma [Porovnání výchozích oprávnění typu Host a uživatel](../fundamentals/users-default-permissions.md).
