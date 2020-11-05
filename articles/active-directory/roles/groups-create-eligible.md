---
title: Vytvořte skupinu pro přiřazování rolí v Azure Active Directory | Microsoft Docs
description: Naučte se vytvořit skupinu s přiřazením rolí ve službě Azure AD. Spravujte role Azure v Azure Portal, PowerShellu nebo Graph API.
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
ms.openlocfilehash: bc2c42f407ee31a792a6634a813358d428120b46
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2020
ms.locfileid: "93378614"
---
# <a name="create-a-role-assignable-group-in-azure-active-directory"></a>Vytvoření skupiny s přiřazením rolí v Azure Active Directory

Roli můžete přiřadit jenom skupině, která byla vytvořená s vlastností ' isAssignableToRole ' nastavenou na hodnotu true nebo byla vytvořena na portálu Azure AD s **rolemi služby Azure AD, kterou je možné přiřadit do skupiny** zapnuté. Tento atribut skupiny vytvoří skupinu, která může být přiřazena roli v Azure Active Directory (Azure AD). Tento článek popisuje, jak vytvořit tento speciální druh skupiny. **Poznámka:** Skupina s vlastností isAssignableToRole nastavenou na hodnotu true nemůže být dynamickým typem členství. Další informace najdete v tématu [použití skupiny ke správě přiřazení rolí Azure AD](groups-concept.md).

## <a name="using-azure-ad-admin-center"></a>Použití centra pro správu Azure AD

1. Přihlaste se do [centra pro správu Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) pomocí oprávnění správce privilegované role nebo globální správce v organizaci Azure AD.
1. Vyberte **skupiny**  >  **všechny skupiny**  >  **Nová skupina**.

    [![Otevřete Azure Active Directory a vytvořte novou skupinu.](./media/groups-create-eligible/new-group.png "Otevřete Azure Active Directory a vytvořte novou skupinu.")](./media/groups-create-eligible/new-group.png#<lightbox>)

1. Na kartě **Nová skupina** zadejte typ skupiny, název a popis.
1. **Role Azure AD se dají přiřadit ke skupině**. Tento přepínač je viditelný jenom pro správce privilegovaných rolí a globální správce, protože se jedná jenom o dvě role, které můžou přepínač nastavit.

    [![Nastavit novou skupinu jako způsobilou pro přiřazení role](./media/groups-create-eligible/eligible-switch.png "Nastavit novou skupinu jako způsobilou pro přiřazení role")](./media/groups-create-eligible/eligible-switch.png#<lightbox>)

1. Vyberte členy a vlastníky skupiny. Máte také možnost přiřadit role ke skupině, ale nepožaduje se zde přiřazení role.

    [![Přidejte členy do skupiny role-přiřazení a přiřaďte role.](./media/groups-create-eligible/specify-members.png "Přidejte členy do skupiny role-přiřazení a přiřaďte role.")](./media/groups-create-eligible/specify-members.png#<lightbox>)

1. Po zadání členů a vlastníků vyberte **vytvořit**.

    [![Tlačítko vytvořit je v dolní části stránky.](./media/groups-create-eligible/create-button.png "Tlačítko vytvořit je v dolní části stránky.")](./media/groups-create-eligible/create-button.png#<lightbox>)

Tato skupina se vytvoří se všemi rolemi, které jste jim mohli přiřadit.

## <a name="using-powershell"></a>Použití PowerShellu

### <a name="install-the-azure-ad-preview-module"></a>Instalace modulu Azure AD Preview

```powershell
install-module azureadpreview
import-module azureadpreview
```

Chcete-li ověřit, zda je modul připravený k použití, vydejte následující příkaz:

```powershell
get-module azureadpreview
```

### <a name="create-a-group-that-can-be-assigned-to-role"></a>Vytvoření skupiny, kterou je možné přiřadit roli

```powershell
$group = New-AzureADMSGroup -DisplayName "Contoso_Helpdesk_Administrators" -Description "This group is assigned to Helpdesk Administrator built-in role in Azure AD." -MailEnabled $true -SecurityEnabled $true -MailNickName "contosohelpdeskadministrators" -IsAssignableToRole $true
```

Pro tento typ skupiny `isPublic` bude vždycky false a `isSecurityEnabled` bude vždycky true.

### <a name="copy-one-groups-users-and-service-principals-into-a-role-assignable-group"></a>Kopírování uživatelů a instančních objektů jedné skupiny do skupiny s přiřazením rolí

```powershell
#Basic set up
install-module azureadpreview
import-module azureadpreview
get-module azureadpreview

#Connect to Azure AD. Sign in as Privileged Role Administrator or Global Administrator. Only these two roles can create a role-assignable group.
Connect-AzureAD

#Input variabled: Existing group
$idOfExistingGroup = "14044411-d170-4cb0-99db-263ca3740a0c"

#Input variables: New role-assignable group
$groupName = "Contoso_Bellevue_Admins"
$groupDescription = "This group is assigned to Helpdesk Administrator built-in role in Azure AD."
$mailNickname = "contosobellevueadmins"

#Create new security group which is a role assignable group. For creating a Microsoft 365 group, set GroupTypes="Unified" and MailEnabled=$true
$roleAssignablegroup = New-AzureADMSGroup -DisplayName $groupName -Description $groupDescription -MailEnabled $false -MailNickname $mailNickname -SecurityEnabled $true -IsAssignableToRole $true

#Get details of existing group
$existingGroup = Get-AzureADMSGroup -Id $idOfExistingGroup
$membersOfExistingGroup = Get-AzureADGroupMember -ObjectId $existingGroup.Id

#Copy users and service principals from existing group to new group
foreach($member in $membersOfExistingGroup){
if($member.ObjectType -eq 'User' -or $member.ObjectType -eq 'ServicePrincipal'){
Add-AzureADGroupMember -ObjectId $roleAssignablegroup.Id -RefObjectId $member.ObjectId
}
}
```

## <a name="using-microsoft-graph-api"></a>Používání rozhraní Microsoft Graph API

### <a name="create-a-role-assignable-group-in-azure-ad"></a>Vytvoření skupiny s přiřazením role v Azure AD

```powershell
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

Pro tento typ skupiny `isPublic` bude vždycky false a `isSecurityEnabled` bude vždycky true.

## <a name="next-steps"></a>Další kroky

- [Přiřazení role ke cloudové skupině](groups-assign-role.md)
- [Využití cloudových skupin ke správě přiřazení rolí](groups-concept.md)
- [Řešení potíží s rolemi přiřazenými ke cloudovým skupinám](groups-faq-troubleshooting.md)
