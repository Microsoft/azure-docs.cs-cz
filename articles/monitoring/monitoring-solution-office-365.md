---
title: Řešení pro správu Office 365 v Azure | Dokumentace Microsoftu
description: Tento článek poskytuje podrobné informace o konfiguraci a použití služeb Office 365 řešení v Azure.  Obsahuje podrobný popis služeb Office 365 záznamy vytvořené v Log Analytics.
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
ms.date: 08/15/2018
ms.author: bwren
ms.openlocfilehash: e3620bbf92cab926d56c4de0817f833b61cf2b03
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46125081"
---
# <a name="office-365-management-solution-in-azure-preview"></a>Řešení pro správu Office 365 v Azure (Preview)

![Logo Office 365](media/monitoring-solution-office-365/icon.png)

Řešení pro správu Office 365 vám umožní monitorovat vaše prostředí Office 365 ve službě Log Analytics.

- Monitorování aktivity uživatelů na účty Office 365 a analýza vzorců používání a také identifikovat trendy chování. Například můžete extrahovat scénáře konkrétní použití, jako jsou soubory, které jsou sdílené mimo vaši organizaci nebo nejoblíbenější Sharepointové weby.
- Monitorování aktivit správce ke sledování změn konfigurace a operace vysoká oprávnění.
- Detekujte a prošetřete chování uživatele, který lze přizpůsobit potřebám vaší organizace.
- Předvedení audit a dodržování předpisů. Můžete například monitorovat operací přístupu k souboru na důvěrné soubory, které vám pomohou s procesu audit a dodržování předpisů.
- Provádět provozní řešení potíží s použitím [prohledávání protokolů](../log-analytics/log-analytics-log-search.md) nad data aktivit Office 365 vaší organizace.

## <a name="prerequisites"></a>Požadavky
Je nutné provést následující před toto řešení je nainstalovaná a nakonfigurovaná.

- Organizace předplatné služeb Office 365.
- Přihlašovací údaje pro uživatelský účet, který má oprávnění globálního správce.
- Pokud chcete přijímat data auditu, je nutné [konfigurace auditování](https://support.office.com/en-us/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=en-US&rs=en-US&ad=US#PickTab=Before_you_begin) v rámci vašeho předplatného Office 365.  Všimněte si, že [auditování poštovní schránky](https://technet.microsoft.com/library/dn879651.aspx) je samostatně nakonfigurovaný.  Stále můžete řešení nainstalovat a shromažďovat další data, pokud není nakonfigurováno auditování.
 

## <a name="management-packs"></a>Sady Management Pack
Toto řešení není možné nainstalovat všechny sady management Pack v [připojené skupiny pro správu](../log-analytics/log-analytics-om-agents.md).
  
## <a name="install-and-configure"></a>Instalace a konfigurace
Začněte přidáním [řešení Office 365 ke svému předplatnému](monitoring-solutions.md#install-a-management-solution). Až ho přidáte, je nutné provést kroky konfigurace v této části pro získání přístupu k předplatnému Office 365.

### <a name="required-information"></a>Požadované informace
Před zahájením tohoto postupu, shromážděte následující informace.

Z pracovního prostoru Log Analytics:

- Název pracovního prostoru: pracovní prostor, kde budou shromažďovány dat Office 365.
- Název skupiny prostředků: skupinu prostředků, který obsahuje tento pracovní prostor.
- ID předplatného Azure: předplatné, které obsahuje tento pracovní prostor.

Z vašeho předplatného Office 365:

- Uživatelské jméno: E-mailovou adresu účtu správce.
- ID tenanta: Jedinečné ID pro předplatné služeb Office 365.
- ID klienta: řetězec o 16 znacích, který představuje klienta Office 365.
- Tajný kód klienta: Zašifrovaný řetězec potřebné pro ověřování.

### <a name="create-an-office-365-application-in-azure-active-directory"></a>Vytvoření aplikace Office 365 ve službě Azure Active Directory
Prvním krokem je vytvoření aplikace v Azure Active Directory, řešení pro správu bude používat pro přístup k řešení pro Office 365.

1. Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com/).
1. Vyberte **Azure Active Directory** a potom **registrace aplikací**.
1. Klikněte na **Registrace nové aplikace**.

    ![Přidat registraci aplikace](media/monitoring-solution-office-365/add-app-registration.png)
1. Zadejte aplikace **název** a **přihlašovací adresa URL**.  Název by měl být popisný.  Použití _http://localhost_ pro adresu URL a zachovat _webové aplikace a rozhraní API_ pro **typu aplikace**
    
    ![Vytvořit aplikaci](media/monitoring-solution-office-365/create-application.png)
1. Klikněte na tlačítko **vytvořit** a ověřit informace o aplikaci.

    ![Registrovaná aplikace](media/monitoring-solution-office-365/registered-app.png)

### <a name="configure-application-for-office-365"></a>Konfigurace aplikace pro Office 365

1. Klikněte na tlačítko **nastavení** otevřít **nastavení** nabídky.
1. Vyberte **vlastnosti**. Změna **víceklientských** k _Ano_.

    ![Nastavení multitenant](media/monitoring-solution-office-365/settings-multitenant.png)

1. Vyberte **požadovaná oprávnění** v **nastavení** nabídky a pak klikněte na tlačítko **přidat**.
1. Klikněte na tlačítko **vyberte rozhraní API** a potom **rozhraní API pro správu Office 365**. Klikněte na tlačítko **rozhraní API pro správu Office 365**. Klikněte na **Vybrat**.

    ![Vybrat rozhraní API](media/monitoring-solution-office-365/select-api.png)

1. V části **vyberte oprávnění** vyberte následující možnosti pro obě **oprávnění aplikace** a **delegovaná oprávnění**:
    - Přečíst informace o stavu služby pro organizaci
    - Čtení dat o aktivitách pro vaši organizaci
    - Přečíst sestavy aktivit pro organizaci

    ![Vybrat rozhraní API](media/monitoring-solution-office-365/select-permissions.png)

1. Klikněte na tlačítko **vyberte** a potom **provádí**.
1. Klikněte na tlačítko **udělit oprávnění** a potom klikněte na tlačítko **Ano** žádost o ověření.

    ![Udělení oprávnění](media/monitoring-solution-office-365/grant-permissions.png)

### <a name="add-a-key-for-the-application"></a>Přidat klíč pro aplikaci

1. Vyberte **klíče** v **nastavení** nabídky.
1. Zadejte **popis** a **doba trvání** nového klíče.
1. Klikněte na tlačítko **Uložit** a zkopírujte **hodnota** , který je generován.

    ![Klíče](media/monitoring-solution-office-365/keys.png)

### <a name="add-admin-consent"></a>Přidat souhlas správce
Pokud chcete povolit účet správce poprvé, je nutné zadat souhlas správce pro aplikaci. Můžete to provést pomocí skriptu prostředí PowerShell. 

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
    $WorkspaceLocation
    
    Function AdminConsent{
    
    $domain='login.microsoftonline.com'
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
                             $domain='login.microsoftonline.us'; break}
           default {$OfficeAppClientId="55b65fb5-b825-43b5-8972-c8b6875867c1";
                    $domain='login.windows-ppe.net'; break} #Int
        }
    
        $domain
        Start-Process -FilePath  "https://$($domain)/common/adminconsent?client_id=$($OfficeAppClientId)&state=12345"
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

1. Zobrazí se podobná té následující okno. Klikněte na tlačítko **přijmout**.
    
    ![Souhlas správce](media/monitoring-solution-office-365/admin-consent.png)

### <a name="subscribe-to-log-analytics-workspace"></a>Přihlaste se k pracovnímu prostoru Log Analytics odběru
Posledním krokem je přihlášení k odběru aplikace do pracovního prostoru Log Analytics. Můžete také provést pomocí skriptu prostředí PowerShell.

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
                                    'contentTypes':'Audit.Exchange,Audit.AzureActiveDirectory,Audit.Sharepoint'
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

### <a name="troubleshooting"></a>Řešení potíží

Při pokusu o vytvoření odběru po předplatné už existuje, může se zobrazit následující chyba.

```
Invoke-WebRequest : {"Message":"An error has occurred."}
At C:\Users\v-tanmah\Desktop\ps scripts\office365_subscription.ps1:161 char:19
+ $officeresponse = Invoke-WebRequest @Officeparams
+                   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidOperation: (System.Net.HttpWebRequest:HttpWebRequest) [Invoke-WebRequest], WebException
    + FullyQualifiedErrorId : WebCmdletWebResponseException,Microsoft.PowerShell.Commands.InvokeWebRequestCommand 
```

Pokud nejsou zadány neplatné hodnoty parametrů, může se zobrazit následující chyba.

```
Select-AzureRmSubscription : Please provide a valid tenant or a valid subscription.
At line:12 char:18
+ ... cription = (Select-AzureRmSubscription -SubscriptionId $($Subscriptio ...
+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzureRmContext], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.SetAzureRMContextCommand

```

## <a name="uninstall"></a>Odinstalace
Můžete odebrat pomocí procesu v řešení pro správu Office 365 [odebrat řešení pro správu](../monitoring/monitoring-solutions.md#remove-a-management-solution). Tato data se shromažďují z Office 365 do Log Analytics ale nezastaví. Pomocí následujícího postupu zrušit odběr služeb Office 365 a shromažďování dat ukončit.

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
Řešení Office 365 nenačítá data z libovolného [agenty OMS](../log-analytics/log-analytics-data-sources.md).  Načte data přímo z Office 365.

### <a name="collection-frequency"></a>Četnost shromažďování dat
Může trvat několik hodin se zpočátku shromážděná data. Jakmile začne shromažďování, odešle Office 365 [oznámení webhooku](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference#receiving-notifications) s podrobná data do Log Analytics pokaždé, když je vytvořen záznam. Tento záznam je k dispozici ve službě Log Analytics v rámci pár minut od jejich obdržení.

## <a name="using-the-solution"></a>Použití řešení
Když přidáte řešení pro Office 365 do pracovního prostoru Log Analytics **Office 365** se přidá dlaždice na řídicí panel. Na této dlaždici se zobrazuje počet a grafické vyjádření počtu počítačů ve vašem prostředí a jejich kompatibilita s aktualizacemi.<br><br>
![Dlaždice souhrnu Office 365](media/monitoring-solution-office-365/tile.png)  

Klikněte na **Office 365** otevřete dlaždici **Office 365** řídicího panelu.

![Řídicí panel pro Office 365](media/monitoring-solution-office-365/dashboard.png)  

Řídicí panel obsahuje sloupce v následující tabulce. Každý sloupec uvádí hlavní deset upozornění podle počtu odpovídajících kritériím tohoto sloupce pro zadaný obor a časový rozsah. Můžete spustit prohledávání protokolu, který poskytuje úplný seznam klikneme všechny v dolní části sloupce nebo kliknutím na záhlaví sloupce.

| Sloupec | Popis |
|:--|:--|
| Operace | Poskytuje informace o aktivní uživatelé za všechny monitorované předplatných Office 365. Také budete moci zobrazit počet aktivit, ke kterým dochází v čase.
| Výměna | Zobrazí rozpis aktivity systému Exchange Server, například přidat poštovní schránku oprávnění nebo nastavení poštovní schránky. |
| SharePoint | Zobrazuje nejvyšší aktivity, že uživatelé provádět na dokumentů služby SharePoint. Při podrobné analýze z této dlaždice na stránce vyhledávání zobrazuje podrobnosti o těchto činností, jako je například cílového dokumentu a umístění této aktivity. Například v případě události přístup k souboru se budete moci naleznete v dokumentu, která se právě využívají, jeho přidružený účet názvu a IP adresy. |
| Azure Active Directory | Obsahuje hlavní uživatelské aktivity, jako je například resetování hesla uživatele a pokusů o přihlášení. Při podrobné analýze, bude moct zobrazit podrobnosti o těchto činností, jako výsledný stav. Toto je nejčastěji užitečné, pokud chcete monitorovat podezřelé aktivity ve službě Azure Active Directory. |




## <a name="log-analytics-records"></a>Záznamy služby Log Analytics

Mají všechny záznamy vytvořené v pracovním prostoru Log Analytics řešení pro Office 365 **typ** z **OfficeActivity**.  **OfficeWorkload** vlastnost určuje, jaké služby Office 365 záznam označuje – Exchange, AzureActiveDirectory, SharePoint nebo OneDrive.  **Detailů** vlastnost určuje typ operace.  Vlastnosti pro každý typ operace se bude lišit a jsou uvedeny v následujících tabulkách.

### <a name="common-properties"></a>Společné vlastnosti
Následující vlastnosti jsou společné pro všechny záznamy Office 365.

| Vlastnost | Popis |
|:--- |:--- |
| Typ | *OfficeActivity* |
| Když | IP adresa zařízení použitá při protokolování aktivity. IP adresa se zobrazí ve formátu adresy IPv4 nebo IPv6. |
| OfficeWorkload | Služby Office 365, odkazující na záznam.<br><br>AzureActiveDirectory<br>Výměna<br>SharePoint|
| Operace | Název aktivity uživatele nebo správce.  |
| Kódu organizace | Identifikátor GUID pro tenanta Office 365 vaší organizace. Tato hodnota bude vždy stejné pro svou organizaci, bez ohledu na služby Office 365, ve kterém se vyskytuje. |
| Typ záznamu | Typ operace provést. |
| ResultStatus | Určuje, zda byla akce (zadaná ve vlastnosti Operation) úspěšná. Možné hodnoty jsou Succeeded, PartiallySucceded nebo Failed. Pro aktivitu správy serveru Exchange, hodnotu buď True nebo False. |
| UserId | Hlavní název uživatele (hlavní název uživatele) uživatele, který provedl akci, jejímž výsledkem bylo zaprotokolování záznamu například my_name@my_domain_name. Všimněte si, že záznamy aktivity prováděné systémovými účty (například SHAREPOINT\system nebo NTAUTHORITY\SYSTEM) jsou zahrnuté také. | 
| Vlastnosti userkey jedná | Alternativní ID pro uživatele identifikovaného ve vlastnosti ID uživatele.  Například tato vlastnost naplní jedinečné ID účtu služby passport (PUID) pro události prováděné uživateli na Sharepointu, Onedrivu pro firmy a serveru Exchange. Tato vlastnost může také zadejte stejnou hodnotu jako vlastnost ID uživatele pro události, ke kterým dochází v jiných službách a akcích prováděné systémovými účty|
| UserType | Typ uživatele, který provedl operaci.<br><br>Správce<br>Aplikace<br>DcAdmin<br>Pravidelné<br>Rezervováno<br>ServicePrincipal<br>Systémový |


### <a name="azure-active-directory-base"></a>Azure Active Directory base
Následující vlastnosti jsou společné pro všechny záznamy v Azure Active Directory.

| Vlastnost | Popis |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| Typ záznamu     | AzureActiveDirectory |
| AzureActiveDirectory_EventType | Typ události služby Azure AD. |
| ExtendedProperties | Rozšířené vlastnosti událostí Azure AD. |


### <a name="azure-active-directory-account-logon"></a>Azure přihlašovací účet služby Active Directory
Tyto záznamy se vytvoří, když se pokusí přihlásit uživatele služby Active Directory.

| Vlastnost | Popis |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| Typ záznamu     | AzureActiveDirectoryAccountLogon |
| Aplikace | Aplikace, která aktivuje událost přihlášení účtu, jako je například Office 15. |
| Klient | Podrobnosti o klientovi zařízení, operační systém zařízení a prohlížeči v zařízení, které se používá události přihlášení účtu. |
| Stavu přihlášení | Tato vlastnost je k dispozici přímo z OrgIdLogon.LoginStatus. Mapování různých zajímavé nezdařených pokusů o přihlášení může udělat upozorňování algoritmy. |
| UserDomain | Tenant informací o identitě (TÍ). | 


### <a name="azure-active-directory"></a>Azure Active Directory
Tyto záznamy jsou vytvořeny při změnu nebo přidání objektů služby Azure Active Directory.

| Vlastnost | Popis |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| Typ záznamu     | AzureActiveDirectory |
| AADTarget | Uživatel, který byla provedena akce (identifikovaných podle vlastnosti Operation). |
| Objekt actor | Uživatel nebo instanční objekt, který tuto akci provedl. |
| ActorContextId | Identifikátor GUID, který objekt actor patří do organizace. |
| ActorIpAddress | IP adresa objektu actor ve formátu adresy IPV4 nebo IPV6. |
| InterSystemsId | Identifikátor GUID, které sledují akce komponentami v rámci služby Office 365. |
| IntraSystemId |   Identifikátor GUID, který je generován Azure Active Directory a sledovat akce. |
| SupportTicketId | Pro akci v situacích, "act-on-behalf-of" podporovat ID lístku. |
| TargetContextId | Identifikátor GUID, který cílový uživatel patří do organizace. |


### <a name="data-center-security"></a>Zabezpečení datového centra
Tyto záznamy jsou vytvořeny z dat auditu zabezpečení dat ve službě System Center.  

| Vlastnost | Popis |
|:--- |:--- |
| EffectiveOrganization | Název tenanta, která byla cílem ke zvýšení úrovně oprávnění nebo rutiny v. |
| ElevationApprovedTime | Časové razítko pro kdy byl schválen zvýšení oprávnění. |
| ElevationApprover | Jméno manažera společnosti Microsoft. |
| ElevationDuration | Doba trvání, pro který byl aktivní zvýšení oprávnění. |
| ElevationRequestId |  Jedinečný identifikátor pro žádost o zvýšení oprávnění. |
| ElevationRole | Bylo vyžádáno role zvýšení oprávnění. |
| ElevationTime | Čas zahájení zvýšení oprávnění. |
| Start_Time | Počáteční čas spuštění rutiny. |


### <a name="exchange-admin"></a>Správce Exchange
Tyto záznamy se vytvoří, když dojde ke změně konfigurace systému Exchange.

| Vlastnost | Popis |
|:--- |:--- |
| OfficeWorkload | Výměna |
| Typ záznamu     | ExchangeAdmin |
| ExternalAccess |  Určuje, zda rutina byla spuštěna uživatelem ve vaší organizaci, mají pracovníci společnosti Microsoft datacenter nebo účet služby datacenter nebo delegovaný správce. Hodnota False označuje, že rutina byla spuštěna uživatelem ve vaší organizaci. Hodnotu True označuje, že byl rutinu spustit personál datového centra, účet služby datacenter nebo delegovaný správce. |
| ModifiedObjectResolvedName |  Toto je popisný název objektu, který změnil tuto rutinu. To je zaznamenána pouze v případě, že rutina upraví objekt. |
| Název organizace | Název tenanta. |
| OriginatingServer | Název serveru, ze kterého se provedl rutinu. |
| Parametry | Název a hodnotu pro všechny parametry, které byly použity pomocí rutiny, který je identifikován ve vlastnosti operace. |


### <a name="exchange-mailbox"></a>Poštovní schránky serveru Exchange
Tyto záznamy jsou vytvořeny při poštovní schránky systému Exchange se provedly změny nebo doplnění.

| Vlastnost | Popis |
|:--- |:--- |
| OfficeWorkload | Výměna |
| Typ záznamu     | ExchangeItem |
| ClientInfoString | Informace o e-mailovém klientovi, který byl použit k provedení této operace, jako je například verze prohlížeče, verze aplikace Outlook a informace o mobilních zařízeních. |
| Client_IPAddress | IP adresa zařízení, která byla použita při operaci protokolu byla zaznamenána. IP adresa se zobrazí ve formátu adresy IPv4 nebo IPv6. |
| ClientMachineName | Název počítače, který je hostitelem klientovi aplikace Outlook. |
| ClientProcessName | E-mailového klienta, která byla použita pro přístup k poštovní schránce. |
| ClientVersion | Verze e-mailovém klientovi. |
| InternalLogonType | Vyhrazeno pro interní použití. |
| Logon_Type | Určuje typ uživatele, který přistupuje k poštovní schránce a provést operaci, která byla zaznamenána. |
| LogonUserDisplayName |    Popisný název uživatele, který provedl operaci. |
| LogonUserSid | Identifikátor SID uživatele, který provedl operaci. |
| MailboxGuid | Identifikátor GUID Exchange, která se použila poštovní schránky. |
| MailboxOwnerMasterAccountSid | Hlavní účet poštovní schránky vlastníka účtu SID. |
| MailboxOwnerSid | Identifikátor SID vlastníka poštovní schránky. |
| MailboxOwnerUPN | E-mailovou adresu osoby, která je vlastníkem poštovní schránku, která se použila. |


### <a name="exchange-mailbox-audit"></a>Auditování poštovní schránky systému Exchange
Tyto záznamy jsou vytvořeny při vytvoření položky auditu poštovních schránek.

| Vlastnost | Popis |
|:--- |:--- |
| OfficeWorkload | Výměna |
| Typ záznamu     | ExchangeItem |
| Položka | Představuje položku, na kterém se provedla operaci | 
| SendAsUserMailboxGuid | Identifikátor GUID Exchange poštovní schránku, která se použila k odeslání e-mailu. |
| SendAsUserSmtp | Adresa SMTP uživatele, který je právě zosobnit. |
| SendonBehalfOfUserMailboxGuid | Identifikátor GUID Exchange poštovní schránku, která se použila k odesílání pošty jménem. |
| SendOnBehalfOfUserSmtp | Adresa SMTP uživatele, jehož jménem je odesláno e-mailu. |


### <a name="exchange-mailbox-audit-group"></a>Skupina auditu poštovních schránek systému Exchange
Tyto záznamy jsou vytvořeny při skupiny Exchange se provedly změny nebo doplnění.

| Vlastnost | Popis |
|:--- |:--- |
| OfficeWorkload | Výměna |
| OfficeWorkload | ExchangeItemGroup |
| AffectedItems | Informace o jednotlivých položkách ve skupině. |
| CrossMailboxOperations | Označuje, že operace nepodílí více než jedné poštovní schránky. |
| DestMailboxId | Nastaví jenom v případě, že parametr CrossMailboxOperations má hodnotu True. Určuje poštovní schránky cílového GUID. |
| DestMailboxOwnerMasterAccountSid | Nastaví jenom v případě, že parametr CrossMailboxOperations má hodnotu True. Určuje identifikátor SID pro hlavní účet SID vlastníka cílové poštovní schránky. |
| DestMailboxOwnerSid | Nastaví jenom v případě, že parametr CrossMailboxOperations má hodnotu True. Určuje identifikátor SID cílové poštovní schránky. |
| DestMailboxOwnerUPN | Nastaví jenom v případě, že parametr CrossMailboxOperations má hodnotu True. Určuje hlavní název uživatele vlastníka cílové poštovní schránky. |
| DestFolder | Cílová složka pro operace, jako je přesunout. |
| Složka | Složka, ve kterém se nachází skupina položek. |
| Složky |     Informace o zapojenou do činnosti; zdrojové složky například, pokud jsou vybraná a pak odstranit složky. |


### <a name="sharepoint-base"></a>Základní služby SharePoint
Tyto vlastnosti jsou společné pro všechny záznamy služby SharePoint.

| Vlastnost | Popis |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| EventSource | Určuje, že k události došlo ve službě SharePoint. Možné hodnoty jsou služby SharePoint nebo ObjectModel. |
| Typ položky | Typ objektu, který byl otevřeny nebo upraveny. Najdete v tabulce ItemType podrobnosti na typy objektů. |
| MachineDomainInfo | Informace o operacích synchronizace zařízení. Tyto informace je nahlášeno pouze v případě, že je k dispozici v požadavku. |
| MachineId |   Informace o operacích synchronizace zařízení. Tyto informace je nahlášeno pouze v případě, že je k dispozici v požadavku. |
| Site_ | Identifikátor GUID serveru, kde se nachází soubor nebo složku, které zobrazuje uživatel. |
| Source_Name | Entita, která aktivuje toto auditování se provádí operaci. Možné hodnoty jsou služby SharePoint nebo ObjectModel. |
| UserAgent | Informace o klienta nebo prohlížeče uživatele. Tato informace jsou poskytovány klienta nebo prohlížeče. |


### <a name="sharepoint-schema"></a>Schéma služby SharePoint
Tyto záznamy se vytvoří, když se změny konfigurace provedly do Sharepointu.

| Vlastnost | Popis |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| CustomEvent | Volitelný řetězec pro vlastní události. |
| Event_Data |  Volitelné datové části pro vlastní události. |
| ModifiedProperties | Vlastnost je zahrnuté pro události správy, jako je například přidávání uživatele jako člena webu nebo skupiny správce kolekce webu. Vlastnost obsahuje název vlastnosti, které bylo změněno (například skupiny Správci serveru), nová hodnota změněné vlastnosti (tyto uživatele, který se přidal jako správce serveru) a předchozí hodnotu změněný objekt. |


### <a name="sharepoint-file-operations"></a>Operace se soubory služby SharePoint
Tyto záznamy jsou vytvořeny v reakci na operace se soubory ve službě SharePoint.

| Vlastnost | Popis |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePointFileOperation |
| DestinationFileExtension | Přípona souboru, který je zkopírovaný ani přesunutý. Tato vlastnost se zobrazí pouze pro FileCopied a FileMoved události. |
| DestinationFileName | Název souboru, který je zkopírovaný ani přesunutý. Tato vlastnost se zobrazí pouze pro FileCopied a FileMoved události. |
| DestinationRelativeUrl | Adresa URL cílové složky, kde je soubor zkopírován nebo přesunut. Kombinace hodnot pro parametry SiteURL, DestinationRelativeURL a DestinationFileName je stejná jako hodnota pro vlastnost ID objektu, který je úplný název cesty k souboru, který jste zkopírovali. Tato vlastnost se zobrazí pouze pro FileCopied a FileMoved události. |
| SharingType | Typ oprávnění, které byly přiřazeny uživateli, který prostředek sdílí s sdílení. Tento uživatel je identifikován parametrem UserSharedWith. |
| Site_Url | Adresa URL webu, kde se nachází soubor nebo složku, které zobrazuje uživatel. |
| SourceFileExtension | Přípona souboru, která se použila uživatelem. Tato vlastnost je prázdné, pokud objekt, který se použila je složka. |
| SourceFileName |  Název souboru nebo složky, které zobrazuje uživatel. |
| SourceRelativeUrl | Adresa URL ke složce, která obsahuje soubor, které zobrazuje uživatel. Kombinace hodnot pro parametry SiteURL SourceRelativeURL a SourceFileName je stejná jako hodnota pro vlastnost ID objektu, který je úplný název cesty k souboru přístup uživatele. |
| UserSharedWith |  Uživatel, který prostředek sdílí s. |




## <a name="sample-log-searches"></a>Ukázky hledání v protokolech
V následující tabulce jsou uvedeny ukázky hledání v protokolech pro záznamy aktualizace shromážděné tímto řešením.

| Dotaz | Popis |
| --- | --- |
|Počet všech operací v rámci předplatného Office 365 |OfficeActivity &#124; shrnout count() by operace |
|Využití webů služby SharePoint|OfficeActivity &#124; kde OfficeWorkload = ~ "sharepoint" &#124; shrnout count() by SiteUrl | Seřadit podle počtu asc|
|Operacemi přístupu k souboru jednotlivými typy uživatelů|hledání v OfficeWorkload (OfficeActivity) = ~ "azureactivedirectory" a "Test"|
|Dejte hledat klíčové klíčovému slovu.|Typ = OfficeActivity OfficeWorkload "Test" azureactivedirectory =|
|Externí akce monitorování na serveru Exchange|OfficeActivity &#124; kde OfficeWorkload = ~ "exchange" a ExternalAccess == true|



## <a name="next-steps"></a>Další postup
* K zobrazení podrobných údajů o aktualizaci použijte Hledání v protokolu služby [Log Analytics](../log-analytics/log-analytics-log-searches.md).
* [Vytvářejte vlastní řídicí panely](../log-analytics/log-analytics-dashboards.md) zobrazíte váš oblíbený vyhledávací dotazy Office 365.
* [Vytvořit upozornění](../log-analytics/log-analytics-alerts.md) proaktivně upozornit důležité aktivit Office 365.  
