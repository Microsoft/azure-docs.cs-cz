---
title: Zobrazit role přiřazené ke skupině v Azure Active Directory | Microsoft Docs
description: Přečtěte si, jak můžete zobrazit role přiřazené ke skupině pomocí centra pro správu Azure AD. Zobrazení skupin a přiřazených rolí jsou výchozí uživatelská oprávnění.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: article
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7dac23897f8a17c9adb4ae78736a6a8afa85a18b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103011999"
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
Get-AzureADMSGroup -SearchString "Contoso_Helpdesk_Administrators"
```

### <a name="view-role-assignment-to-a-group"></a>Zobrazení přiřazení role ke skupině

```powershell
Get-AzureADMSRoleAssignment -Filter "principalId eq '<object id of group>" 
```

## <a name="using-microsoft-graph-api"></a>Používání rozhraní Microsoft Graph API

### <a name="get-object-id-of-the-group"></a>Získat ID objektu skupiny

```http
GET https://graph.microsoft.com/beta/groups?$filter=displayName+eq+'Contoso_Helpdesk_Administrator'
```

### <a name="get-role-assignments-to-a-group"></a>Získá přiřazení rolí ke skupině.

```http
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments?$filter=principalId eq
```

## <a name="next-steps"></a>Další kroky

- [Využití cloudových skupin ke správě přiřazení rolí](groups-concept.md)
- [Řešení potíží s rolemi přiřazenými ke cloudovým skupinám](groups-faq-troubleshooting.md)
