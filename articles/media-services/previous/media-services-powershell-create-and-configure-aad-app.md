---
title: Použití Powershellu k vytvoření aplikace Azure AD pro přístup k rozhraní API služby Azure Media Services | Dokumentace Microsoftu
description: Další informace o použití Powershellu k vytvoření aplikace Azure Active Directory (Azure AD) a nastavte ho až přístup k rozhraní API služby Azure Media Services.
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
ms.date: 10/29/2018
ms.author: juliako
ms.openlocfilehash: bd7938fb600a1a0cd65b89fde6ca25135306b76d
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50232638"
---
# <a name="use-powershell-to-create-an-azure-ad-app-to-use-with-the-azure-media-services-api"></a>Použití Powershellu k vytvoření aplikace Azure AD pomocí rozhraní API služby Azure Media Services

Zjistěte, jak pomocí skriptu prostředí PowerShell pro vytvoření aplikace Azure Active Directory (Azure AD) a instanční objekt služby pro přístup k prostředkům Azure Media Services.  

## <a name="prerequisites"></a>Požadavky

- Účet Azure. Pokud účet nemáte, začněte tématem [bezplatnou zkušební verzi Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Účet Media Services. Další informace najdete v tématu [vytvoření účtu Azure Media Services na webu Azure Portal](media-services-portal-create-account.md).
- Azure PowerShell verze 0.8.8 nebo vyšší verze. Další informace najdete v tématu [jak pomocí prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
- Rutiny Azure Resource Manageru.  

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

- [Vytvoření instančního objektu pro přístup k prostředkům pomocí Azure PowerShellu](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)
- [Správa řízení přístupu na základě Role pomocí Azure Powershellu](../../role-based-access-control/role-assignments-powershell.md)
- [Postup při ruční konfiguraci aplikace démonů pomocí certifikátů](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/blob/master/Manual-Configuration-Steps.md#add-the-certificate-as-a-key-for-the-todolistdaemonwithcert-application-in-azure-ad)

## <a name="next-steps"></a>Další postup

Začínáme s [nahrávání souborů do účtu](media-services-portal-upload-files.md).
