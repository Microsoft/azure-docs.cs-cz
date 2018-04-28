---
title: Vytvoření aplikace Azure AD pro přístup k rozhraní API služby Azure Media Services pomocí prostředí PowerShell | Microsoft Docs
description: Zjistěte, jak pomocí prostředí PowerShell k vytvoření aplikace Azure Active Directory (Azure AD) a nastavte jej tak přístup k rozhraní API služby Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: juliako
ms.openlocfilehash: 734f40e7fad993783999dfec00a4213e34da801b
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="use-powershell-to-create-an-azure-ad-app-to-use-with-the-azure-media-services-api"></a>Vytvoření aplikace Azure AD pro použití s rozhraní API služby Azure Media Services pomocí prostředí PowerShell

Další informace o použití skriptu prostředí PowerShell k vytvoření objektu služby a aplikace Azure Active Directory (Azure AD) pro přístup k prostředkům Azure Media Services.  

## <a name="prerequisites"></a>Požadavky

- Účet Azure. Pokud nemáte účet, začínat [bezplatné zkušební verzi Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Účet Media Services. Další informace najdete v tématu [vytvoření účtu Azure Media Services na webu Azure portal](media-services-portal-create-account.md).
- Azure PowerShell verze 0.8.8 nebo vyšší verze. Další informace najdete v tématu [jak pomocí prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
- Rutiny Azure Resource Manager.  

## <a name="create-an-azure-ad-app-by-using-powershell"></a>Vytvoření aplikace Azure AD pomocí prostředí PowerShell  

```powershell
Connect-AzureRmAccount
Import-Module AzureRM.Resources
Set-AzureRmContext -SubscriptionId $SubscriptionId
$ServicePrincipal = New-AzureRMADServicePrincipal -DisplayName $ApplicationDisplayName -Password $Password

Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id 
$NewRole = $null
$Scope = "/subscriptions/your subscription id/resourceGroups/userresourcegroup/providers/microsoft.media/mediaservices/your media account"

$Retries = 0;While ($NewRole -eq $null -and $Retries -le 6)
{
    # Sleep here for a few seconds to allow the service principal application to become active (usually, it will take only a couple of seconds)
    Sleep 15
    New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId -Scope $Scope | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $ServicePrincipal.ApplicationId -ErrorAction SilentlyContinue
    $Retries++;
}
```

Další informace najdete v následujících článcích:

- [Vytvoření instančního objektu pro přístup k prostředkům pomocí Azure PowerShellu](../azure-resource-manager/resource-group-authenticate-service-principal.md)
- [Správa řízení přístupu na základě Role pomocí prostředí Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)
- [Jak ručně nakonfigurovat démon aplikace pomocí certifikátů](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/blob/master/Manual-Configuration-Steps.md#add-the-certificate-as-a-key-for-the-todolistdaemonwithcert-application-in-azure-ad)

## <a name="next-steps"></a>Další postup

Začínáme s [nahrávání souborů do účtu](media-services-portal-upload-files.md).
