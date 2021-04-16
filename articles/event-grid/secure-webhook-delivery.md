---
title: Zabezpečené doručování webhooků pomocí Azure AD v Azure Event Grid
description: Popisuje, jak doručovat události do koncových bodů HTTPS chráněných pomocí Azure Active Directory pomocí Azure Event Grid
ms.topic: how-to
ms.date: 04/13/2021
ms.openlocfilehash: 6a0f9059e17d96d497b425abc9749e69c5ab4d41
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575543"
---
# <a name="publish-events-to-azure-active-directory-protected-endpoints"></a>Publikování událostí do chráněných koncových bodů Azure Active Directory
Tento článek popisuje, jak pomocí Azure Active Directory (Azure AD) zabezpečit připojení mezi **odběrem událostí** a vaším **koncovým bodem Webhooku**. Přehled aplikací a instančních objektů služby Azure AD najdete v tématu [Přehled Microsoft Identity Platform (v 2.0)](../active-directory/develop/v2-overview.md).

Tento článek používá Azure Portal k ukázce, ale funkci lze také povolit pomocí rozhraní příkazového řádku, PowerShellu nebo sad SDK.

> [!IMPORTANT]
> Do 30. března 2021 se zavedla další kontroly přístupu v rámci vytváření nebo aktualizace předplatného pro události, které řeší chybu zabezpečení. Instanční objekt klienta předplatitele musí být buď vlastníkem, nebo musí mít přiřazenou roli pro cílový instanční objekt aplikační služby. Proveďte prosím novou konfiguraci aplikace AAD podle následujících pokynů.


## <a name="create-an-azure-ad-application"></a>Vytvoření aplikace Azure AD
Zaregistrujte Webhook pomocí Azure AD vytvořením aplikace Azure AD pro váš chráněný koncový bod. Viz [scénář: chráněné webové rozhraní API](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview). Nakonfigurujte chráněné rozhraní API, které bude volat aplikace typu démon.
    
## <a name="enable-event-grid-to-use-your-azure-ad-application"></a>Povolení Event Grid používání aplikace Azure AD
V této části se dozvíte, jak povolit Event Grid k používání aplikace Azure AD. 

> [!NOTE]
> Abyste mohli tento skript spustit, musíte být členem [role Správce aplikací Azure AD](../active-directory/roles/permissions-reference.md#all-roles) .

### <a name="connect-to-your-azure-tenant"></a>Připojení k vašemu tenantovi Azure
Nejdřív se připojte k tenantovi Azure pomocí `Connect-AzureAD` příkazu. 

```PowerShell
$myWebhookAadTenantId = "<Your Webhook's Azure AD tenant id>"

Connect-AzureAD -TenantId $myWebhookAadTenantId
```

### <a name="create-microsofteventgrid-service-principal"></a>Vytvoření instančního objektu Microsoft. EventGrid
Spusťte následující skript k vytvoření instančního objektu pro **Microsoft. EventGrid** , pokud ještě neexistuje. 

```PowerShell
# This is the "Azure Event Grid" Azure Active Directory (AAD) AppId
$eventGridAppId = "4962773b-9cdb-44cf-a8bf-237846a00ab7"

# Create the "Azure Event Grid" AAD Application service principal if it doesn't exist
$eventGridSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $eventGridAppId + "'")
if ($eventGridSP -match "Microsoft.EventGrid")
{
    Write-Host "The Service principal is already defined.`n"
} else {
    # Create a service principal for the "Azure Event Grid" AAD Application and add it to the role
    Write-Host "Creating the Azure Event Grid service principal"
    $eventGridSP = New-AzureADServicePrincipal -AppId $eventGridAppId
}
```

### <a name="create-a-role-for-your-application"></a>Vytvoření role pro aplikaci   
Spuštěním následujícího skriptu vytvořte roli pro vaši aplikaci Azure AD. V tomto příkladu je název role: **AzureEventGridSecureWebhookSubscriber**. Upravte skript PowerShellu `$myTenantId` tak, aby používal vaše ID tenanta Azure AD, a `$myAzureADApplicationObjectId` ID objektu vaší aplikace Azure AD.

```PowerShell
# This is your Webhook's Azure AD Application's ObjectId. 
$myWebhookAadApplicationObjectId = "<Your webhook's aad application object id>"

# This is the name of the new role we will add to your Azure AD Application
$eventGridRoleName = "AzureEventGridSecureWebhookSubscriber"
       
# Create an application role of given name and description
Function CreateAppRole([string] $Name, [string] $Description)
{
    $appRole = New-Object Microsoft.Open.AzureAD.Model.AppRole
    $appRole.AllowedMemberTypes = New-Object System.Collections.Generic.List[string]
    $appRole.AllowedMemberTypes.Add("Application");
    $appRole.AllowedMemberTypes.Add("User");
    $appRole.DisplayName = $Name
    $appRole.Id = New-Guid
    $appRole.IsEnabled = $true
    $appRole.Description = $Description
    $appRole.Value = $Name;
    return $appRole
}
       
# Get my Azure AD Application, it's roles and service principal
$myApp = Get-AzureADApplication -ObjectId $myWebhookAadApplicationObjectId
$myAppRoles = $myApp.AppRoles

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
       
# Create the role if it doesn't exist
if ($myAppRoles -match $eventGridRoleName)
{
    Write-Host "The Azure Event Grid role is already defined.`n"
} else {      
    # Add our new role to the Azure AD Application
    Write-Host "Creating the Azure Event Grid role in Azure Ad Application: " $myWebhookAadApplicationObjectId
    $newRole = CreateAppRole -Name $eventGridRoleName -Description "Azure Event Grid Role"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}

# print application's roles
Write-Host "My Azure AD Application's Roles: "
Write-Host $myAppRoles

```

### <a name="create-role-assignment-for-the-client-creating-event-subscription"></a>Vytvořit přiřazení role pro klienta, který vytváří odběr události
Přiřazení role by se mělo vytvořit v Aplikace Azure AD Webhooku pro aplikaci AAD nebo uživatele AAD, který vytváří odběr události. Použijte jeden z následujících skriptů v závislosti na tom, jestli aplikace AAD nebo uživatel AAD vytváří odběr událostí.

> [!IMPORTANT]
> Do 30. března 2021 se zavedla další kontroly přístupu v rámci vytváření nebo aktualizace předplatného pro události, které řeší chybu zabezpečení. Instanční objekt klienta předplatitele musí být buď vlastníkem, nebo musí mít přiřazenou roli pro cílový instanční objekt aplikační služby. Proveďte prosím novou konfiguraci aplikace AAD podle následujících pokynů.

#### <a name="create-role-assignment-for-an-event-subscription-aad-app"></a>Vytvoření přiřazení role pro aplikaci AAD odběru události 

```powershell
# This is the app id of the application which will create event subscription. Set to $null if you are not assigning the role to app.
$eventSubscriptionWriterAppId = "<the app id of the application which will create event subscription>"

$myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")

$eventSubscriptionWriterSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $eventSubscriptionWriterAppId + "'")
if ($eventSubscriptionWriterSP -eq $null)
{
        $eventSubscriptionWriterSP = New-AzureADServicePrincipal -AppId $eventSubscriptionWriterAppId
}

Write-Host "Creating the Azure Ad App Role assignment for application: " $eventSubscriptionWriterAppId
$eventGridAppRole = $myApp.AppRoles | Where-Object -Property "DisplayName" -eq -Value $eventGridRoleName
New-AzureADServiceAppRoleAssignment -Id $eventGridAppRole.Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventSubscriptionWriterSP.ObjectId -PrincipalId $eventSubscriptionWriterSP.ObjectId
```

#### <a name="create-role-assignment-for-an-event-subscription-aad-user"></a>Vytvoření přiřazení role pro uživatele AAD pro odběr událostí 

```powershell
# This is the user principal name of the user who will create event subscription. Set to $null if you are not assigning the role to user.
$eventSubscriptionWriterUserPrincipalName = "<the user principal name of the user who will create event subscription>"

$myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
Write-Host "Creating the Azure Ad App Role assignment for user: " $eventSubscriptionWriterUserPrincipalName
$eventSubscriptionWriterUser = Get-AzureAdUser -ObjectId $eventSubscriptionWriterUserPrincipalName
$eventGridAppRole = $myApp.AppRoles | Where-Object -Property "DisplayName" -eq -Value $eventGridRoleName
New-AzureADUserAppRoleAssignment -Id $eventGridAppRole.Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventSubscriptionWriterUser.ObjectId -PrincipalId $eventSubscriptionWriterUser.ObjectId
```

### <a name="create-role-assignment-for-event-grid-service-principal"></a>Vytvoří přiřazení role pro Event Grid instančního objektu.
Spusťte příkaz New-AzureADServiceAppRoleAssignment pro přiřazení Event Grid instančního objektu k roli, kterou jste vytvořili v předchozím kroku.

```powershell
$eventGridAppRole = $myApp.AppRoles | Where-Object -Property "DisplayName" -eq -Value $eventGridRoleName
New-AzureADServiceAppRoleAssignment -Id $eventGridAppRole.Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventGridSP.ObjectId -PrincipalId $eventGridSP.ObjectId
```

Pro výstup informací, které budete používat později, spusťte následující příkazy.

```powershell
Write-Host "My Webhook's Azure AD Tenant Id:  $myWebhookAadTenantId"
Write-Host "My Webhook's Azure AD Application Id: $($myApp.AppId)"
Write-Host "My Webhook's Azure AD Application ObjectId Id$($myApp.ObjectId)"
```

    
## <a name="configure-the-event-subscription"></a>Konfigurace odběru událostí
Při vytváření odběru událostí použijte následující postup:

1. Jako **webový Hook** vyberte typ koncového bodu. 
1. Zadejte **identifikátor URI** koncového bodu.

    ![Výběr typu koncového bodu Webhook](./media/secure-webhook-delivery/select-webhook.png)
1. V horní části stránky **vytvořit odběry událostí** vyberte kartu **Další funkce** .
1. Na kartě **Další funkce** proveďte tyto kroky:
    1. Vyberte **použít ověřování AAD** a nakonfigurujte ID TENANTA a ID aplikace:
    1. Zkopírujte ID tenanta Azure AD z výstupu skriptu a zadejte ho do pole **ID tenanta AAD** .
    1. Zkopírujte ID aplikace Azure AD z výstupu skriptu a zadejte ho do pole **ID aplikace AAD** . Alternativně můžete použít identifikátor URI ID aplikace AAD. Další informace o identifikátoru URI ID aplikace najdete v [tomto článku](../app-service/configure-authentication-provider-aad.md).

        ![Akce zabezpečení Webhooku](./media/secure-webhook-delivery/aad-configuration.png)



## <a name="next-steps"></a>Další kroky

* Informace o sledování doručení událostí najdete v tématu [monitorování Event Grid doručování zpráv](monitor-event-delivery.md).
* Další informace o ověřovacím klíči najdete v tématu [Event Grid Security and Authentication](security-authentication.md).
* Další informace o vytváření předplatného Azure Event Grid najdete v tématu [schéma předplatného Event Grid](subscription-creation-schema.md).
