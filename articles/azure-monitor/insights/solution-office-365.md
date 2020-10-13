---
title: Řešení pro správu Office 365 v Azure
description: Tento článek poskytuje podrobné informace o konfiguraci a použití řešení Office 365 v Azure.  Obsahuje podrobný popis záznamů Office 365 vytvořených v Azure Monitor.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/30/2020
ms.openlocfilehash: eb20bf4164cb2153f6786dbec04f79453554fa25
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91999746"
---
# <a name="office-365-management-solution-in-azure-preview"></a>Řešení pro správu Office 365 v Azure (Preview)

![Logo Office 365](media/solution-office-365/icon.png)

> [!IMPORTANT]
> ## <a name="solution-update"></a>Aktualizace řešení
> Toto řešení bylo nahrazeno řešením obecné dostupnosti [Office 365](../../sentinel/connect-office-365.md) ve [službě Azure Sentinel](../../sentinel/overview.md) a v [řešení pro vytváření sestav a monitorování Azure AD](../../active-directory/reports-monitoring/plan-monitoring-and-reporting.md). Společně poskytují aktualizovanou verzi předchozího řešení Azure Monitor Office 365 s vylepšeným prostředím pro konfiguraci. Stávající řešení můžete dál používat do 31. října 2020.
> 
> Azure Sentinel je nativní cloudové informace o zabezpečení a řešení pro správu událostí, které ingestují protokoly a poskytuje další funkce SIEM, včetně detekce, šetření, loveckí a řízené přehledy na základě strojového učení. Použití služby Azure Sentinel vám teď poskytne příjem aktivit Office 365 SharePoint a protokolů správy Exchange.
> 
> Vytváření sestav Azure AD poskytuje komplexnější přehled o protokolech z aktivity Azure AD ve vašem prostředí, včetně událostí přihlášení, událostí auditu a změn v adresáři. Pokud chcete připojit protokoly služby Azure AD, můžete použít buď [konektor Azure Sentinel Azure AD](../../sentinel/connect-azure-active-directory.md) , nebo nakonfigurovat [integraci protokolů služby azure AD pomocí Azure monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md). 
>
> Shromažďování protokolu Azure AD se řídí cenami Azure Monitor.  Další informace najdete v tématu [Azure monitor ceny](https://azure.microsoft.com/pricing/details/monitor/) .
>
> Použití řešení Azure Sentinel Office 365:
> 1. Používání konektoru Office 365 ve službě Azure Sentinel má vliv na ceny pro váš pracovní prostor. Další informace najdete v tématu [ceny služby Azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/).
> 2. Pokud už používáte řešení Azure Monitor Office 365, musíte ho nejdřív odinstalovat pomocí skriptu v [níže uvedené části věnované odinstalaci](#uninstall).
> 3. Povolte v pracovním prostoru [řešení Sentinel Azure](../../sentinel/quickstart-onboard.md) .
> 4. Přejít na stránku **datových konektorů** v Azure Sentinel a povolit konektor **Office 365** .
>
> ## <a name="frequently-asked-questions"></a>Nejčastější dotazy
> 
> ### <a name="q-is-it-possible-to-on-board-the-office-365-azure-monitor-solution-between-now-and-october-31"></a>Otázka: je možné začlenit řešení Office 365 Azure Monitor mezi dnes a 31. října?
> Ne, skripty pro připojování řešení Azure Monitor Office 365 už nejsou k dispozici. Řešení bude odstraněno 31. října.
> 
> ### <a name="q-will-the-tables-and-schemas-be-changed"></a>Otázka: budou se tabulky a schémata měnit?
> Název a schéma **OfficeActivity** tabulky zůstane stejné jako v aktuálním řešení. V novém řešení můžete pokračovat v používání stejných dotazů, kromě dotazů, které odkazují na data Azure AD.
> 
> Nové protokoly [řešení pro vytváření sestav a monitorování služby Azure AD](../../active-directory/reports-monitoring/plan-monitoring-and-reporting.md) budou ingestovat do tabulek [SigninLogs](../../active-directory/reports-monitoring/concept-sign-ins.md) a [AuditLogs](../../active-directory/reports-monitoring/concept-audit-logs.md) namísto **OfficeActivity**. Další informace najdete v tématu [Analýza protokolů služby Azure AD](../../active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics.md), které jsou relevantní taky pro uživatele Sentinel a Azure monitor uživatelů Azure.
> 
> Následují ukázky pro převod dotazů z **OfficeActivity** na **SigninLogs**:
> 
> **Dotaz na neúspěšné přihlášení podle uživatele:**
> 
> ```Kusto
> OfficeActivity
> | where TimeGenerated >= ago(1d) 
> | where OfficeWorkload == "AzureActiveDirectory"                      
> | where Operation == 'UserLoginFailed'
> | summarize count() by UserId    
> ```
> 
> ```Kusto
> SigninLogs
> | where ConditionalAccessStatus == "failure" or ConditionalAccessStatus == "notApplied"
> | summarize count() by UserDisplayName
> ```
> 
> **Zobrazit operace Azure AD:**
> 
> ```Kusto
> OfficeActivity
> | where OfficeWorkload =~ "AzureActiveDirectory"
> | sort by TimeGenerated desc
> | summarize AggregatedValue = count() by Operation
> ```
> 
> ```Kusto
> AuditLogs
> | summarize count() by OperationName
> ```
> 
> ### <a name="q-how-can-i-on-board-azure-sentinel"></a>Otázka: Jak můžu zapnout službu Azure Sentinel na kartě?
> Azure Sentinel je řešení, které můžete povolit pro nový nebo existující Log Analytics pracovní prostor. Další informace najdete [v dokumentaci ke službě Azure Sentinel on-Desking](../../sentinel/quickstart-onboard.md).
>
> ### <a name="q-do-i-need-azure-sentinel-to-connect-the-azure-ad-logs"></a>Otázka: Potřebuji, abyste připojili protokoly Azure AD ke službě Azure Sentinel?
> [Integraci protokolů služby Azure AD můžete nakonfigurovat pomocí Azure monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md), která nesouvisí s řešením Sentinel Azure. Služba Azure Sentinel poskytuje nativní konektor a předem připravený obsah pro protokoly služby Azure AD. Další informace najdete v následující otázce na předem připraveném obsahu orientovaném na zabezpečení.
>
> ###    <a name="q-what-are-the-differences-when-connecting-azure-ad-logs-from-azure-sentinel-and-azure-monitor"></a>Otázka: Jaké jsou rozdíly při připojování protokolů služby Azure AD z Azure Sentinel a Azure Monitor?
> Azure Sentinel a Azure Monitor se připojují k protokolům Azure AD na základě stejného [řešení pro vytváření sestav a monitorování Azure AD](../../active-directory/reports-monitoring/plan-monitoring-and-reporting.md). Azure Sentinel poskytuje nativní konektor, který spojuje stejná data a poskytuje informace o monitorování.
>
> ###    <a name="q-what-do-i-need-to-change-when-moving-to-the-new-azure-ad-reporting-and-monitoring-tables"></a>Otázka: co musím změnit při přesunu do nových tabulek pro vytváření sestav a monitorování Azure AD?
> Všechny dotazy, které používají data Azure AD, včetně dotazů v upozorněních, řídicích panelech a libovolného obsahu, který jste vytvořili pomocí Azure AD data v Office 365, se musí znovu vytvořit pomocí nových tabulek.
>
> Služba Azure Sentinel a Azure AD poskytují integrovaný obsah, který můžete použít při přechodu do řešení Azure AD Reporting a monitoring. Další informace najdete v další otázce na předem připraveném obsahu orientovaném na zabezpečení a [o tom, jak používat Azure monitor sešity pro Azure Active Directory sestavy](../../active-directory/reports-monitoring/howto-use-azure-monitor-workbooks.md). 
>
> ### <a name="q-how-i-can-use-the-azure-sentinel-out-of-the-box-security-oriented-content"></a>Otázka: Jak můžu použít okamžitý obsah orientovaný na zabezpečení Azure Sentinel?
> Služba Azure Sentinel poskytuje předem připravené řídicí panely zaměřené na zabezpečení, vlastní dotazy na výstrahy, lovecké dotazy, šetření a možnosti automatizované reakce založené na protokolech Office 365 a Azure AD. Další informace najdete v kurzech k Azure Sentinel a kurzům:
>
> - [Vyhledá hrozby předem.](../../sentinel/tutorial-detect-threats-built-in.md)
> - [Vytváření vlastních analytických pravidel pro detekci podezřelých hrozeb](../../sentinel/tutorial-detect-threats-custom.md)
> - [Monitorování dat](../../sentinel/tutorial-monitor-your-data.md)
> - [Prozkoumat incidenty pomocí služby Azure Sentinel](../../sentinel/tutorial-investigate-cases.md)
> - [Nastavení automatických odpovědí na hrozby v Azure Sentinel](../../sentinel/tutorial-respond-threats-playbook.md)
> - [Komunita GitHubu Azure Sentinel](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks)
> 
> ### <a name="q-does-azure-sentinel-provide-additional-connectors-as-part-of-the-solution"></a>Otázka: umožňuje službě Azure Sentinel v rámci řešení přidat další konektory?
> Ano, další informace najdete v tématu [zdroje dat služby Azure Sentinel Connect](../../sentinel/connect-data-sources.md).
> 
> ###    <a name="q-what-will-happen-on-october-31-do-i-need-to-offboard-beforehand"></a>Otázka: co se stane 31. října Musím odpojení předem?
> 
> - Nebudete moct přijímat data z řešení **Office 365** . Řešení se odebere z vašeho pracovního prostoru a na webu Marketplace už nebude dostupné.
> - Pro zákazníky s Sentinelem v Azure se v řešení **Office 365** pro **SecurityInsights** pro Azure sentinel bude zahrnout řešení Log Analytics pracovního prostoru.
> - Pokud vaše řešení neodpojení ručně do 31. října, vaše data se odpojí automaticky a tabulka **OfficeActivity** se odebere. I tak budete moct tabulku obnovit, když povolíte konektor Office 365 ve službě Azure Sentinel, jak je vysvětleno níže.
> 
> ### <a name="q-will-my-data-transfer-to-the-new-solution"></a>Otázka: budou moje data přenesena do nového řešení?
> Yes. Když odeberete řešení **Office 365** z pracovního prostoru, jeho data budou dočasně nedostupná, protože schéma se odebere. Pokud povolíte nový konektor **sady Office 365** v nástroji Sentinel, obnoví se schéma do pracovního prostoru a veškerá shromážděná data budou k dispozici. 
 

Řešení pro správu sady Office 365 umožňuje monitorovat prostředí sady Office 365 v Azure Monitor.

- Monitorujte aktivity uživatelů na účtech Office 365, abyste mohli analyzovat vzorce používání a identifikovat trendy chování. Můžete například extrahovat konkrétní scénáře použití, například soubory, které jsou sdíleny mimo vaši organizaci nebo nejoblíbenější weby služby SharePoint.
- Monitorujte aktivity správce a sledujte změny konfigurace nebo operace s vysokými oprávněními.
- Umožňuje detekovat a prozkoumat nežádoucí chování uživatelů, které je možné přizpůsobit potřebám vaší organizace.
- Předvedení auditu a dodržování předpisů. Například můžete monitorovat operace přístupu k souborům u důvěrných souborů, které vám pomůžou s procesem auditu a dodržování předpisů.
- Řešení potíží s operačním systémem pomocí [dotazů protokolu](../log-query/log-query-overview.md) na data o aktivitách vaší organizace v Office 365.


## <a name="uninstall"></a>Odinstalovat

Řešení pro správu Office 365 můžete odebrat pomocí procesu v části [Odebrání řešení pro správu](solutions.md#remove-a-monitoring-solution). Tím se nezastaví shromažďování dat ze sady Office 365 do Azure Monitor i když. Pomocí níže uvedeného postupu můžete zrušit odběr Office 365 a zastavit shromažďování dat.

1. Následující skript uložte jako *office365_unsubscribe.ps1*.

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
    $platformParameters = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.PlatformParameters" -ArgumentList "Auto"
    $global:authResultARM = $authContext.AcquireTokenAsync($resourceAppIdURIARM, $clientId, $redirectUri, $platformParameters)
    $global:authResultARM.Wait()
    $authHeader = $global:authResultARM.Result.CreateAuthorizationHeader()
    $authHeader
    }
    
    Function Office-UnSubscribe-Call{
    
    #----------------------------------------------------------------------------------------------------------------------------------------------
    $authHeader = $global:authResultARM.Result.CreateAuthorizationHeader()
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

Zobrazí se výzva k zadání přihlašovacích údajů. Zadejte přihlašovací údaje pro váš pracovní prostor Log Analytics.

## <a name="data-collection"></a>Shromažďování dat

Shromažďování dat na začátku může trvat několik hodin. Po zahájení shromažďování se sada Office 365 pošle [oznámení Webhooku](/office/office-365-management-api/office-365-management-activity-api-reference#receiving-notifications) s podrobnými daty, která se Azure monitor pokaždé, když se vytvoří záznam. Tento záznam je k dispozici v Azure Monitor během několika minut po přijetí.

## <a name="using-the-solution"></a>Použití řešení

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Když do svého pracovního prostoru Log Analytics přidáte řešení Office 365, na řídicí panel se přidá dlaždice **Office 365** . Na této dlaždici se zobrazuje počet a grafické vyjádření počtu počítačů ve vašem prostředí a jejich kompatibilita s aktualizacemi.<br><br>
![Dlaždice souhrnu Office 365](media/solution-office-365/tile.png)  

Kliknutím na dlaždici **office 365** otevřete řídicí panel **Office 365** .

![Řídicí panel Office 365](media/solution-office-365/dashboard.png)  

Řídicí panel obsahuje sloupce v následující tabulce. V každém sloupci je uveden seznam deseti výstrah podle počtu odpovídající kritériím daného sloupce pro zadaný obor a časový rozsah. Hledání v protokolu, které poskytuje celý seznam, můžete spustit kliknutím na Zobrazit vše v dolní části sloupce nebo kliknutím na záhlaví sloupce.

| Sloupec | Popis |
|:--|:--|
| Operace | Poskytuje informace o aktivních uživatelích z monitorovaných předplatných Office 365. Uvidíte také počet aktivit, ke kterým dojde v průběhu času.
| Výměna | Zobrazuje rozpis aktivit serveru Exchange, například oprávnění Add-Mailbox nebo nastavení poštovní schránky. |
| SharePoint | Zobrazuje hlavní aktivity, které uživatelé provádějí v dokumentech služby SharePoint. Když přejdete k podrobnostem na této dlaždici, na stránce hledání se zobrazí podrobnosti o těchto aktivitách, jako je cílový dokument a umístění této aktivity. Například pro událost otevření souboru budete moci zobrazit dokument, ke kterému se přistupovalo, jeho přidružený název účtu a IP adresu. |
| Azure Active Directory | Zahrnuje hlavní aktivity uživatelů, jako je resetování hesla uživatele a pokusů o přihlášení. Když přejdete k podrobnostem, budete moci zobrazit podrobnosti o těchto činnostech, jako je stav výsledku. To je hlavně užitečné, pokud chcete monitorovat podezřelé aktivity v Azure Active Directory. |




## <a name="azure-monitor-log-records"></a>Záznamy protokolu Azure Monitor

Všechny záznamy vytvořené v pracovním prostoru Log Analytics v Azure Monitor řešení Office 365 mají **typ** **OfficeActivity**.  Vlastnost **OfficeWorkload** určuje, kterou službu Office 365 tento záznam odkazuje na Exchange, Azureactivedirectory selhala, SharePoint nebo OneDrive.  Vlastnost **RecordType** určuje typ operace.  Vlastnosti se budou lišit pro každý typ operace a jsou uvedeny v následujících tabulkách.

### <a name="common-properties"></a>Společné vlastnosti

Následující vlastnosti jsou společné pro všechny záznamy sady Office 365.

| Vlastnost | Popis |
|:--- |:--- |
| Typ | *OfficeActivity* |
| IP adresa klienta | IP adresa zařízení použitá během protokolování aktivity. IP adresa se zobrazí ve formátu adresy IPv4 nebo IPv6. |
| OfficeWorkload | Služba Office 365, na kterou odkazuje záznam.<br><br>Azureactivedirectory selhala<br>Výměna<br>SharePoint|
| Operace | Název aktivity uživatele nebo správce.  |
| OrganizationId | Identifikátor GUID klienta Office 365 vaší organizace. Tato hodnota bude pro vaši organizaci vždycky stejná, bez ohledu na službu Office 365, ve které se vyskytuje. |
| RecordType | Typ operace byl proveden. |
| ResultStatus | Určuje, jestli byla akce (zadaná ve vlastnosti Operation) úspěšná, nebo ne. Možné hodnoty jsou úspěšné, pravdivé hodnotě stavem nebo selhaly. V případě aktivity správce serveru Exchange je hodnota buď true, nebo false. |
| UserId | Hlavní název uživatele (UPN) uživatele, který provedl akci, která vedla k zaznamenání záznamu; například my_name@my_domain_name . Všimněte si, že jsou zahrnuté také záznamy aktivity prováděné systémovými účty (například SHAREPOINT\system nebo NTAUTHORITY\SYSTEM.). | 
| UserKey | Alternativní ID pro uživatele identifikovaného ve vlastnosti UserId.  Tato vlastnost se například naplní jedinečným IDENTIFIKÁTORem (PUID) služby Passport pro události prováděné uživateli na SharePointu, OneDrivu pro firmy a Exchange. Tato vlastnost může taky určovat stejnou hodnotu jako vlastnost UserID pro události, ke kterým dochází v jiných službách a událostech, které provádí systémové účty.|
| UserType | Typ uživatele, který provedl operaci.<br><br>Správce<br>Aplikace<br>DcAdmin<br>Pravidelný<br>Vyhrazené<br>ServicePrincipal<br>Systém |


### <a name="azure-active-directory-base"></a>Základ Azure Active Directory

Následující vlastnosti jsou společné pro všechny záznamy Azure Active Directory.

| Vlastnost | Popis |
|:--- |:--- |
| OfficeWorkload | Azureactivedirectory selhala |
| RecordType     | Azureactivedirectory selhala |
| AzureActiveDirectory_EventType | Typ události služby Azure AD. |
| ExtendedProperties | Rozšířené vlastnosti události Azure AD. |


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
| Actor (Herec/herečka) | Uživatel nebo instanční objekt, který tuto akci provedl. |
| ActorContextId | Identifikátor GUID organizace, do které patří objekt actor |
| ActorIpAddress | IP adresa objektu actor ve formátu adresy IPV4 nebo IPV6. |
| InterSystemsId | Identifikátor GUID, který sleduje akce napříč komponentami v rámci služby Office 365. |
| IntraSystemId |     Identifikátor GUID generovaný Azure Active Directory pro sledování akce. |
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
| ElevationRequestId |     Jedinečný identifikátor žádosti o zvýšení oprávnění. |
| ElevationRole | Role, pro kterou bylo zvýšení oprávnění vyžádáno. |
| ElevationTime | Čas zahájení zvýšení oprávnění. |
| Start_Time | Čas zahájení provedení rutiny. |


### <a name="exchange-admin"></a>Správce Exchange

Tyto záznamy jsou vytvořeny při změně konfigurace systému Exchange.

| Vlastnost | Popis |
|:--- |:--- |
| OfficeWorkload | Výměna |
| RecordType     | ExchangeAdmin |
| ExternalAccess |     Určuje, jestli rutina spustila uživatel ve vaší organizaci, pracovníkovi Datacenter společnosti Microsoft nebo účtem služby Datacenter nebo delegovaným správcem. Hodnota false znamená, že rutina spustila někdo ve vaší organizaci. Hodnota true označuje, že rutina byla spuštěna pracovníky datového centra, účtem služby Datacenter nebo delegovaným správcem. |
| ModifiedObjectResolvedName |     Toto je uživatelsky přívětivý název objektu, který byl změněn rutinou. Toto je zaznamenáno pouze v případě, že rutina mění objekt. |
| OrganizationName | Název tenanta. |
| OriginatingServer | Název serveru, ze kterého byla rutina spuštěna. |
| Parametry | Název a hodnota pro všechny parametry, které byly použity s rutinou, která je identifikována vlastností Operations. |


### <a name="exchange-mailbox"></a>Poštovní schránka Exchange

Tyto záznamy jsou vytvořeny při změnách nebo přidávání do poštovních schránek systému Exchange.

| Vlastnost | Popis |
|:--- |:--- |
| OfficeWorkload | Výměna |
| RecordType     | ExchangeItem |
| ClientInfoString | Informace o e-mailovém klientovi, který se použil k provedení operace, jako je například verze prohlížeče, verze aplikace Outlook a informace o mobilních zařízeních. |
| Client_IPAddress | IP adresa zařízení, které se použilo při zaznamenání operace. IP adresa se zobrazí ve formátu adresy IPv4 nebo IPv6. |
| ClientMachineName | Název počítače, který je hostitelem klienta aplikace Outlook. |
| ClientProcessName | E-mailový klient, který se použil pro přístup k poštovní schránce. |
| ClientVersion | Verze e-mailového klienta |
| InternalLogonType | Vyhrazeno pro interní použití. |
| Logon_Type | Určuje typ uživatele, který použil poštovní schránku, a provedl zaprotokolovaná operace. |
| LogonUserDisplayName |     Uživatelsky přívětivé jméno uživatele, který operaci provedl. |
| LogonUserSid | Identifikátor SID uživatele, který operaci provedl. |
| MailboxGuid | Identifikátor GUID serveru Exchange poštovní schránky, ke které došlo |
| MailboxOwnerMasterAccountSid | Identifikátor SID hlavního účtu účtu vlastníka poštovní schránky. |
| MailboxOwnerSid | Identifikátor SID vlastníka poštovní schránky |
| MailboxOwnerUPN | E-mailová adresa osoby, která vlastní poštovní schránku, která byla k dispozici. |


### <a name="exchange-mailbox-audit"></a>Audit poštovní schránky serveru Exchange

Tyto záznamy se vytvoří při vytvoření položky auditu pro poštovní schránku.

| Vlastnost | Popis |
|:--- |:--- |
| OfficeWorkload | Výměna |
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
| OfficeWorkload | Výměna |
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
| MachineId |     Informace o operacích synchronizace zařízení. Tyto informace jsou hlášeny pouze v případě, že jsou v žádosti přítomné. |
| Site_ | Identifikátor GUID lokality, ve které je umístěn soubor nebo složka, ke které má uživatel přistup. |
| Source_Name | Entita, která aktivovala auditovaná operace. Možné hodnoty jsou SharePoint nebo ObjectModel. |
| UserAgent | Informace o klientském nebo prohlížeči uživatele. Tyto informace poskytuje klient nebo prohlížeč. |


### <a name="sharepoint-schema"></a>Schéma služby SharePoint

Tyto záznamy jsou vytvořeny při provedení změn konfigurace služby SharePoint.

| Vlastnost | Popis |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| CustomEvent | Volitelný řetězec pro vlastní události. |
| Event_Data |     Volitelná datová část pro vlastní události |
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
| SourceFileName |     Název souboru nebo složky, ke kterým uživatel přistupoval. |
| SourceRelativeUrl | Adresa URL složky, která obsahuje soubor, ke kterému uživatel přistupoval. Kombinace hodnot parametrů SiteURL, SourceRelativeURL a SourceFileName je stejná jako hodnota vlastnosti ObjectID, což je úplný název cesty k souboru, ke kterému přistupoval uživatel. |
| UserSharedWith |     Uživatel, se kterým se prostředek sdílel. |




## <a name="sample-log-queries"></a>Ukázky dotazů protokolu

Následující tabulka uvádí Ukázky dotazů protokolu pro záznamy aktualizací shromážděné tímto řešením.

| Dotaz | Description |
| --- | --- |
|Počet všech operací v předplatném sady Office 365 |OfficeActivity &#124; souhrnu počtu () podle operace |
|Použití webů SharePointu|OfficeActivity &#124; WHERE OfficeWorkload = ~ "SharePoint" &#124; Shrnutí Count () pomocí řazení SiteUrl \| podle Count ASC|
|Operace přístupu k souboru podle typu uživatele | OfficeActivity &#124; sumarizace Count () podle UserType |
|Monitorování externích akcí na Exchangi|OfficeActivity &#124; WHERE OfficeWorkload = ~ "Exchange" a ExternalAccess = = true|



## <a name="next-steps"></a>Další kroky

* K zobrazení podrobných dat aktualizace použijte [dotazy protokolu v Azure monitor](../log-query/log-query-overview.md) .
* [Vytvořte si vlastní řídicí panely](../learn/tutorial-logs-dashboards.md) pro zobrazení oblíbených vyhledávacích dotazů sady Office 365.
* [Vytvářejte výstrahy](../platform/alerts-overview.md) , které se proaktivně informují o důležitých aktivitách Office 365.  
