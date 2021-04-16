---
title: Vytváření vlastních rolí pro správu podnikových aplikací v Azure Active Directory
description: Vytvořte a přiřaďte vlastní role Azure AD pro přístup k podnikovým aplikacím v Azure Active Directory
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 04/14/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a7c04afe76ced0abf40abf8e30362005fb269172
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534722"
---
# <a name="create-custom-roles-to-manage-enterprise-apps-in-azure-active-directory"></a>Vytváření vlastních rolí pro správu podnikových aplikací v Azure Active Directory

Tento článek vysvětluje, jak vytvořit vlastní roli s oprávněními ke správě přiřazení podnikových aplikací pro uživatele a skupiny v Azure Active Directory (Azure AD). Pro prvky přiřazení rolí a význam termínů, jako je například podtyp, oprávnění a sada vlastností, se podívejte na [Přehled vlastních rolí](custom-overview.md).

## <a name="enterprise-app-role-permissions"></a>Oprávnění role podnikových aplikací

V tomto článku jsou popsána dvě oprávnění podnikových aplikací. Všechny příklady používají oprávnění aktualizace.

* Chcete-li číst přiřazení uživatelů a skupin v oboru, udělte `microsoft.directory/servicePrincipals/appRoleAssignedTo/read` oprávnění
* Chcete-li spravovat přiřazení uživatelů a skupin v oboru, udělte `microsoft.directory/servicePrincipals/appRoleAssignedTo/update` oprávnění

Udělení oprávnění k aktualizaci má za následek, že by nabyvatel mohl spravovat přiřazení uživatelů a skupin k podnikovým aplikacím. Rozsah přiřazení uživatelů nebo skupin lze udělit pro jednu aplikaci nebo udělenou pro všechny aplikace. Pokud je udělená na úrovni celé organizace, může zmocněnec spravovat přiřazení všech aplikací. Pokud je tato možnost provedena na úrovni aplikace, může nabyvatel spravovat přiřazení pouze pro zadanou aplikaci.

Udělení oprávnění k aktualizaci se provádí ve dvou krocích:

1. Vytvoření vlastní role s oprávněním `microsoft.directory/servicePrincipals/appRoleAssignedTo/update`
1. Udělte uživatelům nebo skupinám oprávnění ke správě přiřazení uživatelů a skupin k podnikovým aplikacím. To je, když můžete nastavit obor na úroveň v rámci organizace nebo na jednu aplikaci.

## <a name="use-the-azure-ad-admin-center"></a>Použití centra pro správu Azure AD

### <a name="create-a-new-custom-role"></a>Vytvoření nové vlastní role

>[!NOTE]
> Vlastní role se vytvářejí a spravují na úrovni celé organizace a jsou dostupné jenom ze stránky přehled organizace.

1. Přihlaste se do [centra pro správu Azure AD](https://aad.portal.azure.com) pomocí oprávnění správce privilegované role nebo globální správce ve vaší organizaci.
1. Vyberte **Azure Active Directory**, vyberte **role a správci** a pak vyberte **Nová vlastní role**.

    ![Přidání nové vlastní role ze seznamu rolí v Azure AD](./media/custom-enterprise-apps/new-custom-role.png)

1. Na kartě **základy** poskytněte "spravovat přiřazení uživatelů a skupin" pro název role a "udělit oprávnění ke správě přiřazení uživatelů a skupin" pro popis role a pak vyberte **Další**.

    ![Zadejte název a popis vlastní role.](./media/custom-enterprise-apps/role-name-and-description.png)

1. Na kartě **oprávnění** do vyhledávacího pole zadejte Microsoft. Directory/ServicePrincipals/appRoleAssignedTo/Update a potom zaškrtněte políčka vedle požadovaných oprávnění a pak vyberte **Další**.

    ![Přidání oprávnění k vlastní roli](./media/custom-enterprise-apps/role-custom-permissions.png)

1. Na kartě **Revize + vytvořit** Zkontrolujte oprávnění a vyberte **vytvořit**.

    ![Nyní můžete vytvořit vlastní roli.](./media/custom-enterprise-apps/role-custom-create.png)

### <a name="assign-the-role-to-a-user-using-the-azure-ad-portal"></a>Přiřazení role uživateli pomocí portálu Azure AD

1. Přihlaste se k [centru pro správu Azure AD](https://aad.portal.azure.com) pomocí oprávnění role správce privilegovaných rolí.
1. Vyberte **Azure Active Directory** a pak vyberte **role a správci**.
1. Vyberte **udělit oprávnění ke správě role přiřazení uživatelů a skupin** .

    ![Otevřete role a správce a vyhledejte vlastní roli.](./media/custom-enterprise-apps/select-custom-role.png)

1. Vyberte **Přidat přiřazení**, vyberte požadovaného uživatele a pak kliknutím na **Vybrat** přidejte k uživateli přiřazení role.

    ![Přidání přiřazení pro vlastní roli uživateli](./media/custom-enterprise-apps/assign-user-to-role.png)

#### <a name="assignment-tips"></a>Tipy pro přiřazení

* Pokud chcete udělit oprávnění pro assignees ke správě uživatelů a skupin přístupu ke všem podnikovým aplikacím v celé organizaci, začněte od seznamu **rolí a správců** pro celou organizaci na stránce **přehledu** Azure AD pro vaši organizaci.
* Pokud chcete udělit oprávnění assignees ke správě přístupu uživatelů a skupin pro konkrétní podnikovou aplikaci, přejděte do této aplikace v Azure AD a otevřete ji v seznamu **role a správci** pro danou aplikaci. Vyberte novou vlastní roli a dokončete přiřazení uživatele nebo skupiny. Assignees může spravovat uživatele a přístup skupin jenom pro konkrétní aplikaci.
* Pokud chcete otestovat přiřazení vlastní role, přihlaste se jako pověřená osoba a otevřete stránku **Uživatelé a skupiny** aplikace a ověřte, že je povolená možnost **Přidat uživatele** .

    ![Ověření uživatelských oprávnění](./media/custom-enterprise-apps/verify-user-permissions.png)

## <a name="use-azure-ad-powershell"></a>Použití Azure AD PowerShellu

Další podrobnosti najdete v tématu [Vytvoření a přiřazení vlastní role](custom-create.md) a [přiřazení vlastních rolí k oboru prostředků pomocí PowerShellu](custom-assign-powershell.md).

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

### <a name="create-a-custom-role"></a>Vytvoření vlastní role

Pomocí následujícího skriptu PowerShellu vytvořte novou roli:

```PowerShell
# Basic role information
$description = "Manage user and group assignments"
$displayName = "Can manage user and group assignments for Applications"
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction = @("microsoft.directory/servicePrincipals/appRoleAssignedTo/update")
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customRole = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role"></a>Přiřazení vlastní role

Přiřaďte roli pomocí tohoto skriptu PowerShellu.

```powershell
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'chandra@example.com'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Manage user and group assignments'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'My Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

## <a name="use-the-microsoft-graph-api"></a>Použití rozhraní Microsoft Graph API

Pomocí poskytnutého příkladu v rozhraní Microsoft Graph API vytvořte vlastní roli. Další podrobnosti najdete v tématu [Vytvoření a přiřazení vlastní role](custom-create.md) a [přiřazení vlastních rolí správce pomocí rozhraní Microsoft Graph API](custom-assign-graph.md).

Požadavek HTTP na vytvoření vlastní role

```HTTP
POST
https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitionsIsEnabled $true
{
    "description":"Can manage user and group assignments for Applications.",
    "displayName":" Manage user and group assignments",
    "isEnabled":true,
    "rolePermissions":
    [
        {
            "resourceActions":
            {
                "allowedResourceActions":
                [
                    "microsoft.directory/servicePrincipals/appRoleAssignedTo/update"
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

Přiřazení role kombinuje ID objektu zabezpečení (může to být uživatel nebo instanční objekt), ID definice role a obor prostředku služby Azure AD. Další informace o prvcích přiřazení role najdete v tématu [Přehled vlastních rolí](custom-overview.md) .

Požadavek HTTP na přiřazení vlastní role

```HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments

{
    "principalId":"<PROVIDE OBJECTID OF USER TO ASSIGN HERE>",
    "roleDefinitionId":"<PROVIDE OBJECTID OF ROLE DEFINITION HERE>",
    "resourceScopes":["/"]
}
```

## <a name="next-steps"></a>Další kroky

* [Prozkoumat dostupná vlastní oprávnění role pro podnikové aplikace](custom-enterprise-app-permissions.md)
