---
title: Řešení pro správu Office 365 v Azure | Microsoft Docs
description: Tento článek poskytuje podrobné informace o konfiguraci a použití řešení Office 365 v Azure.  Obsahuje podrobný popis záznamů Office 365 vytvořené v analýzy protokolů.
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2018
ms.author: bwren
ms.openlocfilehash: e93860328ed6a31b7a06cc3420fb50ab4af9a00c
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35236099"
---
# <a name="office-365-management-solution-in-azure-preview"></a>Řešení pro správu Office 365 v Azure (Preview)

![Logo Office 365](media/oms-solution-office-365/icon.png)

Řešení pro správu Office 365 umožňuje monitorovat prostředí Office 365 v analýzy protokolů.

- Monitorování aktivit uživatelů na účty služeb Office 365 a analyzovat vzorce používání a také identifikovat trendy chování. Například můžete rozbalit konkrétní využití scénáře, jako jsou například soubory, které jsou sdíleny mimo vaši organizaci nebo nejoblíbenější webů služby SharePoint.
- Monitorování aktivit správce sledovat změny v konfiguraci nebo operations vysoká oprávnění.
- Zjištění a prozkoumat chování nežádoucí uživatele, který lze přizpůsobit potřebám vaší organizace.
- Předvedení auditu a dodržování předpisů. Například můžete sledovat přístup operací se soubory na důvěrné soubory, které vám můžou pomoct se proces auditu a dodržování předpisů.
- Provádět provozní řešení problémů pomocí [protokolu hledání](../log-analytics/log-analytics-log-search.md) nad data Office 365 aktivit vaší organizace.

## <a name="prerequisites"></a>Požadavky
Toto je požadované před toto řešení je nainstalován a nakonfigurován.

- Organizace předplatné služeb Office 365.
- Přihlašovací údaje pro uživatelský účet, který je globálním správcem.
- Chcete-li přijímat data auditu, je potřeba [konfigurace auditování](https://support.office.com/en-us/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=en-US&rs=en-US&ad=US#PickTab=Before_you_begin) v rámci vašeho předplatného Office 365.  Všimněte si, že [auditování poštovní schránky](https://technet.microsoft.com/library/dn879651.aspx) je samostatně nakonfigurovaný.  Přesto můžete nainstalovat řešení a shromažďovat další data, pokud není nakonfigurováno auditování.
 

## <a name="management-packs"></a>Sady Management Pack
Toto řešení nenainstaluje žádné sady management Pack v [připojené skupiny pro správu](../log-analytics/log-analytics-om-agents.md).
  
## <a name="install-and-configure"></a>Instalace a konfigurace
Začněte přidáním [řešení Office 365 k vašemu předplatnému](/monitoring/monitoring-solutions.md#install-a-management-solution). Po přidání, musíte provést kroky konfigurace v této části je poskytnout přístup k předplatné služeb Office 365.

### <a name="required-information"></a>Požadované informace
Před zahájením tohoto postupu, shromážděte následující informace.

Z pracovního prostoru analýzy protokolů:

- Název pracovního prostoru: pracovní prostor, kde budou shromažďovány data Office 365.
- Název skupiny prostředků: skupinu prostředků, která obsahuje pracovním prostoru.
- ID předplatného Azure: předplatné, které obsahuje pracovním prostoru.

Ze svého předplatného služeb Office 365:

- Uživatelské jméno: E-mailovou adresu účtu správce.
- ID klienta: Jedinečné ID pro předplatné služeb Office 365.
- ID klienta: řetězec 16 znaků, který představuje klienta Office 365.
- Tajný klíč klienta: Zašifrovaný řetězec potřebné pro ověřování.

### <a name="create-an-office-365-application-in-azure-active-directory"></a>Vytvoření aplikace Office 365 ve službě Azure Active Directory
Prvním krokem je vytvoření aplikace v Azure Active Directory, který řešení pro správu bude používat pro přístup k řešení Office 365.

1. Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com/).
1. Vyberte **Azure Active Directory** a potom **registrace aplikace**.
1. Klikněte na **Registrace nové aplikace**.

    ![Přidat registraci aplikace](media/oms-solution-office-365/add-app-registration.png)
1. Zadejte aplikaci **název** a **přihlašovací adresa URL**.  Název by měl být popisný.  Použití _http://localhost_ pro adresu URL a udržování _webovou aplikaci nebo API_ pro **typ aplikace**
    
    ![Vytvořit aplikaci](media/oms-solution-office-365/create-application.png)
1. Klikněte na tlačítko **vytvořit** a ověřte informace o aplikaci.

    ![Registrovaná aplikace](media/oms-solution-office-365/registered-app.png)

### <a name="configure-application-for-office-365"></a>Konfigurace aplikace pro Office 365

1. Klikněte na tlačítko **nastavení** otevřete **nastavení** nabídky.
1. Vyberte **vlastnosti**. Změna **nevyužívá dělené tabulky více** k _Ano_.

    ![Víceklientská nastavení](media/oms-solution-office-365/settings-multitenant.png)

1. Vyberte **požadovaná oprávnění** v **nastavení** nabídce a pak klikněte na tlačítko **přidat**.
1. Klikněte na tlačítko **vybrat rozhraní API** a potom **rozhraní API pro správu Office 365**. Klikněte na tlačítko **rozhraní API pro správu Office 365**. Klikněte na **Vybrat**.

    ![Vybrat rozhraní API](media/oms-solution-office-365/select-api.png)

1. V části **vyberte oprávnění** vyberte následující možnosti pro obě **oprávnění aplikací** a **delegovaná oprávnění**:
    - Přečíst informace o stavu služby pro organizaci
    - Čtení dat o aktivitách pro vaši organizaci
    - Přečíst sestavy aktivit pro organizaci

    ![Vybrat rozhraní API](media/oms-solution-office-365/select-permissions.png)

1. Klikněte na tlačítko **vyberte** a potom **provádí**.
1. Klikněte na tlačítko **udělit oprávnění** a pak klikněte na **Ano** po zobrazení výzvy k ověření.

    ![Udělení oprávnění](media/oms-solution-office-365/grant-permissions.png)

### <a name="add-a-key-for-the-application"></a>Přidá klíč pro aplikaci

1. Vyberte **klíče** v **nastavení** nabídky.
1. Zadejte **popis** a **doba trvání** pro nový klíč.
1. Klikněte na tlačítko **Uložit** a poté zkopírujte **hodnotu** generovanou.

    ![Klíče](media/oms-solution-office-365/keys.png)

### <a name="add-admin-consent"></a>Přidat správce souhlasu
Pokud chcete povolit účet správce, který pro první, je nutné zadat správce souhlasu pro aplikaci. To provedete pomocí skriptu prostředí PowerShell. 

1. Uložte následující skript jako *office365_consent.ps1*.

    ```
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,     
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId
    )
    
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
        
    IF ($Subscription -eq $null)
        {Login-AzureRmAccount -ErrorAction Stop}
    $Subscription = (Select-AzureRmSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $Workspace = (Set-AzureRMOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
    $WorkspaceLocation= $Workspace.Location
    $WorkspaceLocationShort= $Workspace.PortalUrl.Split("/",$option)[1].Split(".",$option)[0]
    $WorkspaceLocation
    
    Function AdminConsent{
    
    $domain='login.microsoftonline.com'
    $mmsDomain = 'login.mms.microsoft.com'
    $WorkspaceLocationShort= $Workspace.PortalUrl.Split("/",$option)[1].Split(".",$option)[0]
    $WorkspaceLocationShort
    $WorkspaceLocation
    switch ($WorkspaceLocation.Replace(" ","").ToLower()) {
           "eastus"   {$OfficeAppClientId="d7eb65b0-8167-4b5d-b371-719a2e5e30cc"; break}
           "westeurope"   {$OfficeAppClientId="c9005da2-023d-40f1-a17a-2b7d91af4ede"; break}
           "southeastasia"   {$OfficeAppClientId="09c5b521-648d-4e29-81ff-7f3a71b27270"; break}
           "australiasoutheast"  {$OfficeAppClientId="f553e464-612b-480f-adb9-14fd8b6cbff8"; break}   
           "westcentralus"  {$OfficeAppClientId="98a2a546-84b4-49c0-88b8-11b011dc8c4e"; break}
           "japaneast"   {$OfficeAppClientId="b07d97d3-731b-4247-93d1-755b5dae91cb"; break}
           "uksouth"   {$OfficeAppClientId="f232cf9b-e7a9-4ebb-a143-be00850cd22a"; break}
           "centralindia"   {$OfficeAppClientId="ffbd6cf4-cba8-4bea-8b08-4fb5ee2a60bd"; break}
           "canadacentral"  {$OfficeAppClientId="c2d686db-f759-43c9-ade5-9d7aeec19455"; break}
           "eastus2"  {$OfficeAppClientId="7eb65b0-8167-4b5d-b371-719a2e5e30cc"; break}
           "westus2"  {$OfficeAppClientId="98a2a546-84b4-49c0-88b8-11b011dc8c4e"; break} #Need to check
           "usgovvirginia" {$OfficeAppClientId="c8b41a87-f8c5-4d10-98a4-f8c11c3933fe"; 
                             $domain='login.microsoftonline.us';
                             $mmsDomain = 'usbn1.login.oms.microsoft.us'; break} # US Gov Virginia
           default {$OfficeAppClientId="55b65fb5-b825-43b5-8972-c8b6875867c1";
                    $domain='login.windows-ppe.net'; break} #Int
        }
    
        $OfficeAppRedirectUrl="https://$($WorkspaceLocationShort).$($mmsDomain)/Office365/Authorize"
        $OfficeAppRedirectUrl
        $domain
        Start-Process -FilePath  "https://$($domain)/common/adminconsent?client_id=$($OfficeAppClientId)&state=12345&redirect_uri=$($OfficeAppRedirectUrl)"
    }
    
    AdminConsent -ErrorAction Stop
    ```

2. Pomocí následujícího příkazu spusťte skript.
    ```
    .\office365_consent.ps1 -WorkspaceName <Workspace name> -ResourceGroupName <Resource group name> -SubscriptionId <Subscription ID>
    ```
    Příklad:

    ```
    .\office365_consent.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631- yyyyyyyyyyyy'
    ```

1. Zobrazí se okno podobná té následující. Klikněte na tlačítko **přijmout**.
    
    ![Souhlas správce](media/oms-solution-office-365/admin-consent.png)

### <a name="subscribe-to-log-analytics-workspace"></a>Přihlášení k odběru do pracovního prostoru analýzy protokolů
Posledním krokem je přihlášení k odběru aplikace do pracovního prostoru analýzy protokolů. Můžete to provést taky pomocí skriptu prostředí PowerShell.

1. Uložte následující skript jako *office365_subscription.ps1*.

    ```
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId,
        [Parameter(Mandatory=$True)][string]$OfficeUsername,
        [Parameter(Mandatory=$True)][string]$OfficeTennantId,
        [Parameter(Mandatory=$True)][string]$OfficeClientId,
        [Parameter(Mandatory=$True)][string]$OfficeClientSecret
    )
    $line='#-------------------------------------------------------------------------------------------------------------------------------------------------------------------------'
    $line
    IF ($Subscription -eq $null)
        {Login-AzureRmAccount -ErrorAction Stop}
    $Subscription = (Select-AzureRmSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    $Workspace = (Set-AzureRMOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
    $Workspace
    $WorkspaceLocation= $Workspace.Location
    $OfficeClientSecret =[uri]::EscapeDataString($OfficeClientSecret)
    
    # Client ID for Azure PowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2"
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    $domain='login.microsoftonline.com'
    $adTenant = $Subscription[0].Tenant.Id
    $authority = "https://login.windows.net/$adTenant";
    $ARMResource ="https://management.azure.com/";
    $xms_client_tenant_Id ='55b65fb5-b825-43b5-8972-c8b6875867c1'
    
    switch ($WorkspaceLocation) {
           "USGov Virginia" { 
                             $domain='login.microsoftonline.us';
                              $authority = "https://login.microsoftonline.us/$adTenant";
                              $ARMResource ="https://management.usgovcloudapi.net/"; break} # US Gov Virginia
           default {
                    $domain='login.microsoftonline.com'; 
                    $authority = "https://login.windows.net/$adTenant";
                    $ARMResource ="https://management.azure.com/";break} 
                    }
    
    Function RESTAPI-Auth { 
    
    # Load ADAL Azure AD Authentication Library Assemblies
    $adal = "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Services\Microsoft.IdentityModel.Clients.ActiveDirectory.dll"
    $adalforms = "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Services\Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll"
    $null = [System.Reflection.Assembly]::LoadFrom($adal)
    $null = [System.Reflection.Assembly]::LoadFrom($adalforms)
     
    $global:SubscriptionID = $Subscription.SubscriptionId
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURIARM=$ARMResource;
    # Authenticate and Acquire Token 
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $global:authResultARM = $authContext.AcquireToken($resourceAppIdURIARM, $clientId, $redirectUri, "Auto")
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $authHeader
    }
    
    Function Failure {
    $line
    $formatstring = "{0} : {1}`n{2}`n" +
                    "    + CategoryInfo          : {3}`n" +
                    "    + FullyQualifiedErrorId : {4}`n"
    $fields = $_.InvocationInfo.MyCommand.Name,
              $_.ErrorDetails.Message,
              $_.InvocationInfo.PositionMessage,
              $_.CategoryInfo.ToString(),
              $_.FullyQualifiedErrorId
    
    $formatstring -f $fields
    $_.Exception.Response
    
    $line
    break
    }
    
    Function Connection-API
    {
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $ResourceName = "https://manage.office.com"
    $SubscriptionId   =  $Subscription[0].Subscription.Id
    
    $line
    $connectionAPIUrl = $ARMResource + 'subscriptions/' + $SubscriptionId + '/resourceGroups/' + $ResourceGroupName + '/providers/Microsoft.OperationalInsights/workspaces/' + $WorkspaceName + '/connections/office365connection_' + $SubscriptionId + $OfficeTennantId + '?api-version=2017-04-26-preview'
    $connectionAPIUrl
    $line
    
    $xms_client_tenant_Id ='1da8f770-27f4-4351-8cb3-43ee54f14759'
    
    $BodyString = "{
                    'properties': {
                                    'AuthProvider':'Office365',
                                    'clientId': '" + $OfficeClientId + "',
                                    'clientSecret': '" + $OfficeClientSecret + "',
                                    'Username': '" + $OfficeUsername   + "',
                                    'Url': 'https://$($domain)/" + $OfficeTennantId + "/oauth2/token',
                                  },
                    'etag': '*',
                    'kind': 'Connection',
                    'solution': 'Connection',
                   }"
    
    $params = @{
        ContentType = 'application/json'
        Headers = @{
        'Authorization'="$($authHeader)"
        'x-ms-client-tenant-id'=$xms_client_tenant_Id #Prod-'1da8f770-27f4-4351-8cb3-43ee54f14759'
        'Content-Type' = 'application/json'
        }
        Body = $BodyString
        Method = 'Put'
        URI = $connectionAPIUrl
    }
    $response = Invoke-WebRequest @params 
    $response
    $line
    
    }
    
    Function Office-Subscribe-Call{
    try{
    #----------------------------------------------------------------------------------------------------------------------------------------------
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $SubscriptionId   =  $Subscription[0].Subscription.Id
    $OfficeAPIUrl = $ARMResource + 'subscriptions/' + $SubscriptionId + '/resourceGroups/' + $ResourceGroupName + '/providers/Microsoft.OperationalInsights/workspaces/' + $WorkspaceName + '/datasources/office365datasources_' + $SubscriptionId + $OfficeTennantId + '?api-version=2015-11-01-preview'
    
    $OfficeBodyString = "{
                    'properties': {
                                    'AuthProvider':'Office365',
                                    'office365TenantID': '" + $OfficeTennantId + "',
                                    'connectionID': 'office365connection_" + $SubscriptionId + $OfficeTennantId + "',
                                    'office365AdminUsername': '" + $OfficeUsername + "',
                                    'contentTypes':'Audit.Exchange,Audit.AzureActiveDirectory'
                                  },
                    'etag': '*',
                    'kind': 'Office365',
                    'solution': 'Office365',
                   }"
    
    $Officeparams = @{
        ContentType = 'application/json'
        Headers = @{
        'Authorization'="$($authHeader)"
        'x-ms-client-tenant-id'=$xms_client_tenant_Id
        'Content-Type' = 'application/json'
        }
        Body = $OfficeBodyString
        Method = 'Put'
        URI = $OfficeAPIUrl
      }
    
    $officeresponse = Invoke-WebRequest @Officeparams 
    $officeresponse
    }
    catch{ Failure }
    }
    
    #GetDetails 
    RESTAPI-Auth -ErrorAction Stop
    Connection-API -ErrorAction Stop
    Office-Subscribe-Call -ErrorAction Stop
    ```

2. Spusťte skript následujícím příkazem:
    ```
    .\office365_subscription.ps1 -WorkspaceName <Log Analytics workspace name> -ResourceGroupName <Resource Group name> -SubscriptionId <Subscription ID> -OfficeUsername <OfficeUsername> -OfficeTennantID <Tenant ID> -OfficeClientId <Client ID> -OfficeClientSecret <Client secret>
    ```
    Příklad:

    ```
    .\office365_subscription.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631-yyyyyyyyyyyy' -OfficeUsername 'admin@contoso.com' -OfficeTennantID 'ce4464f8-a172-4dcf-b675-xxxxxxxxxxxx' -OfficeClientId 'f8f14c50-5438-4c51-8956-zzzzzzzzzzzz' -OfficeClientSecret 'y5Lrwthu6n5QgLOWlqhvKqtVUZXX0exrA2KRHmtHgQb='
    ```

### <a name="troublshooting"></a>Troublshooting

Pokud se pokusíte vytvořit odběr po předplatné už existuje, může se zobrazit následující chyby.

```
Invoke-WebRequest : {"Message":"An error has occurred."}
At C:\Users\v-tanmah\Desktop\ps scripts\office365_subscription.ps1:161 char:19
+ $officeresponse = Invoke-WebRequest @Officeparams
+                   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidOperation: (System.Net.HttpWebRequest:HttpWebRequest) [Invoke-WebRequest], WebException
    + FullyQualifiedErrorId : WebCmdletWebResponseException,Microsoft.PowerShell.Commands.InvokeWebRequestCommand 
```

Pokud jsou zadané neplatné hodnoty parametrů, může se zobrazit následující chyby.

```
Select-AzureRmSubscription : Please provide a valid tenant or a valid subscription.
At line:12 char:18
+ ... cription = (Select-AzureRmSubscription -SubscriptionId $($Subscriptio ...
+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzureRmContext], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.SetAzureRMContextCommand

```

## <a name="uninstall"></a>Odinstalace
Můžete odebrat řešení pro správu Office 365 pomocí procesu v [odebrat řešení pro správu](../monitoring/monitoring-solutions.md#remove-a-management-solution). Data se shromažďují ze služeb Office 365 do analýzy protokolů, když to nebude zastaven. Pomocí následujícího postupu zastavte shromažďování dat a odhlášení odběru Office 365.

1. Uložte následující skript jako *office365_unsubscribe.ps1*.

    ```
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId,
        [Parameter(Mandatory=$True)][string]$OfficeTennantId
    )
    $line='#-------------------------------------------------------------------------------------------------------------------------------------------------------------------------'
    
    $line
    IF ($Subscription -eq $null)
        {Login-AzureRmAccount -ErrorAction Stop}
    $Subscription = (Select-AzureRmSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    $Workspace = (Set-AzureRMOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
    $Workspace
    $WorkspaceLocation= $Workspace.Location
    
    # Client ID for Azure PowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2"
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    $domain='login.microsoftonline.com'
    $adTenant =  $Subscription[0].Tenant.Id
    $authority = "https://login.windows.net/$adTenant";
    $ARMResource ="https://management.azure.com/";
    $xms_client_tenant_Id ='55b65fb5-b825-43b5-8972-c8b6875867c1'
    
    switch ($WorkspaceLocation) {
           "USGov Virginia" { 
                             $domain='login.microsoftonline.us';
                              $authority = "https://login.microsoftonline.us/$adTenant";
                              $ARMResource ="https://management.usgovcloudapi.net/"; break} # US Gov Virginia
           default {
                    $domain='login.microsoftonline.com'; 
                    $authority = "https://login.windows.net/$adTenant";
                    $ARMResource ="https://management.azure.com/";break} 
                    }
    
    Function RESTAPI-Auth { 
    
    # Load ADAL Azure AD Authentication Library Assemblies
    $adal = "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Services\Microsoft.IdentityModel.Clients.ActiveDirectory.dll"
    $adalforms = "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Services\Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll"
    $null = [System.Reflection.Assembly]::LoadFrom($adal)
    $null = [System.Reflection.Assembly]::LoadFrom($adalforms)
     
    $global:SubscriptionID = $Subscription.SubscriptionId
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURIARM=$ARMResource;
    # Authenticate and Acquire Token 
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $global:authResultARM = $authContext.AcquireToken($resourceAppIdURIARM, $clientId, $redirectUri, "Auto")
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $authHeader
    }
    
    Function Office-UnSubscribe-Call{
    
    #----------------------------------------------------------------------------------------------------------------------------------------------
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $ResourceName = "https://manage.office.com"
    $SubscriptionId   = $Subscription[0].Subscription.Id
    $OfficeAPIUrl = $ARMResource + 'subscriptions/' + $SubscriptionId + '/resourceGroups/' + $ResourceGroupName + '/providers/Microsoft.OperationalInsights/workspaces/' + $WorkspaceName + '/datasources/office365datasources_'  + $SubscriptionId + $OfficeTennantId + '?api-version=2015-11-01-preview'
    
    $Officeparams = @{
        ContentType = 'application/json'
        Headers = @{
        'Authorization'="$($authHeader)"
        'x-ms-client-tenant-id'=$xms_client_tenant_Id
        'Content-Type' = 'application/json'
        }
        Method = 'Delete'
        URI = $OfficeAPIUrl
      }
    
    $officeresponse = Invoke-WebRequest @Officeparams 
    $officeresponse
    
    }
    
    #GetDetails 
    RESTAPI-Auth -ErrorAction Stop
    Office-UnSubscribe-Call -ErrorAction Stop
    ```

2. Spusťte skript následujícím příkazem:

    ```
    .\office365_unsubscribe.ps1 -WorkspaceName <Log Analytics workspace name> -ResourceGroupName <Resource Group name> -SubscriptionId <Subscription ID> -OfficeTennantID <Tenant ID> 
    ```

    Příklad:

    ```
    .\office365_unsubscribe.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631-yyyyyyyyyyyy' -OfficeTennantID 'ce4464f8-a172-4dcf-b675-xxxxxxxxxxxx'
    ```

## <a name="data-collection"></a>Shromažďování dat
### <a name="supported-agents"></a>Podporovaní agenti
Řešení Office 365 nepodporuje načtení dat ze všech [OMS agenti](../log-analytics/log-analytics-data-sources.md).  Načte data přímo z Office 365.

### <a name="collection-frequency"></a>Četnost shromažďování dat
Může trvat několik hodin, než původně jde data shromažďovat. Jakmile se spustí, shromažďování, odešle Office 365 [webhooku oznámení](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference#receiving-notifications) s podrobná data k analýze protokolů pokaždé, když se vytvoří záznam. Tento záznam je k dispozici v analýzy protokolů během několika minut po přijímání.

## <a name="using-the-solution"></a>Použití řešení
Když přidáte do pracovního prostoru analýzy protokolů řešení Office 365 **Office 365** dlaždice budou přidány do řídicího panelu. Na této dlaždici se zobrazuje počet a grafické vyjádření počtu počítačů ve vašem prostředí a jejich kompatibilita s aktualizacemi.<br><br>
![Dlaždice souhrnu Office 365](media/oms-solution-office-365/tile.png)  

Klikněte na **Office 365** dlaždici otevřete **Office 365** řídicího panelu.

![Řídicí panel Office 365](media/oms-solution-office-365/dashboard.png)  

Řídicí panel obsahuje sloupce v následující tabulce. Každý sloupec uvádí top deseti výstrah podle počtu odpovídající kritériím tento sloupec pro zadaný obor a časový rozsah. Můžete spustit hledání protokolů, který poskytuje celý seznam kliknutím najdete všechny v dolní části sloupec nebo kliknutím na záhlaví sloupce.

| Sloupec | Popis |
|:--|:--|
| Operace | Poskytuje informace o aktivní uživatelé z vašich všechny monitorované předplatných Office 365. Také bude moci zobrazit počet aktivit, které dojít v průběhu času.
| Výměna | Ukazuje rozpis aktivity systému Exchange Server, například Add-Mailbox oprávnění nebo Set-poštovní schránky. |
| SharePoint | Ukazuje nejvyšší aktivity, že uživatelé provádět na dokumenty služby SharePoint. Pokud přecházíte z této dlaždice stránky hledání zobrazuje podrobnosti o tyto aktivity, jako je například cílovém dokumentu a umístění této aktivity. Například pro událost přistupovat k souboru, bude možné zobrazit dokumentu, do kterého se přistupuje, jeho přidružený účet názvu a IP adresy. |
| Azure Active Directory | Obsahuje hlavní uživatelské aktivity, například Resetovat heslo uživatele a pokusů o přihlášení. Když přejdete k podrobnostem, nebudete moci zobrazit podrobnosti o tyto aktivity, jako je výsledný stav. Toto je nejčastěji užitečné, pokud chcete monitorovat podezřelých aktivit v Azure Active Directory. |




## <a name="log-analytics-records"></a>Záznamy služby Log Analytics

Mají všechny záznamy v pracovním prostoru analýzy protokolů vytvořené řešení Office 365 **typ** z **OfficeActivity**.  **OfficeWorkload** vlastnost určuje služby Office 365, která odkazuje záznam - Exchange azureactivedirectory selhala, SharePoint nebo OneDrive.  **RecordType** vlastnost určuje typ operace.  Vlastnosti pro každý typ operace se bude lišit a jsou uvedeny v následujících tabulkách.

### <a name="common-properties"></a>Běžné vlastnosti
Následující vlastnosti jsou společné pro všechny záznamy Office 365.

| Vlastnost | Popis |
|:--- |:--- |
| Typ | *OfficeActivity* |
| Když | IP adresa zařízení, která byla použita při protokolu byla zaznamenána aktivita. IP adresa se zobrazí ve formátu adresa IPv4 nebo IPv6. |
| OfficeWorkload | Služby Office 365, která odkazuje na záznam.<br><br>Azureactivedirectory selhala<br>Výměna<br>SharePoint|
| Operace | Název aktivity uživatele nebo správce.  |
| Kódu organizace | Identifikátor GUID pro klienta Office 365 vaší organizace. Tato hodnota bude vždy stejná pro vaši organizaci, bez ohledu na to, ve kterém byly provedeny služby Office 365. |
| recordType | Typ operace provádí. |
| ResultStatus | Určuje, zda akce (zadaná ve vlastnosti operace) byla úspěšná, nebo ne. Možné hodnoty jsou úspěšné, PartiallySucceded nebo se nezdařilo. Aktivita správce systému Exchange, má hodnotu buď True nebo False. |
| UserId | Hlavní název uživatele (hlavní název uživatele) uživatele, který provedl akci, která byla vygenerována v záznamu protokolována; například my_name@my_domain_name. Všimněte si, že zaznamenává aktivity prováděné účty systému (například SHAREPOINT\system nebo NTAUTHORITY\SYSTEM) jsou také zahrnuty. | 
| UserKey | Alternativní ID pro uživatele identifikovaného ve vlastnosti ID uživatele.  Například tato vlastnost obsahuje jedinečné ID passport (PUID) pro události se provádí uživatelů na SharePoint, Onedrivu pro firmy a Exchange. Tato vlastnost může také zadejte stejnou hodnotu jako vlastnost ID uživatele pro události, ke kterým dochází v jiných službách a událostech provádí účty systému|
| UserType | Typ uživatele, který provádí operaci.<br><br>Správa<br>Aplikace<br>DcAdmin<br>Regulární<br>Rezervováno<br>ServicePrincipal<br>Systémový |


### <a name="azure-active-directory-base"></a>Základní Azure Active Directory
Následující vlastnosti jsou společné pro všechny záznamy Azure Active Directory.

| Vlastnost | Popis |
|:--- |:--- |
| OfficeWorkload | Azureactivedirectory selhala |
| recordType     | Azureactivedirectory selhala |
| AzureActiveDirectory_EventType | Typ události, Azure AD. |
| ExtendedProperties | Rozšířené vlastnosti události, Azure AD. |


### <a name="azure-active-directory-account-logon"></a>Azure přihlašovací účet služby Active Directory
Tyto záznamy jsou vytvořeny při uživatele služby Active Directory se pokusí přihlásit.

| Vlastnost | Popis |
|:--- |:--- |
| OfficeWorkload | Azureactivedirectory selhala |
| recordType     | AzureActiveDirectoryAccountLogon |
| Aplikace | Aplikace, která spustí událost přihlášení účtu, jako je například Office 15. |
| Klient | Podrobnosti o klientovi zařízení, operačního systému zařízení a prohlížeč zařízení, která byla použita pro události přihlášení účtu. |
| loginStatus | Tato vlastnost je přímo z OrgIdLogon.LoginStatus. Mapování různé zajímavé nezdařených pokusů o přihlášení může provést výstrahy algoritmy. |
| UserDomain | Informace identita klienta (TÍ). | 


### <a name="azure-active-directory"></a>Azure Active Directory
Tyto záznamy jsou vytvořen při změna nebo přidání jsou objekty služby Azure Active Directory.

| Vlastnost | Popis |
|:--- |:--- |
| OfficeWorkload | Azureactivedirectory selhala |
| recordType     | Azureactivedirectory selhala |
| AADTarget | Uživatel, který na byla provedena akce (identifikovaný vlastnost operace). |
| Objekt actor | Uživatel nebo objektu služby, která akci provedla. |
| ActorContextId | Organizace, který je součástí objektu actor identifikátor GUID. |
| ActorIpAddress | Objektu actor IP adresu ve formátu adresy IPV4 nebo IPV6. |
| InterSystemsId | Identifikátor GUID, které sledují akce mezi komponentami v rámci služby Office 365. |
| IntraSystemId |   Identifikátor GUID, které se generují ve službě Azure Active Directory ke sledování akce. |
| SupportTicketId | Pro akci v situacích, "act-on-behalf-of" podporovat ID lístku. |
| TargetContextId | Organizace, který cílový uživatel patří do identifikátor GUID. |


### <a name="data-center-security"></a>Datové Centrum zabezpečení
Tyto záznamy jsou vytvořené z dat auditu zabezpečení dat Center.  

| Vlastnost | Popis |
|:--- |:--- |
| EffectiveOrganization | Název klienta, která byla cílem zvýšení oprávnění nebo rutiny v. |
| ElevationApprovedTime | Časové razítko pro když byla schválena zvýšení oprávnění. |
| ElevationApprover | Název správce společnosti Microsoft. |
| ElevationDuration | Doba trvání, pro který byl aktivní zvýšení oprávnění. |
| ElevationRequestId |  Jedinečný identifikátor pro žádost o zvýšení oprávnění. |
| ElevationRole | Bylo vyžádáno roli zvýšení oprávnění. |
| ElevationTime | Čas zahájení zvýšení oprávnění. |
| Start_Time | Počáteční čas provádění rutiny. |


### <a name="exchange-admin"></a>Správce systému Exchange
Tyto záznamy jsou vytvořeny při provedení změn konfigurace serveru Exchange.

| Vlastnost | Popis |
|:--- |:--- |
| OfficeWorkload | Výměna |
| recordType     | ExchangeAdmin |
| ExternalAccess |  Určuje, zda byl rutinu spustit uživatele v organizaci, pracovníky datového centra společnosti Microsoft nebo účtu služby datacenter nebo delegovaný správce. Hodnota False určuje, že rutina byla spouštět uživatel ve vaší organizaci. Hodnotu True. Určuje, že byl rutinu spouštět pracovníky datacenter, účet služby datacenter nebo delegovaný správce. |
| ModifiedObjectResolvedName |  Toto je popisný název objektu, který rutina, byla změněna. To se zaznamená pouze v případě, že rutina upraví objekt. |
| Název organizace | Název klienta. |
| OriginatingServer | Název serveru, ze kterého byla spuštěna rutina. |
| Parametry | Název a hodnotu pro všechny parametry, které se používaly pomocí rutiny identifikovanou ve vlastnosti operace. |


### <a name="exchange-mailbox"></a>Poštovní schránky serveru Exchange
Tyto záznamy jsou vytvořen při změny nebo přidání jsou poštovní schránky serveru Exchange.

| Vlastnost | Popis |
|:--- |:--- |
| OfficeWorkload | Výměna |
| recordType     | ExchangeItem |
| ClientInfoString | Informace o e-mailovém klientovi, který byl použit k provedení operace, jako je například verze prohlížeče, verze aplikace Outlook a informace o mobilních zařízeních. |
| Client_IPAddress | IP adresa zařízení, která byla použita při operaci protokolu byla zaznamenána. IP adresa se zobrazí ve formátu adresa IPv4 nebo IPv6. |
| ClientMachineName | Název počítače, který je hostitelem klientovi aplikace Outlook. |
| ClientProcessName | Klient e-mailu, který byl použit pro přístup k poštovní schránku. |
| ClientVersion | Verze e-mailového klienta. |
| InternalLogonType | Vyhrazeno pro interní použití. |
| Logon_Type | Určuje typ uživatele, který získat přístup k poštovní schránku a provést operaci, která byla zapsána do protokolu. |
| LogonUserDisplayName |    Uživatelsky přívětivý název uživatele, který provádí operaci. |
| LogonUserSid | SID uživatele, který provedl operaci. |
| MailboxGuid | Identifikátor GUID Exchange poštovní schránky, který byl přístupný. |
| MailboxOwnerMasterAccountSid | Účet vlastníka poštovní schránky hlavní SID účtu. |
| MailboxOwnerSid | Identifikátor SID vlastníka poštovní schránky. |
| MailboxOwnerUPN | E-mailovou adresu osoby, která vlastní poštovní schránky, který byl přístupný. |


### <a name="exchange-mailbox-audit"></a>Auditování poštovní schránky systému Exchange
Tyto záznamy jsou vytvořeny při vytvoření položky auditu poštovní schránky.

| Vlastnost | Popis |
|:--- |:--- |
| OfficeWorkload | Výměna |
| recordType     | ExchangeItem |
| Položka | Představuje položku, na kterém byla provedena operace | 
| SendAsUserMailboxGuid | Identifikátor GUID Exchange poštovní schránky, které se přistupovalo k odesílání e-mailu. |
| SendAsUserSmtp | Adresa SMTP uživatele, který jde o zosobňovaného. |
| SendonBehalfOfUserMailboxGuid | Identifikátor GUID Exchange poštovní schránky, které se přistupovalo k odesílání pošty jménem. |
| SendOnBehalfOfUserSmtp | Adresa SMTP uživatele, jehož jménem je odesláno e-mailu. |


### <a name="exchange-mailbox-audit-group"></a>Skupiny auditování poštovní schránky serveru Exchange
Tyto záznamy jsou vytvořen při změny nebo přidání jsou skupiny Exchange.

| Vlastnost | Popis |
|:--- |:--- |
| OfficeWorkload | Výměna |
| OfficeWorkload | ExchangeItemGroup |
| AffectedItems | Informace o jednotlivých položkách ve skupině. |
| CrossMailboxOperations | Označuje, pokud operace zapojená víc než jednu poštovní schránku. |
| DestMailboxId | Nastavte jenom v případě, že parametr CrossMailboxOperations má hodnotu True. Určuje poštovní schránku cílový identifikátor GUID. |
| DestMailboxOwnerMasterAccountSid | Nastavte jenom v případě, že parametr CrossMailboxOperations má hodnotu True. Určuje identifikátor SID pro hlavní účet SID vlastníka poštovní schránky cíl. |
| DestMailboxOwnerSid | Nastavte jenom v případě, že parametr CrossMailboxOperations má hodnotu True. Určuje identifikátor SID poštovní schránky, cíl. |
| DestMailboxOwnerUPN | Nastavte jenom v případě, že parametr CrossMailboxOperations má hodnotu True. Určuje název UPN vlastníka poštovní schránky, cíl. |
| DestFolder | Cílová složka pro operace, jako je například přesunout. |
| Složka | Složky, kde se nachází skupinu položek. |
| Složky |     Informace o zahrnutých v operaci; zdrojové složky Pokud například složky jsou vybrané a pak je odstranit. |


### <a name="sharepoint-base"></a>Základní služby SharePoint
Tyto vlastnosti jsou společné pro všechny záznamy služby SharePoint.

| Vlastnost | Popis |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| EventSource | Určuje, že k události došlo ve službě SharePoint. Možné hodnoty jsou SharePoint nebo ObjectModel. |
| Typ položky | Typ objektu, který byl přístup nebo upravil. Najdete v tabulce ItemType podrobnosti pro typy objektů. |
| MachineDomainInfo | Informace o zařízení synchronizační operace. Tyto informace je nahlášeno pouze v případě, že je k dispozici v požadavku. |
| MachineId |   Informace o zařízení synchronizační operace. Tyto informace je nahlášeno pouze v případě, že je k dispozici v požadavku. |
| Site_ | Identifikátor GUID lokality, kde se nachází soubor nebo složku přístup uživatele. |
| Source_Name | Typ entity, která aktivuje operace auditování. Možné hodnoty jsou SharePoint nebo ObjectModel. |
| UserAgent | Informace o klienta nebo prohlížeče uživatele. Tato informace jsou poskytovány klienta nebo prohlížeče. |


### <a name="sharepoint-schema"></a>Schéma služby SharePoint
Tyto záznamy jsou vytvořeny při provedení změn konfigurace služby SharePoint.

| Vlastnost | Popis |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| CustomEvent | Volitelný řetězec pro vlastní události. |
| Event_Data |  Volitelné datové části pro vlastní události. |
| ModifiedProperties | Vlastnost je zahrnuté pro správce události, jako je například přidávání uživatele jako člen webu nebo skupinu pro správu kolekce webu. Vlastnost obsahuje název vlastnosti, které bylo změněno (například správce webu skupiny), nová hodnota změněné vlastnosti (takový uživatel, který byl přidán jako správce webu) a předchozí hodnotu upravený objekt. |


### <a name="sharepoint-file-operations"></a>Operace se soubory služby SharePoint
Tyto záznamy jsou vytvořeny v reakci na operací se soubory ve službě SharePoint.

| Vlastnost | Popis |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePointFileOperation |
| DestinationFileExtension | Příponu souboru, který je zkopírovat nebo přesunout. Tato vlastnost se zobrazí pouze pro FileCopied a FileMoved události. |
| DestinationFileName | Název souboru, který je zkopírovat nebo přesunout. Tato vlastnost se zobrazí pouze pro FileCopied a FileMoved události. |
| DestinationRelativeUrl | Adresa URL cílovou složku, kde je soubor zkopírovat nebo přesunout. Kombinace hodnot pro parametry SiteURL, DestinationRelativeURL a DestinationFileName je stejná jako hodnota pro vlastnost ObjectID, což je úplná cesta název souboru, který jste zkopírovali. Tato vlastnost se zobrazí pouze pro FileCopied a FileMoved události. |
| SharingType | Typ oprávnění, které byly přiřazeny uživateli, který prostředek se sdílet s sdílení. Tento uživatel je určen parametrem UserSharedWith. |
| Site_Url | Adresa URL webu, kde je uložený soubor nebo složku přístup uživatele. |
| SourceFileExtension | Příponu souboru, který byl přístupný uživatelem. Tato vlastnost je prázdná, pokud je objekt, který byl přístupný složka. |
| SourceFileName |  Název souboru nebo složky přístup uživatele. |
| SourceRelativeUrl | Adresa URL složky, která obsahuje soubor dostupný uživatelem. Kombinace hodnot pro parametry SiteURL SourceRelativeURL a SourceFileName je stejná jako hodnota pro vlastnost ObjectID, což je název úplná cesta k souboru přístup uživatele. |
| UserSharedWith |  Uživatel, který prostředek se sdílet s. |




## <a name="sample-log-searches"></a>Ukázky hledání v protokolech
V následující tabulce jsou uvedeny ukázky hledání v protokolech pro záznamy aktualizace shromážděné tímto řešením.

| Dotaz | Popis |
| --- | --- |
|Počet všech operací na předplatné služeb Office 365 |OfficeActivity &#124; shrnout count() operací |
|Použití webů služby SharePoint|OfficeActivity &#124; kde OfficeWorkload = ~ "sharepoint" &#124; shrnout count() podle SiteUrl | Řadit podle počtu asc|
|Operace se soubory přístup podle typu uživatele|hledání v OfficeWorkload (OfficeActivity) = ~ "azureactivedirectory selhala" a "Test"|
|Hledání s konkrétní – klíčové slovo|Typ = OfficeActivity OfficeWorkload = azureactivedirectory selhala "Test"|
|Externí akce monitorování na Exchange|OfficeActivity &#124; kde OfficeWorkload = ~ "exchange" a ExternalAccess == true|



## <a name="next-steps"></a>Další postup
* K zobrazení podrobných údajů o aktualizaci použijte Hledání v protokolu služby [Log Analytics](../log-analytics/log-analytics-log-searches.md).
* [Vytvořit vlastní řídicí panely](../log-analytics/log-analytics-dashboards.md) zobrazíte váš oblíbený vyhledávací dotazy Office 365.
* [Vytvářet výstrahy](../log-analytics/log-analytics-alerts.md) proaktivně oznámení důležité aktivit Office 365.  
