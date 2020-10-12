---
title: Přiřazení role ke cloudové skupině v Azure Active Directory | Microsoft Docs
description: Přiřaďte roli Azure AD ke skupině přiřazení role v Azure Portal, PowerShellu nebo Graph API.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/27/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f45b5709369dfc2025b55bc3acec69e9328ce403
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87513413"
---
# <a name="assign-a-role-to-a-cloud-group-in-azure-active-directory"></a>Přiřazení role ke cloudové skupině v Azure Active Directory

Tato část popisuje, jak může správce IT přiřadit roli Azure Active Directory (Azure AD) ke skupině Azure AD.

## <a name="using-azure-ad-admin-center"></a>Použití centra pro správu Azure AD

Přiřazení skupiny k roli Azure AD se podobá přiřazení uživatelů a instančních objektů s výjimkou toho, že je možné použít pouze skupiny, které jsou přiřazovány rolím. V Azure Portal se zobrazí pouze skupiny, které jsou přiřazení rolí.

1. Přihlaste se do [centra pro správu Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) pomocí oprávnění správce privilegované role nebo globální správce v organizaci Azure AD.

1. Vyberte **Azure Active Directory**  >  **role a správci**a vyberte roli, kterou chcete přiřadit.

1. Na stránce ***název role*** vyberte > **Přidat přiřazení**.

   ![Přidat přiřazení nové role](./media/roles-groups-assign-role/add-assignment.png)

1. Vyberte skupinu. Zobrazují se jenom skupiny, které se dají přiřadit k rolím Azure AD.

    [![Pro nové přiřazení role se zobrazí pouze skupiny, které jsou přiřaditelné.](media/roles-groups-assign-role/eligible-groups.png "Pro nové přiřazení role se zobrazí pouze skupiny, které jsou přiřaditelné.")](media/roles-groups-assign-role/eligible-groups.png#lightbox)

1. Vyberte **Přidat**.

Další informace o přiřazování oprávnění role najdete v tématu [přiřazení rolí správců a uživatelů bez oprávnění správce](../fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="using-powershell"></a>Pomocí prostředí PowerShell

### <a name="create-a-group-that-can-be-assigned-to-role"></a>Vytvoření skupiny, kterou je možné přiřadit roli

```powershell
$group = New-AzureADMSGroup -DisplayName "Contoso_Helpdesk_Administrators" -Description "This group is assigned to Helpdesk Administrator built-in role in Azure AD." -MailEnabled $true -SecurityEnabled $true -MailNickName "contosohelpdeskadministrators" -IsAssignableToRole $true 
```

### <a name="get-the-role-definition-for-the-role-you-want-to-assign"></a>Získat definici role pro roli, kterou chcete přiřadit

```powershell
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Helpdesk Administrator'" 
```

### <a name="create-a-role-assignment"></a>Vytvoření přiřazení role

```powershell
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope '/' -RoleDefinitionId $roleDefinition.Id -PrincipalId $group.Id 
```

## <a name="using-microsoft-graph-api"></a>Používání rozhraní Microsoft Graph API

### <a name="create-a-group-that-can-be-assigned-azure-ad-role"></a>Vytvoření skupiny, ke které se dá přiřadit role Azure AD

```
POST https://graph.microsoft.com/beta/groups
{
"description": "This group is assigned to Helpdesk Administrator built-in role of Azure AD.",
"displayName": "Contoso_Helpdesk_Administrators",
"groupTypes": [
"Unified"
],
"mailEnabled": true,
"securityEnabled": true
"mailNickname": "contosohelpdeskadministrators",
"isAssignableToRole": true,
}
```

### <a name="get-the-role-definition"></a>Získání definice role

```
GET https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions?$filter = displayName eq ‘Helpdesk Administrator’
```

### <a name="create-the-role-assignment"></a>Vytvoření přiřazení role

```
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
{
"principalId":"<Object Id of Group>",
"roleDefinitionId":"<ID of role definition>",
"directoryScopeId":"/"
}
```
## <a name="next-steps"></a>Další kroky

- [Použití skupin cloudů ke správě přiřazení rolí](roles-groups-concept.md)
- [Řešení potíží s rolemi přiřazenými ke cloudovým skupinám](roles-groups-faq-troubleshooting.md)
