---
title: Zabezpečené doručování webhooků pomocí Azure AD v Azure Event Grid
description: Popisuje, jak doručovat události do koncových bodů HTTPS chráněných pomocí Azure Active Directory pomocí Azure Event Grid
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: babanisa
ms.openlocfilehash: 9ee1dd74384725b3fffbfea7144c3a536b50c531
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2019
ms.locfileid: "74174548"
---
# <a name="publish-events-to-azure-active-directory-protected-endpoints"></a>Publikovat události do Azure Active Directory chráněných koncových bodů

Tento článek popisuje, jak využít Azure Active Directory k zabezpečení připojení mezi odběrem událostí a vaším koncovým bodem Webhooku. Přehled aplikací a instančních objektů služby Azure AD najdete v tématu [Přehled Microsoft Identity Platform (v 2.0)](https://docs.microsoft.com/azure/active-directory/develop/v2-overview).

Tento článek používá Azure Portal k ukázce, ale funkci lze také povolit pomocí rozhraní příkazového řádku, PowerShellu nebo sad SDK.

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="create-and-azure-ad-application"></a>Vytvoření a aplikace Azure AD

Začněte vytvořením aplikace Azure AD pro chráněný koncový bod. Viz https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview.
    - Nakonfigurujte chráněné rozhraní API, které bude volat aplikace typu démon.
    
## <a name="enable-event-grid-to-use-your-azure-ad-application"></a>Povolení Event Grid používání aplikace Azure AD

K vytvoření principu role a služby ve vaší aplikaci Azure AD použijte níže uvedený skript prostředí PowerShell. Budete potřebovat ID tenanta a ID objektu z vaší aplikace Azure AD:

    > [!NOTE]
    > You must be a member of the [Azure AD Application Administrator role](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#available-roles) to execute this script.
    
1. Upravte $myTenantId skriptu PowerShellu tak, aby používal vaše ID tenanta Azure AD.
1. Upravte $myAzureADApplicationObjectId skriptu PowerShellu tak, aby používal ID objektu vaší aplikace Azure AD.
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
    
## <a name="configure-the-event-subscription"></a>Konfigurace odběru událostí

V toku vytváření předplatného události vyberte koncový bod typ Webhook. Po předanému identifikátoru URI koncového bodu klikněte na kartu Další funkce v horní části okna vytvořit odběry událostí.

![Výběr typu koncového bodu Webhook](./media/secure-webhook-delivery/select-webhook.png)

Na kartě Další funkce zaškrtněte políčko použít ověřování AAD a nakonfigurujte ID tenanta a ID aplikace:

* Zkopírujte ID tenanta Azure AD z výstupu skriptu a zadejte ho do pole ID tenanta AAD.
* Zkopírujte ID aplikace Azure AD z výstupu skriptu a zadejte ho do pole ID aplikace AAD.

    ![Akce zabezpečení Webhooku](./media/secure-webhook-delivery/aad-configuration.png)

## <a name="next-steps"></a>Další kroky

* Informace o sledování doručení událostí najdete v tématu [monitorování Event Grid doručování zpráv](monitor-event-delivery.md).
* Další informace o ověřovacím klíči najdete v tématu [Event Grid Security and Authentication](security-authentication.md).
* Další informace o vytváření předplatného Azure Event Grid najdete v tématu [schéma předplatného Event Grid](subscription-creation-schema.md).