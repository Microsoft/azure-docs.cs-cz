---
title: Zobrazit role přiřazené ke skupině v Azure Active Directory | Microsoft Docs
description: Zobrazte si náhled vlastních rolí Azure AD pro delegování správy identit. Spravujte role Azure v Azure Portal, PowerShellu nebo Graph API.
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
ms.openlocfilehash: 3b7613fb70299a70e4389b97c2647a26cb7c3374
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2020
ms.locfileid: "87476013"
---
# <a name="view-roles-assigned-to-a-group-in-azure-active-directory"></a>Zobrazit role přiřazené ke skupině v Azure Active Directory

Tato část popisuje, jak můžete zobrazit role přiřazené ke skupině pomocí centra pro správu Azure AD. Zobrazení skupin a přiřazených rolí jsou výchozí uživatelská oprávnění.

1. Přihlaste se k [centru pro správu Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) pomocí jakýchkoli přihlašovacích údajů bez oprávnění správce nebo správce.

1. Vyberte skupinu, které vás zajímá.

1. Vyberte **přiřazené role**. Teď můžete zobrazit všechny role Azure AD přiřazené k této skupině.

   ![Zobrazit všechny role přiřazené k vybrané skupině](./media/roles-groups-view-assignments/view-assignments.png)

## <a name="using-powershell"></a>Pomocí prostředí PowerShell

### <a name="get-object-id-of-the-group"></a>Získat ID objektu skupiny

```powershell
Get-AzureADMSGroup -SearchString “Contoso_Helpdesk_Administrators”
```

### <a name="view-role-assignment-to-a-group"></a>Zobrazení přiřazení role ke skupině

```powershell
Get-AzureADMSRoleAssignment -Filter "principalId eq '<object id of group>" 
```

## <a name="using-microsoft-graph-api"></a>Používání rozhraní Microsoft Graph API

### <a name="get-object-id-of-the-group"></a>Získat ID objektu skupiny

```powershell
GET https://graph.microsoft.com/beta/groups?$filter displayName eq ‘Contoso_Helpdesk_Administrator’ 
```

### <a name="get-role-assignments-to-a-group"></a>Získá přiřazení rolí ke skupině.

```powershell
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments?$filter=principalId eq
```

## <a name="next-steps"></a>Další kroky

- [Použití skupin cloudů ke správě přiřazení rolí](roles-groups-concept.md)
- [Řešení potíží s rolemi přiřazenými ke skupinám cloudu](roles-groups-faq-troubleshooting.md)
