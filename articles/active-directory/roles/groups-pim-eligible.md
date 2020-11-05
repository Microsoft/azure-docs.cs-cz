---
title: Přiřazení role ke skupině pomocí Privileged Identity Management ve službě Azure AD | Microsoft Docs
description: Přečtěte si, jak můžete přiřadit roli Azure Active Directory (Azure AD) ke skupině pomocí Azure AD Privileged Identity Management (PIM).
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
ms.openlocfilehash: 74ad503374e0148e9813508b6c7f8b21e2dca7a3
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2020
ms.locfileid: "93379158"
---
# <a name="assign-a-role-to-a-group-using-privileged-identity-management"></a>Přiřazení role ke skupině pomocí Privileged Identity Management

Tento článek popisuje, jak můžete přiřadit roli Azure Active Directory (Azure AD) ke skupině pomocí Azure AD Privileged Identity Management (PIM).

> [!NOTE]
> Abyste mohli přiřadit skupinu k roli Azure AD pomocí PIM, musíte používat aktualizovanou verzi Privileged Identity Management. Pokud vaše organizace Azure AD využívá rozhraní Privileged Identity Management API, může být na starší verzi PIM. Pokud ano, přečtěte si prosím alias pim_preview@microsoft.com a přesuňte svoji organizaci a aktualizujte své rozhraní API. Další informace najdete v informacích o [rolích a funkcích Azure AD v PIM](../privileged-identity-management/azure-ad-roles-features.md).

## <a name="using-azure-ad-admin-center"></a>Použití centra pro správu Azure AD

1. Přihlaste se k [Azure AD Privileged Identity Management](https://ms.portal.azure.com/?Microsoft_AAD_IAM_GroupRoles=true&Microsoft_AAD_IAM_userRolesV2=true&Microsoft_AAD_IAM_enablePimIntegration=true#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) jako správce privilegované role nebo globální správce ve vaší organizaci.

1. Vyberte **Privileged Identity Management**  >  **role Azure AD** role  >  **Roles**  >  **Přidat přiřazení** .

1. Vyberte roli a potom vyberte skupinu. Zobrazují se jenom skupiny, které mají nárok na přiřazení role (skupiny s přiřazením rolí), ne všechny skupiny.

    ![Snímek obrazovky zobrazující stránku přidat přiřazení s zvýrazněnými oddíly vybrat roli a vybrat členy](./media/groups-pim-eligible/select-member.png)

1. Vyberte požadované nastavení členství. V případě rolí vyžadujících aktivaci vyberte možnost **způsobilý**. Ve výchozím nastavení by měl být uživatel trvale způsobilý, ale můžete také nastavit počáteční a koncový čas pro nárok uživatele. Po dokončení klikněte na Uložit a přidat a dokončete přiřazení role.

    ![Vyberte uživatele, kterému přiřadíte roli.](./media/groups-pim-eligible/set-assignment-settings.png)

## <a name="using-powershell"></a>Použití PowerShellu

### <a name="download-the-azure-ad-preview-powershell-module"></a>Stažení modulu Azure AD Preview PowerShell

Pokud chcete nainstalovat modul #PowerShell služby Azure AD, použijte následující rutiny:

```powershell
install-module azureadpreview
import-module azureadpreview
```

Chcete-li ověřit, zda je modul připravený k použití, použijte následující rutinu:

```powershell
get-module azureadpreview
```

### <a name="assign-a-group-as-an-eligible-member-of-a-role"></a>Přiřazení skupiny jako oprávněného člena role

```powershell
$schedule = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedSchedule
$schedule.Type = "Once"
$schedule.StartDateTime = "2019-04-26T20:49:11.770Z"
$schedule.endDateTime = "2019-07-25T20:49:11.770Z"
Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId aadRoles -Schedule $schedule -ResourceId "[YOUR TENANT ID]" -RoleDefinitionId "9f8c1837-f885-4dfd-9a75-990f9222b21d" -SubjectId "[YOUR GROUP ID]" -AssignmentState "Eligible" -Type "AdminAdd"
```

## <a name="using-microsoft-graph-api"></a>Používání rozhraní Microsoft Graph API

```powershell
POST
https://graph.microsoft.com/beta/privilegedAccess/aadroles/roleAssignmentRequests  

{

 "roleDefinitionId": {roleDefinitionId},

 "resourceId": {tenantId},

 "subjectId": {GroupId},

 "assignmentState": "Eligible",

 "type": "AdminAdd",

 "reason": "reason string",

 "schedule": {

   "startDateTime": {DateTime},

   "endDateTime": {DateTime},

   "type": "Once"

 }

}
```

## <a name="next-steps"></a>Další kroky

- [Využití cloudových skupin ke správě přiřazení rolí](groups-concept.md)
- [Řešení potíží s rolemi přiřazenými ke cloudovým skupinám](groups-faq-troubleshooting.md)
- [Konfigurace nastavení role správce Azure AD v Privileged Identity Management](../privileged-identity-management/pim-how-to-change-default-settings.md)
- [Přiřazení rolí prostředků Azure v Privileged Identity Management](../privileged-identity-management/pim-resource-roles-assign-roles.md)
