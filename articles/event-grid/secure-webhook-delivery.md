---
title: Zabezpečené doručování WebHooku pomocí Azure AD v Azure Event Grid
description: Popisuje, jak doručit události do koncových bodů HTTPS chráněných službou Azure Active Directory pomocí služby Azure Event Grid.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: babanisa
ms.openlocfilehash: 074378668b0516936e11968ea8c800d3daa667bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74931540"
---
# <a name="publish-events-to-azure-active-directory-protected-endpoints"></a>Publikování událostí do koncových bodů chráněných službou Azure Active Directory

Tento článek popisuje, jak využít azure active directory k zabezpečení připojení mezi odběr událostí a koncový bod webhooku. Přehled aplikací Azure AD a instancí najdete v tématu [Microsoft identity platformy (v2.0) přehled](https://docs.microsoft.com/azure/active-directory/develop/v2-overview).

Tento článek používá portál Azure pro demonstraci, ale funkce může být také povolena pomocí rozhraní příkazového příkazu, prostředí PowerShell nebo sad SDK.

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="create-an-azure-ad-application"></a>Vytvoření aplikace Azure AD

Začněte vytvořením aplikace Azure AD pro váš chráněný koncový bod. Viz třída https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview.
    - Nakonfigurujte chráněné rozhraní API tak, aby bylo voláno aplikací pro daemon.
    
## <a name="enable-event-grid-to-use-your-azure-ad-application"></a>Povolení funkce Event Grid k používání aplikace Azure AD

Pomocí skriptu Prostředí PowerShell níže k vytvoření role a princip utržení služby ve vaší aplikaci Azure AD. Budete potřebovat ID klienta a ID objektu z vaší aplikace Azure AD:

    > [!NOTE]
    > You must be a member of the [Azure AD Application Administrator role](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#available-roles) to execute this script.
    
1. Upravte $myTenantId skriptu PowerShellu tak, aby používali ID klienta Azure AD.
1. Úprava $myAzureADApplicationObjectId skriptu PowerShellu tak, aby používali ID objektu aplikace Azure AD
1. Spusťte upravený skript.

```PowerShell
# This is your Tenant Id. 
$myTenantId = "<the Tenant Id of your Azure AD Application>"

Connect-AzureAD -TenantId $myTenantId
    
# This is your Azure AD Application's ObjectId. 
$myAzureADApplicationObjectId = "<the Object Id of your Azure AD Application>"
    
# This is the "Azure Event Grid" Azure Active Directory AppId
$eventGridAppId = "4962773b-9cdb-44cf-a8bf-237846a00ab7"
    
# This is the name of the new role we will add to your Azure AD Application
$eventGridRoleName = "AzureEventGridSecureWebhook"
    
# Create an application role of given name and description
Function CreateAppRole([string] $Name, [string] $Description)
{
    $appRole = New-Object Microsoft.Open.AzureAD.Model.AppRole
    $appRole.AllowedMemberTypes = New-Object System.Collections.Generic.List[string]
    $appRole.AllowedMemberTypes.Add("Application");
    $appRole.DisplayName = $Name
    $appRole.Id = New-Guid
    $appRole.IsEnabled = $true
    $appRole.Description = $Description
    $appRole.Value = $Name;
    return $appRole
}
    
# Get my Azure AD Application, it's roles and service principal
$myApp = Get-AzureADApplication -ObjectId $myAzureADApplicationObjectId
$myAppRoles = $myApp.AppRoles
$eventGridSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $eventGridAppId + "'")

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
    
# Create the role if it doesn't exist
if ($myAppRoles -match $eventGridRoleName)
{
    Write-Host "The Azure Event Grid role is already defined.`n"
}
else
{
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
    # Add our new role to the Azure AD Application
    $newRole = CreateAppRole -Name $eventGridRoleName -Description "Azure Event Grid Role"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}
    
# Create the service principal if it doesn't exist
if ($eventGridSP -match "Microsoft.EventGrid")
{
    Write-Host "The Service principal is already defined.`n"
}
else
{
    # Create a service principal for the "Azure Event Grid" Azure AD Application and add it to the role
    $eventGridSP = New-AzureADServicePrincipal -AppId $eventGridAppId
}
    
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventGridSP.ObjectId -PrincipalId $eventGridSP.ObjectId
    
Write-Host "My Azure AD Tenant Id" + $myTenantId
Write-Host "My Azure AD Application Id" + $myAzureADApplicationObjectId
Write-Host "My Azure AD Application ($myApp.ObjectId): " + $myApp.ObjectId
Write-Host "My Azure AD Application's Roles"
Write-Host $myApp.AppRoles
```
    
## <a name="configure-the-event-subscription"></a>Konfigurace předplatného události

V toku vytváření pro odběr událostí vyberte typ koncového bodu 'Web Hook'. Jakmile zadáte identifikátor URI koncového bodu, klikněte na kartu Další funkce v horní části okna vytvořit odběry událostí.

![Vybrat webhook u koncového bodu](./media/secure-webhook-delivery/select-webhook.png)

Na kartě Další funkce zaškrtněte políčko Použít ověřování AAD a nakonfigurujte ID klienta a ID aplikace:

* Zkopírujte ID klienta Azure AD z výstupu skriptu a zadejte ho do pole ID klienta AAD.
* Zkopírujte ID aplikace Azure AD z výstupu skriptu a zadejte ho do pole ID aplikace AAD.

    ![Akce Zabezpečené webové háky](./media/secure-webhook-delivery/aad-configuration.png)

## <a name="next-steps"></a>Další kroky

* Informace o dodávkách událostí monitorování naleznete v [tématu Sledování doručování zpráv v programu Event Grid](monitor-event-delivery.md).
* Další informace o ověřovacím klíči naleznete v tématu [Zabezpečení a ověřování mřížky událostí](security-authentication.md).
* Další informace o vytvoření předplatného Služby Azure Event Grid najdete v [tématu schéma předplatného služby Event Grid](subscription-creation-schema.md).
