---
title: Řešení pro správu Office 365 v Azure | Dokumenty společnosti Microsoft
description: Tento článek obsahuje podrobnosti o konfiguraci a použití řešení Office 365 v Azure.  Obsahuje podrobný popis záznamů Office 365 vytvořených v Azure Monitoru.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/30/2020
ms.openlocfilehash: 2c6eb5407ec62b6e9e771ce257b66fca2a91e0a5
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "82023601"
---
# <a name="office-365-management-solution-in-azure-preview"></a>Řešení pro správu Office 365 v Azure (Preview)

![Logo Office 365](media/solution-office-365/icon.png)

> [!IMPORTANT]
> ## <a name="solution-update"></a>Aktualizace řešení
> Toto řešení bylo nahrazeno řešením obecné dostupnosti [Office 365](../../sentinel/connect-office-365.md) v [Azure Sentinelu](../../sentinel/overview.md) a [řešením azure ad pro vytváření sestav a monitorování](../../active-directory/reports-monitoring/plan-monitoring-and-reporting.md). Společně poskytují aktualizovanou verzi předchozího řešení Azure Monitor Office 365 s vylepšenou konfigurací. Stávající řešení můžete používat až do 30.
> 
> Azure Sentinel je cloudové nativní řešení pro správu informací o zabezpečení a událostí, které přijímá protokoly a poskytuje další funkce SIEM, včetně detekce, vyšetřování, vyhledávání a přehledů založených na strojovém učení. Použití Azure Sentinelu vám teď poskytne ingestování sharepointových aktivit Office 365 a protokolů pro správu Exchange.
> 
> Sestavy Azure AD poskytuje komplexnější zobrazení protokolů z aktivity Azure AD ve vašem prostředí, včetně událostí přihlášení, události auditu a změny vašeho adresáře. K připojení protokolů Azure AD můžete použít konektor [Azure Azure AD](../../sentinel/connect-azure-active-directory.md) nebo nakonfigurovat [integraci protokolů Azure AD s Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md). 
>
> Kolekce protokolu Azure AD podléhá ceny Azure Monitor.  Další informace najdete v [tématu Ceny Azure Monitoru.](https://azure.microsoft.com/pricing/details/monitor/)
>
> Použití řešení Azure Sentinel Office 365:
> 1. Použití konektoru Office 365 v Azure Sentinelu ovlivňuje ceny pro váš pracovní prostor. Další informace najdete v tématu [Azure Sentinel ceny](https://azure.microsoft.com/pricing/details/azure-sentinel/).
> 2. Pokud už používáte řešení Azure Monitor Office 365, musíte ho nejprve odinstalovat pomocí skriptu v [části Odinstalovat níže](#uninstall).
> 3. [Povolte řešení Azure Sentinel](../../sentinel/quickstart-onboard.md) u svého pracovního prostoru.
> 4. Přejděte na stránku **Datové konektory** v Azure Sentinelu a povolte konektor **Office 365.**
>
> ## <a name="frequently-asked-questions"></a>Nejčastější dotazy
> 
> ### <a name="q-is-it-possible-to-on-board-the-office-365-azure-monitor-solution-between-now-and-july-30th"></a>Otázka: Je možné navštovit řešení Office 365 Azure Monitor od teď do 30.července?
> Ne, skripty registrace řešení Azure Monitor Office 365 už nejsou dostupné. Řešení bude odstraněno 30.
> 
> ### <a name="q-will-the-tables-and-schemas-be-changed"></a>Otázka: Změní se tabulky a schémata?
> Název tabulky **OfficeActivity** a schéma zůstanou stejné jako v aktuálním řešení. Můžete pokračovat v používání stejných dotazů v novém řešení s výjimkou dotazů, které odkazují na data Azure AD.
> 
> Nové protokoly [řešení azure a dinga a monitorování řešení](../../active-directory/reports-monitoring/plan-monitoring-and-reporting.md) budou ingestovány do [tabulek SigninLogs](../../active-directory/reports-monitoring/concept-sign-ins.md) a [AuditLogs](../../active-directory/reports-monitoring/concept-audit-logs.md) namísto **OfficeActivity**. Další informace najdete v [tématu, jak analyzovat protokoly Azure AD](../../active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics.md), což je také relevantní pro uživatele Azure Sentinel a Azure Monitor.
> 
> Následují ukázky pro převod dotazů z **OfficeActivity** na **SigninLogs**:
> 
> **Dotaz se nezdařil přihlášení podle uživatele:**
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
> **Zobrazení operací Azure AD:**
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
> ### <a name="q-how-can-i-on-board-azure-sentinel"></a>Otázka: Jak můžu napalubě Azure Sentinel?
> Azure Sentinel je řešení, které můžete povolit v novém nebo existujícím pracovním prostoru Log Analytics. Další informace najdete [v dokumentaci k nástupu do služby Azure Sentinel](../../sentinel/quickstart-onboard.md).
>
> ### <a name="q-do-i-need-azure-sentinel-to-connect-the-azure-ad-logs"></a>Otázka: Potřebuji Azure Sentinel k připojení protokolů Azure AD?
> Můžete nakonfigurovat [integraci protokolů Azure AD pomocí Azure Monitoru](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md), který nesouvisí s řešením Azure Sentinel. Azure Sentinel poskytuje nativní konektor a out-of-the box obsah pro protokoly Azure AD. Další informace naleznete v následující otázce o obsahu izolovaném zabezpečení.
>
> ###    <a name="q-what-are-the-differences-when-connecting-azure-ad-logs-from-azure-sentinel-and-azure-monitor"></a>Otázka: Jaké jsou rozdíly při připojování protokolů Azure AD z Azure Sentinelu a Azure Monitoru?
> Azure Sentinel a Azure Monitor se připojují k protokolům Azure AD na základě stejného [řešení pro vytváření sestav a monitorování Azure AD.](../../active-directory/reports-monitoring/plan-monitoring-and-reporting.md) Azure Sentinel poskytuje nativní konektor na jedno kliknutí, který propojuje stejná data a poskytuje informace o monitorování.
>
> ###    <a name="q-what-do-i-need-to-change-when-moving-to-the-new-azure-ad-reporting-and-monitoring-tables"></a>Otázka: Co je potřeba změnit při přechodu na nové tabulky sestav a monitorování Azure AD?
> Všechny dotazy využívající data Azure AD, včetně dotazů ve výstrahách, řídicích panelech a veškerém obsahu, který jste vytvořili pomocí dat Office 365 Azure AD, je nutné znovu vytvořit pomocí nových tabulek.
>
> Azure Sentinel a Azure AD poskytují integrovaný obsah, který můžete použít při přechodu na řešení azure ad sestav a monitorování. Další informace najdete v další otázce týkající se obsahu izolovaného zabezpečení a [použití sešitů Azure Monitor pro sestavy Služby Azure Active Directory](../../active-directory/reports-monitoring/howto-use-azure-monitor-workbooks.md). 
>
> ### <a name="q-how-i-can-use-the-azure-sentinel-out-of-the-box-security-oriented-content"></a>Otázka: Jak můžu používat obsah azure sentinel u dál po vybalení z krabice?
> Azure Sentinel poskytuje přehledzabezpečeníně orientované řídicí panely, vlastní dotazy na výstrahy, vyhledávací dotazy, vyšetřování a možnosti automatické odpovědi založené na protokolech Office 365 a Azure AD. Prozkoumejte GitHub Azure Sentinel a kurzy, kde se dozvíte víc:
>
> - [Detekce hrozeb inas](../../sentinel/tutorial-detect-threats-built-in.md)
> - [Vytváření vlastních analytických pravidel pro detekci podezřelých hrozeb](../../sentinel/tutorial-detect-threats-custom.md)
> - [Monitorování dat](../../sentinel/tutorial-monitor-your-data.md)
> - [Vyšetřování incidentů s Azure Sentinelem](../../sentinel/tutorial-investigate-cases.md)
> - [Nastavení automatických odpovědí na hrozby v Azure Sentinelu](../../sentinel/tutorial-respond-threats-playbook.md)
> - [Komunita GitHub azure sentinelu](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks)
> 
> ### <a name="q-does-azure-sentinel-provide-additional-connectors-as-part-of-the-solution"></a>Otázka: Poskytuje Azure Sentinel další konektory jako součást řešení?
> Ano, viz [Azure Sentinel připojit zdroje dat](../../sentinel/connect-data-sources.md).
> 
> ###    <a name="q-what-will-happen-on-july-30-do-i-need-to-offboard-beforehand"></a>Otázka: Co se stane 30. Musím se předem vylodit?
> 
> - Nebudete moct přijímat data z řešení **Office365.** Řešení již nebude k dispozici na webu Marketplace.
> - Pro zákazníky Azure Sentinelu bude řešení pracovního prostoru Log Analytics **Office365** zahrnuto do řešení Azure Sentinel **SecurityInsights.**
> - Pokud řešení nevypnete ručně, budou vaše data 30.
> 
> ### <a name="q-will-my-data-transfer-to-the-new-solution"></a>Otázka: Budou se moje data přenášet do nového řešení?
> Ano. Když odeberete řešení **Office 365** z pracovního prostoru, jeho data budou dočasně nedostupná, protože schéma je odebráno. Když povolíte nový konektor **Office 365** v Sentinelu, schéma se obnoví do pracovního prostoru a všechna data, která již byla shromážděna, budou k dispozici. 
 

Řešení pro správu Office 365 umožňuje monitorovat prostředí Office 365 v Azure Monitoru.

- Sledujte aktivity uživatelů na svých účtech Office 365, abyste analyzovali vzorce využití a identifikovali trendy chování. Můžete například extrahovat konkrétní scénáře použití, například soubory sdílené mimo vaši organizaci nebo nejoblíbenější weby služby SharePoint.
- Sledování aktivit správců za účelem sledování změn konfigurace nebo operací s vysokými oprávněními.
- Zjišťujte a prozkoumejte nežádoucí chování uživatelů, které lze přizpůsobit potřebám vaší organizace.
- Prokažte audit a dodržování předpisů. Můžete například sledovat operace přístupu k souborům u důvěrných souborů, což vám může pomoci s procesem auditu a dodržování předpisů.
- Proveďte provozní řešení potíží pomocí [dotazů protokolu](../log-query/log-query-overview.md) nad daty aktivit Office 365 vaší organizace.


## <a name="uninstall"></a>Odinstalace

Řešení pro správu Office 365 můžete odebrat pomocí procesu v části [Odebrat řešení pro správu](solutions.md#remove-a-monitoring-solution). Tím se ale nezastaví shromažďování dat z Office 365 do Azure Monitoru. Chcete-li se odhlásit z Office 365 a přestat shromažďovat data, postupujte podle následujícího postupu.

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

2. Spusťte skript pomocí následujícího příkazu:

    ```
    .\office365_unsubscribe.ps1 -WorkspaceName <Log Analytics workspace name> -ResourceGroupName <Resource Group name> -SubscriptionId <Subscription ID> -OfficeTennantID <Tenant ID> 
    ```

    Příklad:

    ```powershell
    .\office365_unsubscribe.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631-yyyyyyyyyyyy' -OfficeTennantID 'ce4464f8-a172-4dcf-b675-xxxxxxxxxxxx'
    ```

Budete vyzváni k zadání přihlašovacích údajů. Zadejte přihlašovací údaje pro pracovní prostor Log Analytics.

## <a name="data-collection"></a>Shromažďování dat

Může trvat několik hodin, než budou data zpočátku shromážděna. Jakmile začne shromažďovat, Office 365 odešle [oznámení webhooku](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference#receiving-notifications) s podrobnými daty do Azure Monitoru při každém vytvoření záznamu. Tento záznam je k dispozici ve službě Azure Monitor během několika minut po přijetí.

## <a name="using-the-solution"></a>Použití řešení

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Když přidáte řešení Office 365 do pracovního prostoru Log Analytics, dlaždice **Office 365** se přidá na řídicí panel. Na této dlaždici se zobrazuje počet a grafické vyjádření počtu počítačů ve vašem prostředí a jejich kompatibilita s aktualizacemi.<br><br>
![Dlaždice souhrnu Office 365](media/solution-office-365/tile.png)  

Kliknutím na dlaždici **Office 365** otevřete řídicí panel **Office 365.**

![Řídicí panel Office 365](media/solution-office-365/dashboard.png)  

Řídicí panel obsahuje sloupce v následující tabulce. V každém sloupci je uvedeno deset nejvyšších výstrah podle počtu, který odpovídá kritériím tohoto sloupce pro zadaný rozsah a časový rozsah. Hledání protokolu, které poskytuje celý seznam, můžete spustit kliknutím na Zobrazit vše v dolní části sloupce nebo kliknutím na záhlaví sloupce.

| Sloupec | Popis |
|:--|:--|
| Operace | Obsahuje informace o aktivních uživatelích ze všech sledovaných předplatných Office 365. Budete také moci zobrazit počet aktivit, které se dějí v průběhu času.
| Výměna | Zobrazuje rozdělení aktivit serveru Exchange Server, jako je oprávnění k přidávací schránce nebo set-mailová schránka. |
| SharePoint | Zobrazuje hlavní aktivity, které uživatelé provádějí v sharepointových dokumentech. Při přechodu na tuto dlaždici se na stránce hledání zobrazí podrobnosti o těchto aktivitách, například cílový dokument a umístění této aktivity. Například pro událost přístup k souboru budete moci zobrazit dokument, ke kterému se přistupuje, jeho přidružený název účtu a adresu IP. |
| Azure Active Directory | Zahrnuje hlavní aktivity uživatelů, jako je například obnovení uživatelského hesla a pokusy o přihlášení. Při přechodu k podrobnostem budete moci zobrazit podrobnosti o těchto aktivitách, jako je stav výsledku. To je většinou užitečné, pokud chcete sledovat podezřelé aktivity ve službě Azure Active Directory. |




## <a name="azure-monitor-log-records"></a>Záznamy protokolu Azure Monitor

Všechny záznamy vytvořené v pracovním prostoru Log Analytics v Azure Monitor u řešení Office 365 mají **typ** **OfficeActivity**.  Vlastnost **OfficeWorkload** určuje, na kterou službu Office 365 záznam odkazuje – Exchange, AzureActiveDirectory, SharePoint nebo OneDrive.  Vlastnost **RecordType** určuje typ operace.  Vlastnosti se budou lišit pro každý typ operace a jsou uvedeny v následujících tabulkách.

### <a name="common-properties"></a>Společné vlastnosti

Následující vlastnosti jsou společné pro všechny záznamy Office 365.

| Vlastnost | Popis |
|:--- |:--- |
| Typ | *OfficeActivity* |
| ClientIP | IP adresa zařízení použitá během protokolování aktivity. IP adresa se zobrazí ve formátu adresy IPv4 nebo IPv6. |
| Pracovní vytížení office | Služba Office 365, na kterou se záznam vztahuje.<br><br>AzureActiveDirectory<br>Výměna<br>SharePoint|
| Operace | Název aktivity uživatele nebo správce.  |
| OrganizationId | Identifikátor GUID pro tenanta Office 365 vaší organizace. Tato hodnota bude pro vaši organizaci vždy stejná, bez ohledu na službu Office 365, ve které k ní dochází. |
| RecordType | Typ provedené operace. |
| ResultStatus | Určuje, jestli byla akce (zadaná ve vlastnosti Operation) úspěšná, nebo ne. Možné hodnoty jsou úspěšné, částečně úspěšné nebo neúspěšné. Pro aktivitu správce Exchange je hodnota True nebo False. |
| UserId | Hlavní název uživatele (hlavní jméno uživatele) uživatele, který provedl akci, která vedla k zaznamenání záznamu; například my_name@my_domain_name. Všimněte si, že jsou zahrnuty také záznamy o činnosti prováděné systémovými účty (například SHAREPOINT\system nebo NTAUTHORITY\SYSTEM). | 
| UserKey | Alternativní ID pro uživatele identifikovaného ve vlastnosti UserId.  Tato vlastnost je například naplněna jedinečným ID pasu (PUID) pro události prováděné uživateli na SharePointu, OneDrivu pro firmy a Exchange. Tato vlastnost může také určit stejnou hodnotu jako vlastnost UserID pro události, ke kterým dochází v jiných službách a událostech prováděných systémovými účty|
| UserType | Typ uživatele, který provedl operaci.<br><br>Správce<br>Aplikace<br>Správce řadičů domény<br>Regular<br>Vyhrazeno<br>ServicePrincipal<br>Systém |


### <a name="azure-active-directory-base"></a>Základna služby Azure Active Directory

Následující vlastnosti jsou společné pro všechny záznamy služby Azure Active Directory.

| Vlastnost | Popis |
|:--- |:--- |
| Pracovní vytížení office | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AzureActiveDirectory_EventType | Typ události Azure AD. |
| ExtendedProperties | Rozšířené vlastnosti události Azure AD. |


### <a name="azure-active-directory-account-logon"></a>Přihlášení k účtu služby Azure Active Directory

Tyto záznamy jsou vytvořeny při pokusu o přihlášení uživatele služby Active Directory.

| Vlastnost | Popis |
|:--- |:--- |
| `OfficeWorkload` | AzureActiveDirectory |
| `RecordType`     | AzureActiveDirectoryAccountLogon |
| `Application` | Aplikace, která aktivuje událost přihlášení k účtu, jako je například Office 15. |
| `Client` | Podrobnosti o klientském zařízení, operačním prohlížeči zařízení a prohlížeči zařízení, který byl použit pro událost přihlášení k účtu. |
| `LoginStatus` | Tato vlastnost je z OrgIdLogon.LoginStatus přímo. Mapování různých zajímavých chyb přihlášení lze provést upozorněním algoritmy. |
| `UserDomain` | Informace o identitě klienta (TII). | 


### <a name="azure-active-directory"></a>Azure Active Directory

Tyto záznamy se vytvoří při změně nebo přidání objektů služby Azure Active Directory.

| Vlastnost | Popis |
|:--- |:--- |
| Pracovní vytížení office | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AADTarget | Uživatel, který akce (identifikované Operation vlastnost) byla provedena na. |
| Herec | Instanční objekt uživatele nebo služby, který akci provedl. |
| ActorContextId | Identifikátor GUID organizace, do které objekt actor patří. |
| ActorIpAddress | IP adresa objektu actor ve formátu IPV4 nebo IPV6. |
| InterSystemsId | Identifikátor GUID, který sleduje akce napříč součástmi v rámci služby Office 365. |
| Id intrasystemid |     Identifikátor GUID generovaný službou Azure Active Directory ke sledování akce. |
| SupportTicketId | ID lístku podpory zákazníků pro akci v situacích "jednat jménem". |
| Parametr TargetContextId | Identifikátor GUID organizace, do které cílový uživatel patří. |


### <a name="data-center-security"></a>Zabezpečení datového centra

Tyto záznamy jsou vytvořeny z dat auditu zabezpečení datového centra.  

| Vlastnost | Popis |
|:--- |:--- |
| Efektivníorganizace | Jméno klienta, na kterého byla zamířena nadečná rutina. |
| ElevationApprovedTime | Časové razítko pro schválení elevace. |
| Oprávnění Proschválení výšku | Jméno správce společnosti Microsoft. |
| Doba trvání výšky | Doba, po kterou byla výška aktivní. |
| Oprávnění ke zvýšení požadavků na výšku |     Jedinečný identifikátor pro požadavek na zvýšení oprávnění. |
| Funkce zvýšení | Role, pro kterou bylo zvýšení požadováno. |
| Doba převýšení | Počáteční čas nadmořské výšky. |
| Start_Time | Čas zahájení provádění rutiny. |


### <a name="exchange-admin"></a>Správce Exchange

Tyto záznamy jsou vytvořeny při provádění změn v konfiguraci serveru Exchange.

| Vlastnost | Popis |
|:--- |:--- |
| Pracovní vytížení office | Výměna |
| RecordType     | Správce Exchange |
| Externí přístup |     Určuje, zda byla rutina spuštěna uživatelem ve vaší organizaci, pracovníky datového centra společnosti Microsoft nebo účtem služby datového centra nebo delegovaným správcem. Hodnota False označuje, že rutina byla spuštěna někým ve vaší organizaci. Hodnota True označuje, že rutina byla spuštěna pracovníky datového centra, účtem služby datového centra nebo delegovaným správcem. |
| ModifiedObjectResolvedName |     Toto je uživatelsky přívětivý název objektu, který byl změněn rutinou. To je zaznamenána pouze v případě, že rutina upraví objekt. |
| OrganizationName | Jméno nájemce. |
| Původní server | Název serveru, ze kterého byla rutina spuštěna. |
| Parametry | Název a hodnota pro všechny parametry, které byly použity s rutinou, která je identifikována v Operations vlastnost. |


### <a name="exchange-mailbox"></a>Poštovní schránka serveru Exchange

Tyto záznamy jsou vytvořeny při změny nebo dodatky jsou provedeny poštovníschránky serveru Exchange.

| Vlastnost | Popis |
|:--- |:--- |
| Pracovní vytížení office | Výměna |
| RecordType     | Položka ExchangeItem |
| KlientInfoString | Informace o e-mailovém klientovi, který byl použit k provedení operace, například verze prohlížeče, verze aplikace Outlook a informace o mobilním zařízení. |
| Client_IPAddress | IP adresa zařízení, která byla použita při zaznamenání operace. IP adresa se zobrazí ve formátu adresy IPv4 nebo IPv6. |
| Název klientského počítače | Název počítače, který je hostitelem klienta aplikace Outlook. |
| Název_klientského procesu | E-mailový klient, který byl použit pro přístup k poštovní schránce. |
| ClientVersion | Verze e-mailového klienta . |
| InternalLogonType | Vyhrazeno pro vnitřní použití. |
| Logon_Type | Označuje typ uživatele, který přistupoval k poštovní schránce a provedl operaci, která byla zaznamenána. |
| LogonUserDisplayName |     Uživatelsky přívětivý název uživatele, který operaci provedl. |
| LogonUserSid | SID uživatele, který operaci provedl. |
| Poštovní schránkaGuid | Identifikátor GUID serveru Exchange poštovní schránky, ke které byl přístup. |
| Poštovní schránkaOwnerMasterAccountSid | SID hlavního účtu účtu vlastníka poštovní schránky. |
| Poštovní schránkaOwnerSid | SID vlastníka poštovní schránky. |
| Vlastník poštovní schránkyUPN | E-mailová adresa osoby, která vlastní poštovní schránku, ke které byl přístup n. |


### <a name="exchange-mailbox-audit"></a>Audit poštovní schránky serveru Exchange

Tyto záznamy jsou vytvořeny při vytvoření položky auditu poštovní schránky.

| Vlastnost | Popis |
|:--- |:--- |
| Pracovní vytížení office | Výměna |
| RecordType     | Položka ExchangeItem |
| Položka | Představuje položku, na které byla provedena operace | 
| SendAsUserMailboxGuid | Identifikátor GUID serveru Exchange poštovní schránky, ke které byl přistupován k odesílání e-mailů. |
| SendAsUserSmtp | Adresa SMTP uživatele, který je zosobněn. |
| SendonBehalfOfUserMailboxGuid | Identifikátor GUID serveru Exchange poštovní schránky, ke které byl přistupován k odesílání pošty jménem. |
| SendOnBehalfOfUserSmtp | Adresa SMTP uživatele, jehož jménem je e-mail odeslán. |


### <a name="exchange-mailbox-audit-group"></a>Skupina auditu poštovní schránky exchange

Tyto záznamy jsou vytvořeny při provádění změn nebo dodatků ve skupinách serveru Exchange.

| Vlastnost | Popis |
|:--- |:--- |
| Pracovní vytížení office | Výměna |
| Pracovní vytížení office | ExchangeItemGroup |
| Ovlivněné položky | Informace o jednotlivých položkách ve skupině. |
| Operace CrossMailbox | Označuje, zda operace zahrnovala více než jednu poštovní schránku. |
| DestMailboxId | Nastavte pouze v případě, že je parametr CrossMailboxOperations True. Určuje identifikátor GUID cílové poštovní schránky. |
| DestMailboxOwnerMasterAccountSid | Nastavte pouze v případě, že je parametr CrossMailboxOperations True. Určuje SID pro hlavní účet SID cílového vlastníka poštovní schránky. |
| DestMailboxOwnerSid | Nastavte pouze v případě, že je parametr CrossMailboxOperations True. Určuje sid cílové poštovní schránky. |
| DestMailboxOwnerUPN | Nastavte pouze v případě, že je parametr CrossMailboxOperations True. Určuje hlavní název uživatele vlastníka cílové poštovní schránky. |
| Složka Dest | Cílová složka pro operace, jako je Například Move. |
| Složka | Složka, ve které je umístěna skupina položek. |
| Složky |     Informace o zdrojových složkách zapojených do operace; například pokud jsou vybrány a poté odstraněny složky. |


### <a name="sharepoint-base"></a>SharePoint Base

Tyto vlastnosti jsou společné pro všechny záznamy služby SharePoint.

| Vlastnost | Popis |
|:--- |:--- |
| Pracovní vytížení office | SharePoint |
| Pracovní vytížení office | SharePoint |
| EventSource | Identifikuje, že došlo k události v SharePointu. Možné hodnoty jsou SharePoint nebo ObjectModel. |
| ItemType | Typ objektu, ke kterému byl přistupován nebo byl změněn. Podrobnosti o typech objektů naleznete v tabulce ItemType. |
| Informace o machinedomaininfo | Informace o operacích synchronizace zařízení. Tyto informace jsou hlášeny pouze v případě, že je k dispozici v požadavku. |
| MachineId |     Informace o operacích synchronizace zařízení. Tyto informace jsou hlášeny pouze v případě, že je k dispozici v požadavku. |
| Site_ | Identifikátor GUID webu, na kterém je umístěn soubor nebo složka, ke které uživatel přistupuje. |
| Source_Name | Entita, která spustila auditovnou operaci. Možné hodnoty jsou SharePoint nebo ObjectModel. |
| Useragent | Informace o klientovi nebo prohlížeči uživatele. Tyto informace jsou poskytovány klientem nebo prohlížečem. |


### <a name="sharepoint-schema"></a>Schéma služby SharePoint

Tyto záznamy jsou vytvořeny při provádění změn konfigurace služby SharePoint.

| Vlastnost | Popis |
|:--- |:--- |
| Pracovní vytížení office | SharePoint |
| Pracovní vytížení office | SharePoint |
| Vlastní událost | Volitelný řetězec pro vlastní události. |
| Event_Data |     Volitelná datová část pro vlastní události. |
| Upravené vlastnosti | Tato vlastnost je zahrnuta pro události správce, jako je například přidání uživatele jako člena webu nebo skupiny správců kolekce webů. Vlastnost obsahuje název vlastnosti, která byla změněna (například skupina Správce webu), novou hodnotu změněné vlastnosti (například uživatele, který byl přidán jako správce webu) a předchozí hodnotu upraveného objektu. |


### <a name="sharepoint-file-operations"></a>Operace se soubory služby SharePoint

Tyto záznamy jsou vytvořeny jako odpověď na operace se soubory v sharepointu.

| Vlastnost | Popis |
|:--- |:--- |
| Pracovní vytížení office | SharePoint |
| Pracovní vytížení office | SharePointFileOperation |
| DestinationFileExtension | Přípona souboru, který je zkopírován nebo přesunut. Tato vlastnost je zobrazena pouze pro události FileCopied a FileMoved. |
| Destinationfilename | Název souboru, který je zkopírován nebo přesunut. Tato vlastnost je zobrazena pouze pro události FileCopied a FileMoved. |
| DestinationRelativeUrl | Adresa URL cílové složky, do které je soubor zkopírován nebo přesunut. Kombinace hodnot pro parametry SiteURL, DestinationRelativeURL a DestinationFileName je stejná jako hodnota vlastnosti ObjectID, což je úplný název cesty pro soubor, který byl zkopírován. Tato vlastnost je zobrazena pouze pro události FileCopied a FileMoved. |
| SharingType | Typ oprávnění ke sdílení, která byla přiřazena uživateli, se kterým byl prostředek sdílen. Tento uživatel je identifikován parametrem UserSharedWith. |
| Site_Url | Adresa URL webu, na kterém je umístěn soubor nebo složka, ke které uživatel přistupuje. |
| Rozšíření SourceFileExtension | Přípona souboru, ke kterému uživatel přistupoval. Tato vlastnost je prázdná, pokud je objekt, ke kterému byl přístup přístupná, složka. |
| Sourcefilename |     Název souboru nebo složky, ke kterým má uživatel přístup. |
| Url sourcerelativeurl | Adresa URL složky obsahující soubor, ke kterému uživatel přistupuje. Kombinace hodnot pro parametry SiteURL, SourceRelativeURL a SourceFileName je stejná jako hodnota vlastnosti ObjectID, což je úplný název cesty pro soubor, ke kterému uživatel přistupuje. |
| Sdílené s uživatelem |     Uživatel, se kterým byl prostředek sdílen. |




## <a name="sample-log-queries"></a>Ukázkové dotazy protokolu

Následující tabulka obsahuje ukázkové dotazy protokolu pro aktualizační záznamy shromážděné tímto řešením.

| Dotaz | Popis |
| --- | --- |
|Počet všech operací v předplatném Office 365 |OfficeActivity &#124; shrnout počet() podle operace |
|Použití sharepointových webů|OfficeActivity &#124; kde OfficeWorkload =~ "sharepoint" &#124; sumarizovat count() podle SiteUrl \| řazení podle Count asc|
|Operace přístupu k souborům podle typu uživatele | OfficeActivity &#124; sumarizovat počet() podle UserType |
|Sledování externích akcí na serveru Exchange|OfficeActivity &#124; kde OfficeWorkload =~ "exchange" a ExternalAccess == true|



## <a name="next-steps"></a>Další kroky

* Pomocí [dotazů protokolu v Azure Monitoru](../log-query/log-query-overview.md) zobrazte podrobná data aktualizací.
* [Vytvořte si vlastní řídicí panely,](../learn/tutorial-logs-dashboards.md) které zobrazí oblíbené vyhledávací dotazy Office 365.
* [Vytvořte výstrahy,](../platform/alerts-overview.md) které budou proaktivně upozorňovány na důležité aktivity Office 365.  
