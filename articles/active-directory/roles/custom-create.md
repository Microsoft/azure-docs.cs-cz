---
title: Vytváření vlastních rolí v řízení přístupu na základě role Azure AD | Microsoft Docs
description: Vytvořte a přiřaďte vlastní role Azure AD s oborem prostředků u prostředků Azure Active Directory.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 01/05/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e0e1543f18c18c7fdf97c39f35ba38ded658392
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "103007813"
---
# <a name="create-and-assign-a-custom-role-in-azure-active-directory"></a>Vytvoření a přiřazení vlastní role v Azure Active Directory

Tento článek popisuje, jak vytvořit nové vlastní role v Azure Active Directory (Azure AD). Základní informace o vlastních rolích najdete v tématu [Přehled vlastních rolí](custom-overview.md). Role může být přiřazena pouze v oboru na úrovni adresáře nebo v oboru prostředků registrace aplikace.

Vlastní role se dají vytvořit na kartě [role a správci](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) na stránce Přehled Azure AD.

## <a name="create-a-role-in-the-azure-portal"></a>Vytvoření role v Azure Portal

### <a name="create-a-new-custom-role-to-grant-access-to-manage-app-registrations"></a>Vytvoření nové vlastní role pro udělení přístupu pro správu registrací aplikací

1. Přihlaste se do [centra pro správu Azure AD](https://aad.portal.azure.com) pomocí oprávnění správce privilegované role nebo globální správce v organizaci Azure AD.
1. Vyberte **Azure Active Directory**  >  **role a správci**  >  **novou vlastní roli**.

   ![Vytvoření nebo úprava rolí na stránce role a správci](./media/custom-create/new-custom-role.png)

1. Na kartě **základy** zadejte název a popis role a pak klikněte na **Další**.

   ![zadání názvu a popisu vlastní role na kartě základy](./media/custom-create/basics-tab.png)

1. Na kartě **oprávnění** vyberte oprávnění nutná ke správě základních vlastností a vlastností přihlašovacích údajů pro registrace aplikací. Podrobný popis jednotlivých oprávnění naleznete [v tématu typy registrace aplikace a oprávnění v Azure Active Directory](custom-available-permissions.md).
   1. Nejdřív na panelu hledání zadejte "přihlašovací údaje" a vyberte `microsoft.directory/applications/credentials/update` oprávnění.

      ![Na kartě oprávnění vyberte oprávnění pro vlastní roli.](./media/custom-create/permissions-tab.png)

   1. Potom na panelu hledání zadejte "základní", vyberte `microsoft.directory/applications/basic/update` oprávnění a pak klikněte na **Další**.
1. Na kartě **Revize + vytvořit** Zkontrolujte oprávnění a vyberte **vytvořit**.

Vaše vlastní role se zobrazí v seznamu dostupných rolí, které se mají přiřadit.

## <a name="create-a-role-using-powershell"></a>Vytvoření role pomocí prostředí PowerShell

### <a name="prepare-powershell"></a>Příprava PowerShellu

Nejdřív je nutné [Stáhnout modul Azure AD Preview PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).

K instalaci modulu Azure AD PowerShell použijte následující příkazy:

``` PowerShell
Install-Module -Name AzureADPreview 
Import-Module -Name AzureADPreview 
```

Chcete-li ověřit, zda je modul připravený k použití, použijte následující příkaz:

``` PowerShell
Get-Module -Name AzureADPreview 

  ModuleType Version      Name                         ExportedCommands 
  ---------- ---------    ----                         ---------------- 
  Binary     2.0.0.115    AzureADPreview               {Add-AzureADAdministrati...} 
```

### <a name="connect-to-azure"></a>Připojení k Azure

Pokud se chcete připojit k Azure Active Directory, použijte následující příkaz:

``` PowerShell
Connect-AzureAD
```

### <a name="create-the-custom-role"></a>Vytvoření vlastní role

Pomocí následujícího skriptu PowerShellu vytvořte novou roli:

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

### <a name="assign-the-custom-role-using-azure-ad-powershell"></a>Přiřazení vlastní role pomocí Azure AD PowerShellu

Přiřaďte roli pomocí níže uvedeného skriptu prostředí PowerShell:

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

## <a name="create-a-role-with-graph-api"></a>Vytvoření role pomocí Graph API

1. Vytvořte definici role.

    Požadavek HTTP na vytvoření vlastní definice role.

    POST

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
    ```

    Text

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

    > [!Note]
    > `"templateId": "GUID"`Je volitelný parametr, který je odeslán v těle v závislosti na požadavku. Pokud máte požadavek na vytvoření více různých vlastních rolí se společnými parametry, je nejlepší vytvořit šablonu a definovat `templateId` hodnotu. Hodnotu můžete vygenerovat `templateId` předem pomocí rutiny prostředí PowerShell `(New-Guid).Guid` . 

1. Vytvořte přiřazení role.

    Požadavek HTTP na vytvoření vlastní definice role.

    POST

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
    ```

    Text

    ``` HTTP
   {
       "principalId":"<GUID OF USER>",
       "roleDefinitionId":"<GUID OF ROLE DEFINITION>",
       "resourceScope":"/<GUID OF APPLICATION REGISTRATION>"
   }
    ```

## <a name="assign-a-custom-role-scoped-to-a-resource"></a>Přiřazení vlastní role v oboru k prostředku

Podobně jako předdefinované role jsou vlastní role ve výchozím rozsahu přiřazeny ve výchozím oboru pro udělení oprávnění k přístupu ke všem registrům aplikací ve vaší organizaci. Ale na rozdíl od integrovaných rolí se můžou vlastní role přiřadit taky v oboru jednoho prostředku Azure AD. To vám umožní dát uživateli oprávnění aktualizovat přihlašovací údaje a základní vlastnosti pro jednu aplikaci, aniž by museli vytvořit druhou vlastní roli.

1. Přihlaste se k [centru pro správu Azure AD](https://aad.portal.azure.com) pomocí oprávnění vývojáře aplikace v organizaci Azure AD.
1. Vyberte **Registrace aplikací**.
1. Vyberte registraci aplikace, ke které udělujete přístup pro správu. Možná budete muset vybrat **všechny aplikace** , abyste viděli úplný seznam registrací aplikací ve vaší organizaci Azure AD.

    ![Vyberte registraci aplikace jako obor prostředku pro přiřazení role.](./media/custom-create/appreg-all-apps.png)

1. V části registrace aplikace vyberte **role a správci**. Pokud jste ho ještě nevytvořili, postupujte podle pokynů v [předchozím postupu](#create-a-new-custom-role-to-grant-access-to-manage-app-registrations).

1. Výběrem role otevřete stránku **přiřazení** .
1. Vyberte **Přidat přiřazení** a přidejte uživatele. Uživateli se udělí jakákoli oprávnění jenom pro vybranou registraci aplikace.

## <a name="next-steps"></a>Další kroky

- Můžete s námi sdílet na [fóru role pro správu Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
- Další informace o rolích a přiřazení rolí správců najdete v tématu [přiřazení rolí správce](permissions-reference.md).
- Pro výchozí uživatelská oprávnění si přečtěte téma [Porovnání výchozích oprávnění typu Host a uživatel](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2froles%2fcontext%2fugr-context).