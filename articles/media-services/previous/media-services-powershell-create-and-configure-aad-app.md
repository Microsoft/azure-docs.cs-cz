---
title: Použití PowerShellu k vytvoření aplikace Azure AD pro přístup k rozhraní Azure Media Services API | Microsoft Docs
description: Naučte se, jak pomocí PowerShellu vytvořit aplikaci Azure Active Directory (Azure AD) a nastavit ji pro přístup k rozhraní Azure Media Services API.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 2f9568d175d564547e58bc3584174cb0f239dd49
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92425226"
---
# <a name="use-powershell-to-create-an-azure-ad-app-to-use-with-the-azure-media-services-api"></a>Použití PowerShellu k vytvoření aplikace Azure AD pro použití s rozhraním Azure Media Services API

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Do Media Services v2 se nepřidávají žádné nové funkce. <br/>Podívejte se na nejnovější verzi [Media Services V3](../latest/index.yml). Podívejte se taky na [pokyny k migraci z v2 na V3](../latest/migrate-from-v2-to-v3.md) .

Naučte se používat skript prostředí PowerShell k vytvoření aplikace Azure Active Directory (Azure AD) a instančního objektu pro přístup k prostředkům Azure Media Services.  

## <a name="prerequisites"></a>Požadavky

- Účet Azure: Pokud účet nemáte, začněte s [bezplatnou zkušební verzí Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Účet Media Services. Další informace najdete v tématu [Vytvoření účtu Azure Media Services v Azure Portal](media-services-portal-create-account.md).

- Azure Powershell Další informace najdete v tématu [Jak používat Azure PowerShell](/powershell/azure/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="create-an-azure-ad-app-by-using-powershell"></a>Vytvoření aplikace Azure AD pomocí prostředí PowerShell  

```powershell
Connect-AzAccount
Import-Module Az.Resources
Set-AzContext -SubscriptionId $SubscriptionId
$ServicePrincipal = New-AzADServicePrincipal -DisplayName $ApplicationDisplayName -Password $Password

Get-AzADServicePrincipal -ObjectId $ServicePrincipal.Id 
$NewRole = $null
$Scope = "/subscriptions/your subscription id/resourceGroups/userresourcegroup/providers/microsoft.media/mediaservices/your media account"

$Retries = 0;While ($NewRole -eq $null -and $Retries -le 6)
{
    # Sleep here for a few seconds to allow the service principal application to become active (usually, it will take only a couple of seconds)
    Sleep 15
    New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId -Scope $Scope | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzRoleAssignment -ServicePrincipalName $ServicePrincipal.ApplicationId -ErrorAction SilentlyContinue
    $Retries++;
}
```

Další informace najdete v následujících článcích:

- [Vytvoření instančního objektu pro přístup k prostředkům pomocí Azure PowerShellu](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)
- [Přidání nebo odebrání přiřazení rolí v Azure pomocí Azure PowerShellu](../../role-based-access-control/role-assignments-powershell.md)
- [Ruční konfigurace aplikací démona pomocí certifikátů](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2)

## <a name="next-steps"></a>Další kroky

Začněte s [nahráváním souborů na svůj účet](media-services-portal-upload-files.md).
