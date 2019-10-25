---
title: Řešení pro správu Office 365 v Azure | Microsoft Docs
description: Tento článek poskytuje podrobné informace o konfiguraci a použití řešení Office 365 v Azure.  Obsahuje podrobný popis záznamů Office 365 vytvořených v Azure Monitor.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/13/2019
ms.openlocfilehash: 84af0484ed9fb792bef6bbbe9c53395b569acb3c
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793870"
---
# <a name="office-365-management-solution-in-azure-preview"></a>Řešení pro správu Office 365 v Azure (Preview)

![Logo Office 365](media/solution-office-365/icon.png)


> [!NOTE]
> Doporučená metoda pro instalaci a konfiguraci řešení Office 365 povoluje [konektor sady office 365](../../sentinel/connect-office-365.md) ve [službě Azure Sentinel](../../sentinel/overview.md) namísto použití kroků v tomto článku. Toto je aktualizovaná verze řešení Office 365 s vylepšeným prostředím pro konfiguraci. Pokud chcete připojit protokoly služby Azure AD, můžete použít buď [konektor Azure Sentinel Azure AD](../../sentinel/connect-azure-active-directory.md) , nebo [nakonfigurovat nastavení diagnostiky Azure AD](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md), které poskytuje rozsáhlá data protokolů než protokoly správy sady Office 365. 
>
> Při [připojování služby Azure Sentinel](../../sentinel/quickstart-onboard.md)zadejte Log Analytics pracovní prostor, ve kterém chcete řešení Office 365 nainstalovat. Jakmile konektor povolíte, bude řešení dostupné v pracovním prostoru a bude použito přesně stejně jako jakékoli jiné monitorovací řešení, které jste nainstalovali.
>
> Uživatelé cloudu pro státní správu Azure musí nainstalovat Office 365 podle kroků v tomto článku, protože Azure Sentinel ještě není v cloudu pro státní správu k dispozici.

Řešení pro správu sady Office 365 umožňuje monitorovat prostředí sady Office 365 v Azure Monitor.

- Monitorujte aktivity uživatelů na účtech Office 365, abyste mohli analyzovat vzorce používání a identifikovat trendy chování. Můžete například extrahovat konkrétní scénáře použití, například soubory, které jsou sdíleny mimo vaši organizaci nebo nejoblíbenější weby služby SharePoint.
- Monitorujte aktivity správce a sledujte změny konfigurace nebo operace s vysokými oprávněními.
- Umožňuje detekovat a prozkoumat nežádoucí chování uživatelů, které je možné přizpůsobit potřebám vaší organizace.
- Předvedení auditu a dodržování předpisů. Například můžete monitorovat operace přístupu k souborům u důvěrných souborů, které vám pomůžou s procesem auditu a dodržování předpisů.
- Řešení potíží s operačním systémem pomocí [dotazů protokolu](../log-query/log-query-overview.md) na data o aktivitách vaší organizace v Office 365.


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Předpoklady

Před instalací a konfigurací tohoto řešení se vyžaduje následující:

- Předplatné organizace Office 365.
- Pověření pro uživatelský účet, který je globálním správcem.
- Pokud chcete přijímat data auditu, musíte [nakonfigurovat auditování](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=en-US&rs=en-US&ad=US#PickTab=Before_you_begin) v předplatném sady Office 365.  [Auditování poštovní schránky](https://technet.microsoft.com/library/dn879651.aspx) je nakonfigurované samostatně.  Přesto můžete řešení nainstalovat a shromažďovat další data, pokud není nakonfigurováno auditování.
 

## <a name="management-packs"></a>Sady Management Pack

Toto řešení neinstaluje žádné sady Management Pack v [připojených skupinách pro správu](../platform/om-agents.md).
  

## <a name="install-and-configure"></a>Instalace a konfigurace

Začněte přidáním [řešení Office 365 do svého předplatného](solutions.md#install-a-monitoring-solution). Po přidání musíte provést kroky konfigurace v této části, abyste měli přístup k vašemu předplatnému Office 365.

### <a name="required-information"></a>Požadované informace

Než začnete s tímto postupem, shromážděte následující informace.

Z Log Analytics pracovního prostoru:

- Název pracovního prostoru: pracovní prostor, ve kterém se shromažďují data Office 365.
- Název skupiny prostředků: Skupina prostředků, která obsahuje pracovní prostor.
- ID předplatného Azure: předplatné, které obsahuje pracovní prostor.

Z vašeho předplatného Office 365:

- Username: e-mailová adresa účtu správce.
- ID tenanta: jedinečné ID pro předplatné Office 365.

Při vytváření a konfiguraci aplikace Office 365 v Azure Active Directory by se měly shromažďovat následující informace:

- ID aplikace (klienta): 16 znaků řetězec, který představuje klienta Office 365.
- Tajný kód klienta: pro ověřování je nutný šifrovaný řetězec.

### <a name="create-an-office-365-application-in-azure-active-directory"></a>Vytvoření aplikace Office 365 v Azure Active Directory

Prvním krokem je vytvoření aplikace v Azure Active Directory, že řešení pro správu bude používat pro přístup k řešení Office 365.

1. Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com/).
1. Vyberte **Azure Active Directory** a potom **Registrace aplikací**.
1. Klikněte na **Nová registrace**.

    ![Přidat registraci aplikace](media/solution-office-365/add-app-registration.png)
1. Zadejte **název**aplikace. Pro **podporované typy účtů**vyberte **účty v jakémkoli organizačním adresáři (ve víceklientském adresáři Azure AD)** .
    
    ![Vytvořit aplikaci](media/solution-office-365/create-application.png)
1. Klikněte na **Registrovat** a ověřte informace o aplikaci.

    ![Registrovaná aplikace](media/solution-office-365/registered-app.png)

1. Uložte si ID aplikace (klienta) spolu se zbytkem shromážděných informací.


### <a name="configure-application-for-office-365"></a>Konfigurace aplikace pro Office 365

1. Vyberte **ověřování** a ověřte, že se v části **podporované typy účtů**vybraly **účty v libovolné organizační složce (ve víceklientském adresáři Azure AD)** .

    ![Nastavení víceklientské architektury](media/solution-office-365/settings-multitenant.png)

1. Vyberte **oprávnění rozhraní API** a pak **přidejte oprávnění**.
1. Klikněte na **rozhraní API pro správu sady Office 365**. 

    ![Vybrat rozhraní API](media/solution-office-365/select-api.png)

1. V části **jaký typ oprávnění vaše aplikace vyžaduje?** vyberte následující možnosti pro **oprávnění aplikace** i **delegovaná oprávnění**:
   - Přečíst informace o stavu služby pro vaši organizaci
   - Čtení dat o aktivitách ve vaší organizaci
   - Čtení sestav aktivit pro vaši organizaci

     ![Vybrat rozhraní API](media/solution-office-365/select-permissions-01.png)![Vybrat rozhraní API](media/solution-office-365/select-permissions-02.png)

1. Klikněte na tlačítko **Přidat oprávnění**.
1. Klikněte na **udělit souhlas správce** a po zobrazení výzvy k ověření klikněte na **Ano** .


### <a name="add-a-secret-for-the-application"></a>Přidání tajného klíče pro aplikaci

1. Vyberte **certifikáty & tajných** kódů a pak **nový tajný klíč klienta**.

    ![Klíče](media/solution-office-365/secret.png)
 
1. Zadejte **Popis** a **dobu trvání** nového klíče.
1. Klikněte na tlačítko **Přidat** a poté uložte **hodnotu** , která byla vygenerována jako tajný klíč klienta, spolu se zbytkem shromážděných informací.

    ![Klíče](media/solution-office-365/keys.png)

### <a name="add-admin-consent"></a>Přidat souhlas správce

Chcete-li povolit účet správce poprvé, je nutné pro aplikaci zadat souhlas správce. Můžete to provést pomocí skriptu PowerShellu. 

1. Uložte následující skript jako *office365_consent. ps1*.

    ```powershell
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,     
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId
    )
    
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    
    IF ($Subscription -eq $null)
        {Login-AzAccount -ErrorAction Stop}
    $Subscription = (Select-AzSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $Workspace = (Set-AzOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
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

2. Spusťte skript pomocí následujícího příkazu. Pro přihlašovací údaje se zobrazí dvakrát výzva. Nejdřív zadejte přihlašovací údaje pro svůj pracovní prostor Log Analytics a pak přihlašovací údaje globálního správce pro vašeho tenanta Office 365.

    ```
    .\office365_consent.ps1 -WorkspaceName <Workspace name> -ResourceGroupName <Resource group name> -SubscriptionId <Subscription ID>
    ```

    Příklad:

    ```
    .\office365_consent.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631- yyyyyyyyyyyy'
    ```

1. Zobrazí se okno podobné tomu jako na následujícím obrázku. Klikněte na **přijmout**.
    
    ![Souhlas správce](media/solution-office-365/admin-consent.png)

> [!NOTE]
> Je možné, že budete přesměrováni na stránku, která neexistuje. Vezměte ho jako úspěšný.

### <a name="subscribe-to-log-analytics-workspace"></a>Přihlášení k odběru Log Analytics pracovního prostoru

Posledním krokem je přihlášení k odběru aplikace do vašeho pracovního prostoru Log Analytics. Provedete to také pomocí skriptu PowerShellu.

Posledním krokem je přihlášení k odběru aplikace do vašeho pracovního prostoru Log Analytics. Provedete to také pomocí skriptu PowerShellu.

1. Uložte následující skript jako *office365_subscription. ps1*.

    ```powershell
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
        {Login-AzAccount -ErrorAction Stop}
    $Subscription = (Select-AzSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    $Workspace = (Set-AzOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
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
    $global:SubscriptionID = $Subscription.Subscription.Id
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURIARM=$ARMResource
    # Authenticate and Acquire Token 
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $platformParameters = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.PlatformParameters" -ArgumentList "Auto"
    $global:authResultARM = $authContext.AcquireTokenAsync($resourceAppIdURIARM, $clientId, $redirectUri, $platformParameters)
    $global:authResultARM.Wait()
    $authHeader = $global:authResultARM.Result.CreateAuthorizationHeader()

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
    $authHeader = $global:authResultARM.Result.CreateAuthorizationHeader()
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
    $authHeader = $global:authResultARM.Result.CreateAuthorizationHeader()
    $SubscriptionId   =  $Subscription[0].Subscription.Id
    $OfficeAPIUrl = $ARMResource + 'subscriptions/' + $SubscriptionId + '/resourceGroups/' + $ResourceGroupName + '/providers/Microsoft.OperationalInsights/workspaces/' + $WorkspaceName + '/datasources/office365datasources_' + $SubscriptionId + $OfficeTennantId + '?api-version=2015-11-01-preview'
    
    $OfficeBodyString = "{
                    'properties': {
                                    'AuthProvider':'Office365',
                                    'office365TenantID': '" + $OfficeTennantId + "',
                                    'connectionID': 'office365connection_" + $SubscriptionId + $OfficeTennantId + "',
                                    'office365AdminUsername': '" + $OfficeUsername + "',
                                    'contentTypes':'Audit.Exchange,Audit.AzureActiveDirectory,Audit.SharePoint'
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

2. Spusťte skript s následujícím příkazem:

    ```
    .\office365_subscription.ps1 -WorkspaceName <Log Analytics workspace name> -ResourceGroupName <Resource Group name> -SubscriptionId <Subscription ID> -OfficeUsername <OfficeUsername> -OfficeTennantID <Tenant ID> -OfficeClientId <Client ID> -OfficeClientSecret <Client secret>
    ```

    Příklad:

    ```powershell
    .\office365_subscription.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631-yyyyyyyyyyyy' -OfficeUsername 'admin@contoso.com' -OfficeTennantID 'ce4464f8-a172-4dcf-b675-xxxxxxxxxxxx' -OfficeClientId 'f8f14c50-5438-4c51-8956-zzzzzzzzzzzz' -OfficeClientSecret 'y5Lrwthu6n5QgLOWlqhvKqtVUZXX0exrA2KRHmtHgQb='
    ```

### <a name="troubleshooting"></a>Řešení potíží

Pokud je vaše aplikace už přihlášená k tomuto pracovnímu prostoru nebo pokud je tento tenant přihlášený k odběru jiného pracovního prostoru, může se zobrazit následující chyba.

```Output
Invoke-WebRequest : {"Message":"An error has occurred."}
At C:\Users\v-tanmah\Desktop\ps scripts\office365_subscription.ps1:161 char:19
+ $officeresponse = Invoke-WebRequest @Officeparams
+                   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidOperation: (System.Net.HttpWebRequest:HttpWebRequest) [Invoke-WebRequest], WebException
    + FullyQualifiedErrorId : WebCmdletWebResponseException,Microsoft.PowerShell.Commands.InvokeWebRequestCommand 
```

Pokud jsou zadány neplatné hodnoty parametrů, může se zobrazit následující chyba.

```Output
Select-AzSubscription : Please provide a valid tenant or a valid subscription.
At line:12 char:18
+ ... cription = (Select-AzSubscription -SubscriptionId $($Subscriptio ...
+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzContext], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.SetAzContextCommand

```

## <a name="uninstall"></a>Odinstalace

Řešení pro správu Office 365 můžete odebrat pomocí procesu v části [Odebrání řešení pro správu](solutions.md#remove-a-monitoring-solution). Tím se nezastaví shromažďování dat ze sady Office 365 do Azure Monitor i když. Pomocí níže uvedeného postupu můžete zrušit odběr Office 365 a zastavit shromažďování dat.

1. Uložte následující skript jako *office365_unsubscribe. ps1*.

    ```powershell
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId,
        [Parameter(Mandatory=$True)][string]$OfficeTennantId
    )
    $line='#-------------------------------------------------------------------------------------------------------------------------------------------------------------------------'
    
    $line
    IF ($Subscription -eq $null)
        {Login-AzAccount -ErrorAction Stop}
    $Subscription = (Select-AzSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    $Workspace = (Set-AzOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
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

2. Spusťte skript s následujícím příkazem:

    ```
    .\office365_unsubscribe.ps1 -WorkspaceName <Log Analytics workspace name> -ResourceGroupName <Resource Group name> -SubscriptionId <Subscription ID> -OfficeTennantID <Tenant ID> 
    ```

    Příklad:

    ```powershell
    .\office365_unsubscribe.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631-yyyyyyyyyyyy' -OfficeTennantID 'ce4464f8-a172-4dcf-b675-xxxxxxxxxxxx'
    ```

## <a name="data-collection"></a>Shromažďování dat

### <a name="supported-agents"></a>Podporovaní agenti

Řešení Office 365 nenačítá data z žádných [agentů Log Analytics](../platform/agent-data-sources.md).  Načte data přímo z Office 365.

### <a name="collection-frequency"></a>Četnost shromažďování dat

Shromažďování dat na začátku může trvat několik hodin. Po zahájení shromažďování se sada Office 365 pošle [oznámení Webhooku](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference#receiving-notifications) s podrobnými daty, která se Azure monitor pokaždé, když se vytvoří záznam. Tento záznam je k dispozici v Azure Monitor během několika minut po přijetí.

## <a name="using-the-solution"></a>Použití řešení

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Když do svého pracovního prostoru Log Analytics přidáte řešení Office 365, na řídicí panel se přidá dlaždice **Office 365** . Na této dlaždici se zobrazuje počet a grafické vyjádření počtu počítačů ve vašem prostředí a jejich kompatibilita s aktualizacemi.<br><br>
![dlaždici](media/solution-office-365/tile.png) na souhrn Office 365  

Kliknutím na dlaždici **office 365** otevřete řídicí panel **Office 365** .

![Řídicí panel Office 365](media/solution-office-365/dashboard.png)  

Řídicí panel obsahuje sloupce v následující tabulce. V každém sloupci je uveden seznam deseti výstrah podle počtu odpovídající kritériím daného sloupce pro zadaný obor a časový rozsah. Hledání v protokolu, které poskytuje celý seznam, můžete spustit kliknutím na Zobrazit vše v dolní části sloupce nebo kliknutím na záhlaví sloupce.

| Column | Popis |
|:--|:--|
| Operations | Poskytuje informace o aktivních uživatelích z monitorovaných předplatných Office 365. Uvidíte také počet aktivit, ke kterým dojde v průběhu času.
| Exchange | Zobrazuje rozpis aktivit systému Exchange Server, jako je například oprávnění k přidání a poštovní schránce, nebo nastavit poštovní schránku. |
| SharePoint | Zobrazuje hlavní aktivity, které uživatelé provádějí v dokumentech služby SharePoint. Když přejdete k podrobnostem na této dlaždici, na stránce hledání se zobrazí podrobnosti o těchto aktivitách, jako je cílový dokument a umístění této aktivity. Například pro událost otevření souboru budete moci zobrazit dokument, ke kterému se přistupovalo, jeho přidružený název účtu a IP adresu. |
| Azure Active Directory | Zahrnuje hlavní aktivity uživatelů, jako je resetování hesla uživatele a pokusů o přihlášení. Když přejdete k podrobnostem, budete moci zobrazit podrobnosti o těchto činnostech, jako je stav výsledku. To je hlavně užitečné, pokud chcete monitorovat podezřelé aktivity v Azure Active Directory. |




## <a name="azure-monitor-log-records"></a>Záznamy protokolu Azure Monitor

Všechny záznamy vytvořené v pracovním prostoru Log Analytics v Azure Monitor řešení Office 365 mají **typ** **OfficeActivity**.  Vlastnost **OfficeWorkload** určuje, kterou službu Office 365 tento záznam odkazuje na Exchange, Azureactivedirectory selhala, SharePoint nebo OneDrive.  Vlastnost **RecordType** určuje typ operace.  Vlastnosti se budou lišit pro každý typ operace a jsou uvedeny v následujících tabulkách.

### <a name="common-properties"></a>Společné vlastnosti

Následující vlastnosti jsou společné pro všechny záznamy sady Office 365.

| Vlastnost | Popis |
|:--- |:--- |
| Typ | *OfficeActivity* |
| IP adresa klienta | IP adresa zařízení, které se použilo při zaznamenání aktivity. IP adresa se zobrazí ve formátu adresy IPv4 nebo IPv6. |
| OfficeWorkload | Služba Office 365, na kterou odkazuje záznam.<br><br>Azureactivedirectory selhala<br>Exchange<br>SharePoint|
| Operace | Název aktivity uživatele nebo správce.  |
| OrganizationId | Identifikátor GUID klienta Office 365 vaší organizace. Tato hodnota bude pro vaši organizaci vždycky stejná, bez ohledu na službu Office 365, ve které se vyskytuje. |
| RecordType | Typ operace byl proveden. |
| ResultStatus | Označuje, zda akce (zadaná ve vlastnosti Operation) byla úspěšná nebo ne. Možné hodnoty jsou úspěšné, pravdivé hodnotě stavem nebo selhaly. V případě aktivity správce serveru Exchange je hodnota buď true, nebo false. |
| userId | Hlavní název uživatele (UPN) uživatele, který provedl akci, která vedla k zaznamenání záznamu; například my_name@my_domain_name. Všimněte si, že jsou zahrnuté také záznamy aktivity prováděné systémovými účty (například SHAREPOINT\system nebo NTAUTHORITY\SYSTEM.). | 
| Vlastnosti UserKey | Alternativní ID pro uživatele identifikovaného ve vlastnosti UserId.  Tato vlastnost se například naplní jedinečným IDENTIFIKÁTORem (PUID) služby Passport pro události prováděné uživateli na SharePointu, OneDrivu pro firmy a Exchange. Tato vlastnost může taky určovat stejnou hodnotu jako vlastnost UserID pro události, ke kterým dochází v jiných službách a událostech, které provádí systémové účty.|
| userType | Typ uživatele, který provedl operaci.<br><br>Správ<br>Aplikace<br>DcAdmin<br>Platné<br>Rezervováno<br>ServicePrincipal<br>Systém |


### <a name="azure-active-directory-base"></a>Základ Azure Active Directory

Následující vlastnosti jsou společné pro všechny záznamy Azure Active Directory.

| Vlastnost | Popis |
|:--- |:--- |
| OfficeWorkload | Azureactivedirectory selhala |
| RecordType     | Azureactivedirectory selhala |
| AzureActiveDirectory_EventType | Typ události služby Azure AD. |
| extendedProperties | Rozšířené vlastnosti události Azure AD. |


### <a name="azure-active-directory-account-logon"></a>Přihlášení Azure Active Directory účtu

Tyto záznamy se vytvoří, když se uživatel služby Active Directory pokusí přihlásit.

| Vlastnost | Popis |
|:--- |:--- |
| `OfficeWorkload` | Azureactivedirectory selhala |
| `RecordType`     | AzureActiveDirectoryAccountLogon |
| `Application` | Aplikace, která aktivuje událost přihlášení k účtu, například Office 15. |
| `Client` | Podrobnosti o klientském zařízení, operačním systému zařízení a prohlížeči zařízení, které se použily pro událost přihlášení k účtu. |
| `LoginStatus` | Tato vlastnost je přímo z OrgIdLogon. ovládací stavu přihlášení. Mapování různých zajímavých chyb přihlášení může být provedeno pomocí algoritmů výstrahy. |
| `UserDomain` | Informace o identitě tenanta (TII). | 


### <a name="azure-active-directory"></a>Azure Active Directory

Tyto záznamy se vytvoří, když se změní nebo doplňují objekty Azure Active Directory.

| Vlastnost | Popis |
|:--- |:--- |
| OfficeWorkload | Azureactivedirectory selhala |
| RecordType     | Azureactivedirectory selhala |
| AADTarget | Uživatel, na kterém se provedla akce (identifikovaná vlastností Operation) |
| Tříd | Uživatel nebo instanční objekt, který tuto akci provedl. |
| ActorContextId | Identifikátor GUID organizace, do které patří objekt actor |
| ActorIpAddress | IP adresa objektu actor ve formátu adresy IPV4 nebo IPV6. |
| InterSystemsId | Identifikátor GUID, který sleduje akce napříč komponentami v rámci služby Office 365. |
| IntraSystemId |   Identifikátor GUID generovaný Azure Active Directory pro sledování akce. |
| SupportTicketId | ID lístku zákaznické podpory pro akci v situacích "jednání na základě". |
| TargetContextId | Identifikátor GUID organizace, do které patří cílový uživatel |


### <a name="data-center-security"></a>Zabezpečení datového centra

Tyto záznamy jsou vytvořeny z dat auditu zabezpečení datového centra.  

| Vlastnost | Popis |
|:--- |:--- |
| EffectiveOrganization | Název tenanta, na kterém byl zvýšení úrovně oprávnění/rutina cílen. |
| ElevationApprovedTime | Časové razítko pro dobu, kdy bylo zvýšení oprávnění schváleno. |
| ElevationApprover | Název Microsoft Manageru |
| ElevationDuration | Doba, po kterou bylo zvýšení oprávnění aktivní. |
| ElevationRequestId |  Jedinečný identifikátor žádosti o zvýšení oprávnění. |
| ElevationRole | Role, pro kterou bylo zvýšení oprávnění vyžádáno. |
| ElevationTime | Čas zahájení zvýšení oprávnění. |
| Start_Time | Čas zahájení provedení rutiny. |


### <a name="exchange-admin"></a>Správce Exchange

Tyto záznamy jsou vytvořeny při změně konfigurace systému Exchange.

| Vlastnost | Popis |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeAdmin |
| ExternalAccess |  Určuje, jestli rutina spustila uživatel ve vaší organizaci, pracovníkovi Datacenter společnosti Microsoft nebo účtem služby Datacenter nebo delegovaným správcem. Hodnota false znamená, že rutina spustila někdo ve vaší organizaci. Hodnota true označuje, že rutina byla spuštěna pracovníky datového centra, účtem služby Datacenter nebo delegovaným správcem. |
| ModifiedObjectResolvedName |  Toto je uživatelsky přívětivý název objektu, který byl změněn rutinou. Toto je zaznamenáno pouze v případě, že rutina mění objekt. |
| Organizace | Název tenanta. |
| OriginatingServer | Název serveru, ze kterého byla rutina spuštěna. |
| Parametry | Název a hodnota pro všechny parametry, které byly použity s rutinou, která je identifikována vlastností Operations. |


### <a name="exchange-mailbox"></a>Poštovní schránka Exchange

Tyto záznamy jsou vytvořeny při změnách nebo přidávání do poštovních schránek systému Exchange.

| Vlastnost | Popis |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| ClientInfoString | Informace o e-mailovém klientovi, který se použil k provedení operace, jako je například verze prohlížeče, verze aplikace Outlook a informace o mobilních zařízeních. |
| Client_IPAddress | IP adresa zařízení, které se použilo při zaznamenání operace. IP adresa se zobrazí ve formátu adresy IPv4 nebo IPv6. |
| ClientMachineName | Název počítače, který je hostitelem klienta aplikace Outlook. |
| ClientProcessName | E-mailový klient, který se použil pro přístup k poštovní schránce. |
| ClientVersion | Verze e-mailového klienta |
| InternalLogonType | Vyhrazeno pro interní použití. |
| Logon_Type | Určuje typ uživatele, který použil poštovní schránku, a provedl zaprotokolovaná operace. |
| LogonUserDisplayName |    Uživatelsky přívětivé jméno uživatele, který operaci provedl. |
| LogonUserSid | Identifikátor SID uživatele, který operaci provedl. |
| MailboxGuid | Identifikátor GUID serveru Exchange poštovní schránky, ke které došlo |
| MailboxOwnerMasterAccountSid | Identifikátor SID hlavního účtu účtu vlastníka poštovní schránky. |
| MailboxOwnerSid | Identifikátor SID vlastníka poštovní schránky |
| MailboxOwnerUPN | E-mailová adresa osoby, která vlastní poštovní schránku, která byla k dispozici. |


### <a name="exchange-mailbox-audit"></a>Audit poštovní schránky serveru Exchange

Tyto záznamy se vytvoří při vytvoření položky auditu pro poštovní schránku.

| Vlastnost | Popis |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| Položka | Představuje položku, na které byla operace provedena. | 
| SendAsUserMailboxGuid | Identifikátor GUID serveru Exchange poštovní schránky, na kterou se přistupoval e-mail pro odeslání e-mailu. |
| SendAsUserSmtp | Adresa SMTP uživatele, který je zosobněn. |
| SendonBehalfOfUserMailboxGuid | Identifikátor GUID serveru Exchange poštovní schránky, ke které se připojila pošta jménem. |
| SendOnBehalfOfUserSmtp | Adresa SMTP uživatele, jehož jménem se e-mail pošle. |


### <a name="exchange-mailbox-audit-group"></a>Skupina auditu pro poštovní schránku systému Exchange

Tyto záznamy se vytvoří, když se změní nebo doplňují skupiny Exchange.

| Vlastnost | Popis |
|:--- |:--- |
| OfficeWorkload | Exchange |
| OfficeWorkload | ExchangeItemGroup |
| AffectedItems | Informace o jednotlivých položkách ve skupině. |
| CrossMailboxOperations | Určuje, zda operace zahrnovala více než jednu poštovní schránku. |
| DestMailboxId | Nastavte pouze v případě, že má parametr CrossMailboxOperations hodnotu true. Určuje cílový identifikátor GUID poštovní schránky. |
| DestMailboxOwnerMasterAccountSid | Nastavte pouze v případě, že má parametr CrossMailboxOperations hodnotu true. Určuje SID hlavního účtu pro vlastníka cílové poštovní schránky. |
| DestMailboxOwnerSid | Nastavte pouze v případě, že má parametr CrossMailboxOperations hodnotu true. Určuje identifikátor SID cílové poštovní schránky. |
| DestMailboxOwnerUPN | Nastavte pouze v případě, že má parametr CrossMailboxOperations hodnotu true. Určuje hlavní název uživatele (UPN) vlastníka cílové poštovní schránky. |
| DestFolder | Cílová složka pro operace, jako je například Move. |
| Složka | Složka, ve které se nachází skupina položek. |
| Složky |     Informace o zdrojových složkách, které se podílejí na operaci; Například pokud jsou vybrané složky a pak se odstraní. |


### <a name="sharepoint-base"></a>Základ služby SharePoint

Tyto vlastnosti jsou společné pro všechny záznamy SharePointu.

| Vlastnost | Popis |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| EventSource | Označuje, že došlo k události v SharePointu. Možné hodnoty jsou SharePoint nebo ObjectModel. |
| ItemType | Typ objektu, který byl použit nebo změněn. Podrobnosti o typech objektů naleznete v tabulce ItemType. |
| MachineDomainInfo | Informace o operacích synchronizace zařízení. Tyto informace jsou hlášeny pouze v případě, že jsou v žádosti přítomné. |
| MachineId |   Informace o operacích synchronizace zařízení. Tyto informace jsou hlášeny pouze v případě, že jsou v žádosti přítomné. |
| Webovém | Identifikátor GUID lokality, ve které je umístěn soubor nebo složka, ke které má uživatel přistup. |
| Source_Name | Entita, která aktivovala auditovaná operace. Možné hodnoty jsou SharePoint nebo ObjectModel. |
| UserAgent | Informace o klientském nebo prohlížeči uživatele. Tyto informace poskytuje klient nebo prohlížeč. |


### <a name="sharepoint-schema"></a>Schéma služby SharePoint

Tyto záznamy jsou vytvořeny při provedení změn konfigurace služby SharePoint.

| Vlastnost | Popis |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| CustomEvent | Volitelný řetězec pro vlastní události. |
| Event_Data |  Volitelná datová část pro vlastní události |
| ModifiedProperties | Tato vlastnost je obsažena pro události správce, jako je například přidání uživatele jako člena webu nebo skupiny pro správu kolekce webů. Vlastnost obsahuje název vlastnosti, která byla upravena (například skupina správců webu), novou hodnotu upravené vlastnosti (například uživatel, který byl přidán jako správce lokality), a předchozí hodnotu upraveného objektu. |


### <a name="sharepoint-file-operations"></a>Operace se soubory SharePointu

Tyto záznamy jsou vytvořeny v reakci na operace se soubory ve službě SharePoint.

| Vlastnost | Popis |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePointFileOperation |
| DestinationFileExtension | Přípona souboru, který je zkopírovaný nebo přesunutý. Tato vlastnost se zobrazuje pouze pro zkopírované a přesunuté události. |
| DestinationFileName | Název souboru, který se zkopíruje nebo přesune. Tato vlastnost se zobrazuje pouze pro zkopírované a přesunuté události. |
| DestinationRelativeUrl | Adresa URL cílové složky, ve které je soubor zkopírován nebo přesunut. Kombinace hodnot parametrů SiteURL, DestinationRelativeURL a DestinationFileName je stejná jako hodnota vlastnosti ObjectID, což je úplný název cesty pro kopírovaný soubor. Tato vlastnost se zobrazuje pouze pro zkopírované a přesunuté události. |
| SharingType | Typ oprávnění ke sdílení, která byla přiřazena uživateli, se kterým byl prostředek sdílen. Tento uživatel je identifikovaný parametrem UserSharedWith. |
| Site_Url | Adresa URL webu, kde se nachází soubor nebo složka, ke kterým má uživatel přistup. |
| SourceFileExtension | Přípona souboru, ke kterému měl uživatel přistup. Tato vlastnost je prázdná, pokud je objekt, ke kterému byl přistup, složka. |
| SourceFileName |  Název souboru nebo složky, ke kterým uživatel přistupoval. |
| SourceRelativeUrl | Adresa URL složky, která obsahuje soubor, ke kterému uživatel přistupoval. Kombinace hodnot parametrů SiteURL, SourceRelativeURL a SourceFileName je stejná jako hodnota vlastnosti ObjectID, což je úplný název cesty k souboru, ke kterému přistupoval uživatel. |
| UserSharedWith |  Uživatel, se kterým se prostředek sdílel. |




## <a name="sample-log-searches"></a>Ukázky hledání v protokolech

V následující tabulce jsou uvedeny ukázky hledání v protokolech pro záznamy aktualizace shromážděné tímto řešením.

| Dotaz | Popis |
| --- | --- |
|Počet všech operací v předplatném sady Office 365 |OfficeActivity &#124; sumarizace Count () podle operace |
|Použití webů SharePointu|OfficeActivity &#124; WHERE OfficeWorkload = ~ "SharePoint" &#124; sumarizace Count () podle SiteUrl \| řazení podle počtu ASC|
|Operace přístupu k souboru podle typu uživatele|Hledat v (OfficeActivity) OfficeWorkload = ~ "azureactivedirectory selhala" a "MyTest"|
|Hledat pomocí konkrétního klíčového slova|Type = OfficeActivity OfficeWorkload = azureactivedirectory selhala "MyTest"|
|Monitorování externích akcí na Exchangi|OfficeActivity &#124; WHERE OfficeWorkload = ~ "Exchange" and ExternalAccess = = true|



## <a name="next-steps"></a>Další kroky

* K zobrazení podrobných dat aktualizace použijte [dotazy protokolu v Azure monitor](../log-query/log-query-overview.md) .
* [Vytvořte si vlastní řídicí panely](../learn/tutorial-logs-dashboards.md) pro zobrazení oblíbených vyhledávacích dotazů sady Office 365.
* [Vytvářejte výstrahy](../platform/alerts-overview.md) , které se proaktivně informují o důležitých aktivitách Office 365.  
