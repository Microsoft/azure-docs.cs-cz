---
title: Vytvoření vlastních rolí v řízení přístupu založeném na rolích Azure AD | Dokumenty společnosti Microsoft
description: Vytvořte a přiřaďte vlastní role Azure AD s oborem prostředků ve zdrojích Azure Active Directory.
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
ms.openlocfilehash: c2cb19c82f8c19bf87eeef755adb5756b2452512
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74025271"
---
# <a name="create-and-assign-a-custom-role-in-azure-active-directory"></a>Vytvoření a přiřazení vlastní role ve službě Azure Active Directory

Tento článek popisuje, jak vytvořit nové vlastní role ve službě Azure Active Directory (Azure AD). Základní informace o vlastních rolích naleznete v [přehledu vlastních rolí](roles-custom-overview.md). Roli lze přiřadit buď v oboru na úrovni adresáře, nebo pouze v oboru prostředku registrace aplikace.

Vlastní role se můžou vytvářet na kartě [Role a správci](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) na stránce přehledu Azure AD.

## <a name="create-a-role-in-the-azure-portal"></a>Vytvoření role na webu Azure Portal

### <a name="create-a-new-custom-role-to-grant-access-to-manage-app-registrations"></a>Vytvoření nové vlastní role pro udělení přístupu ke správě registrací aplikací

1. Přihlaste se do  [Centra pro správu Azure AD](https://aad.portal.azure.com)s oprávněními správce privilegovaných rolí nebo globálního správce v organizaci Azure AD.
1. Vyberte **Role služby Azure Active Directory** > **a správci** > **Nová vlastní role**.

   ![Vytvoření nebo úprava rolí ze stránky Role a správci](./media/roles-create-custom/new-custom-role.png)

1. Na kartě **Základy** zadejte název a popis role a klepněte na tlačítko **Další**.

   ![Zadejte název a popis vlastní role na kartě Základy](./media/roles-create-custom/basics-tab.png)

1. Na kartě **Oprávnění** vyberte oprávnění potřebná ke správě základních vlastností a vlastností pověření registrací aplikací. Podrobný popis jednotlivých oprávnění najdete [v tématu Podtypy a oprávnění registrace aplikací ve službě Azure Active Directory](./roles-custom-available-permissions.md).
   1. Nejprve zadejte "pověření" do vyhledávacího `microsoft.directory/applications/credentials/update` panelu a vyberte oprávnění.

      ![Výběr oprávnění pro vlastní roli na kartě Oprávnění](./media/roles-create-custom/permissions-tab.png)

   1. Dále zadejte na panelu hledání položku "basic", vyberte `microsoft.directory/applications/basic/update` oprávnění a klepněte na tlačítko **Další**.
1. Na kartě **Revize + vytvořit** zkontrolujte oprávnění a vyberte **Vytvořit**.

Vaše vlastní role se zobrazí v seznamu dostupných rolí, které chcete přiřadit.

## <a name="create-a-role-using-powershell"></a>Vytvoření role pomocí PowerShellu

### <a name="prepare-powershell"></a>Příprava prostředí PowerShell

Nejprve je nutné [stáhnout modul Azure AD Preview PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).

Chcete-li nainstalovat modul Azure AD PowerShell, použijte následující příkazy:

``` PowerShell
install-module azureadpreview
import-module azureadpreview
```

Chcete-li ověřit, zda je modul připraven k použití, použijte následující příkaz:

``` PowerShell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.2.31     azuread                      {Add-AzureADAdministrati...}
```

### <a name="create-the-custom-role"></a>Vytvoření vlastní role

Vytvořte novou roli pomocí následujícího skriptu Prostředí PowerShell:

``` PowerShell
# Basic role information
$displayName = "Application Support Administrator"
$description = "Can manage basic aspects of application registrations."
$templateId = (New-Guid).Guid
 
# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/basic/update",
    "microsoft.directory/applications/credentials/update"
)
$rolePermissions = @{'allowedResourceActions'= $allowedResourceAction}
 
# Create new custom admin role
$customAdmin = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role-using-azure-ad-powershell"></a>Přiřazení vlastní role pomocí Prostředí Azure AD PowerShell

Přiřazení role pomocí níže uvedeného skriptu prostředí PowerShell:

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

## <a name="create-a-role-with-graph-api"></a>Vytvoření role pomocí rozhraní Graph API

1. Vytvořte definici role.

    Požadavek HTTP pro vytvoření vlastní definice role.

    POST

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
    ```

    Tělo

    ``` HTTP
   {
       "description": "Can manage basic aspects of application registrations.",
       "displayName": "Application Support Administrator",
       "isEnabled": true,
       "templateId": "<GUID>",
       "rolePermissions": [
           {
               "allowedResourceActions": [
                   "microsoft.directory/applications/basic/update",
                   "microsoft.directory/applications/credentials/update"
               ]
           }
       ]
   }
    ```

1. Vytvořte přiřazení role.

    Požadavek HTTP pro vytvoření vlastní definice role.

    POST

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
    ```

    Tělo

    ``` HTTP
   {
       "principalId":"<GUID OF USER>",
       "roleDefinitionId":"<GUID OF ROLE DEFINITION>",
       "resourceScope":"/<GUID OF APPLICATION REGISTRATION>"
   }
    ```

## <a name="assign-a-custom-role-scoped-to-a-resource"></a>Přiřazení vlastní role s vymezeným oborem k prostředku

Stejně jako předdefinované role jsou vlastní role ve výchozím nastavení přiřazovány ve výchozím oboru celé organizace, aby udělaly přístupová oprávnění pro všechny registrace aplikací ve vaší organizaci. Ale na rozdíl od předdefinovaných rolí, vlastní role lze také přiřadit v rámci jednoho prostředku Azure AD. To umožňuje udělit uživateli oprávnění k aktualizaci přihlašovacích údajů a základních vlastností jedné aplikace bez nutnosti vytvářet druhou vlastní roli.

1. Přihlaste se do [Centra pro správu Azure AD](https://aad.portal.azure.com) s oprávněními vývojáře aplikací v organizaci Azure AD.
1. Vyberte **Registrace aplikací**.
1. Vyberte registraci aplikace, ke které udělujete přístup ke správě. Možná budete muset vybrat **všechny aplikace,** abyste viděli úplný seznam registrací aplikací ve vaší organizaci Azure AD.

    ![Výběr registrace aplikace jako oboru prostředku pro přiřazení role](./media/roles-create-custom/appreg-all-apps.png)

1. V registraci aplikace vyberte **Role a správce**. Pokud jste jej ještě nevytvořili, pokyny jsou v [předchozím postupu](#create-a-new-custom-role-to-grant-access-to-manage-app-registrations).

1. Vyberte roli, kterou chcete otevřít na stránce **Přiřazení.**
1. Vyberte **Přidat přiřazení,** chcete-li přidat uživatele. Uživateli budou udělena oprávnění pouze k vybrané registraci aplikace.

## <a name="next-steps"></a>Další kroky

- Neváhejte se s námi podělit na [fóru rolí azure ad pro správu](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
- Další informace o rolích a přiřazení rolí správce najdete v tématu [Přiřazení rolí správce](directory-assign-admin-roles.md).
- Výchozí uživatelská oprávnění naleznete v [porovnání výchozích oprávnění hosta a člena uživatele](../fundamentals/users-default-permissions.md).
