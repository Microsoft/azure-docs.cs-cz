---
title: Zobrazit role přiřazené ke skupině v Azure Active Directory | Microsoft Docs
description: Přečtěte si, jak můžete zobrazit role přiřazené ke skupině pomocí centra pro správu Azure AD. Zobrazení skupin a přiřazených rolí jsou výchozí uživatelská oprávnění.
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
ms.openlocfilehash: e578c90a05085df33c2d547402256cfc38229aa3
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92375955"
---
# <a name="view-roles-assigned-to-a-group-in-azure-active-directory"></a>Zobrazit role přiřazené ke skupině v Azure Active Directory

Tato část popisuje, jak můžete zobrazit role přiřazené ke skupině pomocí centra pro správu Azure AD. Zobrazení skupin a přiřazených rolí jsou výchozí uživatelská oprávnění.

1. Přihlaste se k [centru pro správu Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) pomocí jakýchkoli přihlašovacích údajů bez oprávnění správce nebo správce.

1. Vyberte skupinu, které vás zajímá.

1. Vyberte **přiřazené role**. Teď můžete zobrazit všechny role Azure AD přiřazené k této skupině.

   ![Zobrazit všechny role přiřazené k vybrané skupině](./media/groups-view-assignments/view-assignments.png)

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

- [Použití skupin cloudů ke správě přiřazení rolí](groups-concept.md)
- [Řešení potíží s rolemi přiřazenými ke cloudovým skupinám](groups-faq-troubleshooting.md)
