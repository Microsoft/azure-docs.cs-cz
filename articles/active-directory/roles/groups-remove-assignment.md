---
title: Odebrat přiřazení rolí ze skupiny v Azure Active Directory | Microsoft Docs
description: Zobrazte si náhled vlastních rolí Azure AD pro delegování správy identit. Spravujte role Azure v Azure Portal, PowerShellu nebo Graph API.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: article
ms.date: 11/05/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d58465c56be310d3f9d500ab551c7d2b19ffaa83
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2020
ms.locfileid: "93378376"
---
# <a name="remove-role-assignments-from-a-group-in-azure-active-directory"></a>Odebrat přiřazení rolí ze skupiny v Azure Active Directory

Tento článek popisuje, jak může správce IT odebrat role Azure AD přiřazené skupinám. V Azure Portal nyní můžete k uživateli odebrat přímá i nepřímá přiřazení rolí. Pokud je uživateli přiřazena role podle členství ve skupině, odeberte přiřazení role odebráním uživatele ze skupiny.

## <a name="using-azure-admin-center"></a>Použití centra pro správu Azure

1. Přihlaste se do [centra pro správu Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) pomocí oprávnění správce privilegované role nebo globální správce v organizaci Azure AD.

1. Vyberte **role a správci** > * *_název role_* _.

1. Vyberte skupinu, ze které chcete odebrat přiřazení role, a vyberte _ * odebrat přiřazení * *.

   ![Odebere přiřazení role z vybrané skupiny.](./media/groups-remove-assignment/remove-assignment.png)

1. Po zobrazení výzvy k potvrzení akce vyberte **Ano**.

## <a name="using-powershell"></a>Použití PowerShellu

### <a name="create-a-group-that-can-be-assigned-to-role"></a>Vytvoření skupiny, kterou je možné přiřadit roli

```powershell
$group = New-AzureADMSGroup -DisplayName "Contoso_Helpdesk_Administrators" -Description "This group is assigned to Helpdesk Administrator built-in role in Azure AD." -MailEnabled $true -SecurityEnabled $true -MailNickName "contosohelpdeskadministrators" -IsAssignableToRole $true
```

### <a name="get-the-role-definition-you-want-to-assign-the-group-to"></a>Získat definici role, ke které chcete přiřadit skupinu

```powershell
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Helpdesk Administrator'"
```

### <a name="create-a-role-assignment"></a>Vytvoření přiřazení role

```powershell
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope '/' -RoleDefinitionId $roleDefinition.Id -PrincipalId $group.objectId
```

### <a name="remove-the-role-assignment"></a>Odebrání přiřazení role

```powershell
Remove-AzureAdMSRoleAssignment -Id $roleAssignment.Id 
```

## <a name="using-microsoft-graph-api"></a>Používání rozhraní Microsoft Graph API

### <a name="create-a-group-that-can-be-assigned-an-azure-ad-role"></a>Vytvoření skupiny, ke které se dá přiřadit role Azure AD

```powershell
POST https://graph.microsoft.com/beta/groups

{
"description": "This group is assigned to Helpdesk Administrator built-in role of Azure AD",
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

```powershell
GET https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions?$filter = displayName eq ‘Helpdesk Administrator’
```

### <a name="create-the-role-assignment"></a>Vytvoření přiřazení role

```powershell
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
{
"principalId":"<Object Id of Group>",
"roleDefinitionId":"<Id of role definition>",
"directoryScopeId":"/"
}
```

### <a name="delete-role-assignment"></a>Odstranit přiřazení role

```powershell
DELETE https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/<Id of role assignment>
```

## <a name="next-steps"></a>Další kroky

- [Využití cloudových skupin ke správě přiřazení rolí](groups-concept.md)
- [Řešení potíží s rolemi přiřazenými ke cloudovým skupinám](groups-faq-troubleshooting.md)
