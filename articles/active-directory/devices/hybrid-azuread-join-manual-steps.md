---
title: Ruční konfigurace hybridních zařízení připojených k Azure Active Directory | Microsoft Docs
description: Zjistěte, jak ručně nakonfigurovat hybridní zařízení připojená k Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/01/2018
ms.author: markvi
ms.reviewer: sandeo
ms.openlocfilehash: a3c35057af883eb790c44b3547072031eaf4ad2f
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/02/2018
ms.locfileid: "50962006"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-joined-devices-manually"></a>Kurz: Ruční konfigurace hybridních zařízení připojených k Azure Active Directory 

Díky správě zařízení v Azure Active Directory (Azure AD) můžete zajistit, aby uživatelé přistupovali k vašim prostředkům ze zařízení, která splňují vaše standardy zabezpečení a dodržování předpisů. Další podrobnosti najdete v [úvodu do správy zařízení v Azure Active Directory](overview.md).


> [!TIP]
> Pokud můžete použít Azure AD Connect, přečtěte si část věnovanou [výběru scénáře](hybrid-azuread-join-plan.md#select-your-scenario). Pomocí Azure AD Connect si můžete konfiguraci hybridního připojení k Azure AD výrazně zjednodušit.



Pokud máte místní prostředí Active Directory a chcete připojit svá zařízení připojená k doméně k Azure AD, můžete to provést tak, že nakonfigurujete hybridní zařízení připojená k Azure AD. V tomto kurzu se dozvíte, jak pro svá zařízení ručně nakonfigurovat hybridní připojení k Azure AD.

> [!div class="checklist"]
> * Požadavky
> * Postup konfigurace
> * Konfigurace spojovacího bodu služby
> * Nastavení vystavování deklarací identity
> * Povolení zařízení s Windows nižší úrovně
> * Ověření připojených zařízení
> * Řešení potíží s implementací
 




## <a name="prerequisites"></a>Požadavky

V tomto kurzu se předpokládá znalost následujících témat:
    
-  [Úvod do správy zařízení v Azure Active Directory](../device-management-introduction.md)
    
-  [Plánování implementace hybridního připojení Azure Active Directory](hybrid-azuread-join-plan.md)

-  [Jak řídit How hybridní připojení Azure AD pro vaše zařízení](hybrid-azuread-join-control.md)


Než ve vaší organizaci začnete povolovat hybridní zařízení připojená k Azure AD, je potřeba se ujistit, že:

- Používáte aktuální verzi Azure AD Connect.

- Nástroj Azure AD Connect synchronizoval počítačové objekty zařízení, které chcete hybridně připojit k Azure AD. Pokud počítačové objekty patří do konkrétních organizačních jednotek, je potřeba v Azure AD Connect nakonfigurovat synchronizaci také těchto organizačních jednotek.

  

Azure AD Connect:

- Udržuje přidružení mezi účtem počítače ve vaší místní službě Active Directory (AD) a objektem zařízení v Azure AD. 
- Povoluje další funkce související se zařízeními, jako je Windows Hello pro firmy.

Ujistěte se, že počítače v síti vaší organizace mají přístup k následujícím adresám URL umožňujícím registraci počítačů do Azure AD:

- https://enterpriseregistration.windows.net

- https://login.microsoftonline.com Povolit
- https://device.login.microsoftonline.com

- Služba tokenů zabezpečení vaší organizace (federované domény)

Pokud tomu tak ještě není, služba tokenů zabezpečení vaší organizace (pro federované domény) by se měla přidat do nastavení místního intranetu uživatele.

Pokud vaše organizace plánuje využívat bezproblémové jednotné přihlašování, musí mít počítače ve vaší organizaci přístup k následujícím adresám URL, které je také potřeba přidat do zóny místního intranetu uživatele:

- https://autologon.microsoftazuread-sso.com

- Kromě toho by v zóně intranetu uživatele mělo být povolené následující nastavení: Povolit aktualizace stavového řádku přes skript.

Pokud vaše organizace využívá spravovanou (jinou než federovanou) instalaci s místní službou AD a neprovádí federaci s Azure AD pomocí AD FS, pak se hybridní připojení k Azure AD ve Windows 10 bude spoléhat synchronizaci počítačových objektů ve službě AD do Azure AD. Ujistěte se, že je v konfiguraci synchronizace nástroje Azure AD Connect povolená synchronizace všech organizačních jednotek obsahujících počítačové objekty, které je potřeba hybridně připojit k Azure AD.

V případě zařízení s Windows 10 verze 1703 nebo starší platí, že pokud vaše organizace potřebuje přístup k internetu přes odchozí proxy server, je potřeba implementovat Automatické zjišťování webových proxy serverů (WPAD), kterým umožníte registraci počítačů s Windows 10 v Azure AD. 

Počínaje Windows 10 verze 1803 platí, že i když dojde k selhání pokusu o hybridní připojení zařízení ve federované doméně k Azure AD pomocí AD FS a za předpokladu, že je nástroj Azure AD Connect nakonfigurovaný k synchronizaci objektů počítačů a zařízení s Azure AD, pokusí se zařízení dokončit hybridní připojení k Azure AD pomocí synchronizovaného počítače nebo zařízení.

## <a name="configuration-steps"></a>Postup konfigurace

Hybridní zařízení připojená k Azure AD můžete nakonfigurovat pro různé typy platforem zařízení s Windows. Toto téma obsahuje požadované kroky pro všechny obvyklé scénáře konfigurace.  

V následující tabulce najdete přehled požadovaných kroků pro váš scénář:  



| Kroky                                      | Aktuální Windows a synchronizace hodnoty hash hesel | Aktuální Windows a federace | Windows nižší úrovně |
| :--                                        | :-:                                    | :-:                            | :-:                |
| Konfigurace spojovacího bodu služby | ![Zaškrtnout][1]                            | ![Zaškrtnout][1]                    | ![Zaškrtnout][1]        |
| Nastavení vystavování deklarací identity           |                                        | ![Zaškrtnout][1]                    | ![Zaškrtnout][1]        |
| Povolení zařízení bez Windows 10      |                                        |                                | ![Zaškrtnout][1]        |
| Ověření připojených zařízení          | ![Zaškrtnout][1]                            | ![Zaškrtnout][1]                    | ![Zaškrtnout][1]        |



## <a name="configure-service-connection-point"></a>Konfigurace spojovacího bodu služby

Vaše zařízení během registrace pomocí objektu spojovacího bodu služby zjišťují informace o tenantovi Azure AD. V místní službě Active Directory (AD) musí objekt spojovacího bodu služby pro hybridní zařízení připojená k Azure AD existovat v oddílu názvového kontextu konfigurace doménové struktury počítače. Pro každou doménovou strukturu existuje pouze jeden názvový kontext konfigurace. V konfiguraci Active Directory s více doménovými strukturami musí spojovací bod služby existovat ve všech doménových strukturách obsahujících počítače připojené k doméně.

Pomocí rutiny [**Get-ADRootDSE**](https://technet.microsoft.com/library/ee617246.aspx) můžete načíst názvový kontext konfigurace vaší doménové struktury.  

Pro doménovou strukturu s názvem domény Active Directory *fabrikam.com* je názvový kontext konfigurace následující:

`CN=Configuration,DC=fabrikam,DC=com`

Ve vaší doménové struktuře se objekt spojovacího bodu služby pro automatickou registraci zařízení připojených k doméně nachází v tomto umístění:  

`CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,[Your Configuration Naming Context]`

V závislosti na způsobu nasazení Azure AD Connect je možné, že je objekt spojovacího bodu služby již definovaný.
Pomocí následujícího skriptu Windows PowerShellu můžete ověřit, jestli objekt existuje, a načíst zjištěné hodnoty: 

    $scp = New-Object System.DirectoryServices.DirectoryEntry;

    $scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=fabrikam,DC=com";

    $scp.Keywords;

Výstup příkazu **$scp.Keywords** ukazuje informace o tenantovi Azure, například:

    azureADName:microsoft.com
    azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47

Pokud spojovací bod služby neexistuje, můžete ho vytvořit spuštěním rutiny `Initialize-ADSyncDomainJoinedComputerSync` na vašem serveru Azure AD Connect. Ke spuštění této rutiny se vyžadují přihlašovací údaje podnikového správce.  
Tato rutina:

- Vytvoří spojovací bod služby v doménové struktuře Active Directory, ke které je nástroj Azure AD Connect připojený. 
- Vyžaduje, abyste zadali parametr `AdConnectorAccount`. Jedná se o účet, který je v Azure AD Connect nakonfigurovaný jako účet konektoru Active Directory. 


Následující skript ukazuje příklad použití této rutiny. V tomto skriptu příkaz `$aadAdminCred = Get-Credential` vyžaduje, abyste zadali uživatelské jméno. Musíte zadat uživatelské jméno ve formátu hlavního názvu uživatele (UPN) (`user@example.com`). 


    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;

Rutina `Initialize-ADSyncDomainJoinedComputerSync`:

- Využívá modul Active Directory PowerShell a nástroje AD DS, které spoléhají na službu Active Directory Web Services spuštěnou v řadiči domény. Služba Active Directory Web Services se podporuje v řadičích domény s Windows Serverem 2008 R2 nebo novějším.
- Se podporuje pouze v **modulu MSOnline PowerShell verze 1.1.166.0**. Tento modul můžete stáhnout na tomto [odkazu](https://msconfiggallery.cloudapp.net/packages/MSOnline/1.1.166.0/).   
- Pokud nejsou nainstalované nástroje AD DS, rutina `Initialize-ADSyncDomainJoinedComputerSync` selže.  Nástroje AD DS je možné nainstalovat přes Správce serveru v části Funkce -> Nástroje pro vzdálenou správu serveru -> Nástroje pro správu rolí.

Pro řadiče domény s Windows Serverem 2008 nebo starší verzí vytvořte spojovací bod služby pomocí následujícího skriptu.

V konfiguraci s více doménovými strukturami byste pomocí následujícího skriptu měli vytvořit spojovací bod služby v každé doménové struktuře obsahující počítače:
 
    $verifiedDomain = "contoso.com"    # Replace this with any of your verified domain names in Azure AD
    $tenantID = "72f988bf-86f1-41af-91ab-2d7cd011db47"    # Replace this with you tenant ID
    $configNC = "CN=Configuration,DC=corp,DC=contoso,DC=com"    # Replace this with your AD configuration naming context

    $de = New-Object System.DirectoryServices.DirectoryEntry
    $de.Path = "LDAP://CN=Services," + $configNC
    $deDRC = $de.Children.Add("CN=Device Registration Configuration", "container")
    $deDRC.CommitChanges()

    $deSCP = $deDRC.Children.Add("CN=62a0ff2e-97b9-4513-943f-0d221bd30080", "serviceConnectionPoint")
    $deSCP.Properties["keywords"].Add("azureADName:" + $verifiedDomain)
    $deSCP.Properties["keywords"].Add("azureADId:" + $tenantID)

    $deSCP.CommitChanges()

Ve výše uvedeném skriptu:

- `$verifiedDomain = "contoso.com"` je zástupná hodnota, kterou musíte nahradit některým z vašich ověřených názvů domén v Azure AD. Abyste mohli doménu použít, musíte ji vlastnit.

Další podrobnosti o ověřených názvech domén najdete v tématu [Přidání vlastního názvu domény do Azure Active Directory](../active-directory-domains-add-azure-portal.md).  
Pokud chcete zobrazit seznam ověřených domén vaší společnosti, můžete použít rutinu [Get-AzureADDomain](/powershell/module/Azuread/Get-AzureADDomain?view=azureadps-2.0). 

![Get-AzureADDomain](./media/hybrid-azuread-join-manual-steps/01.png)

## <a name="setup-issuance-of-claims"></a>Nastavení vystavování deklarací identity

Ve federované konfiguraci Azure AD se zařízení při ověřování v Azure AD spoléhají na službu Active Directory Federation Services (AD FS) nebo místní federační služby třetích stran. Zařízení se ověřují za účelem získání přístupového tokenu, pomocí kterého se můžou zaregistrovat ve službě Azure Active Directory Device Registration (Azure DRS).

Aktuální zařízení s Windows se ověřují pomocí Integrovaného ověřování systému Windows v aktivním koncovém bodu WS-Trust (verze 1.3 nebo 2005) hostovaném místní federační službou.

> [!NOTE]
> Pokud používáte AD FS, musí být povolený koncový bod **adfs/services/trust/13/windowstransport** nebo **adfs/services/trust/2005/windowstransport**. Pokud používáte webový ověřovací proxy server, také se ujistěte, že je tento koncový bod publikovaný prostřednictvím proxy serveru. Povolené koncové body můžete zobrazit v konzole pro správu služby AD FS v části **Služba -> Koncové body**.
>
>Pokud jako místní federační službu nepoužíváte AD FS, postupujte podle pokynů vašeho dodavatele a ujistěte se, že podporuje koncové body WS-Trust 1.3 nebo 2005 a že jsou publikované prostřednictvím souboru MetadataExchange (MEX).

Token přijatý službou Azure DRS musí obsahovat následující deklarace identity, aby bylo možné dokončit registraci zařízení. Azure DRS vytvoří v Azure AD objekt zařízení s částí těchto informací, které pak Azure AD Connect použije k přidružení nově vytvořeného objektu zařízení k místnímu účtu počítače.

* `http://schemas.microsoft.com/ws/2012/01/accounttype`
* `http://schemas.microsoft.com/identity/claims/onpremobjectguid`
* `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`

Pokud máte více než jeden ověřený název domény, musíte počítačům poskytnout následující deklaraci identity:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid`

Pokud již vystavujete deklaraci identity ImmutableID (např. alternativní přihlašovací ID), budete počítačům muset poskytnout jednu odpovídající deklaraci identity:

* `http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`

V následujících částech najdete informace o těchto tématech:
 
- Hodnoty, které by každá deklarace identity měla obsahovat
- Jak by definice vypadala ve službě AD FS

Definice vám pomůže ověřit, jestli požadované hodnoty existují, nebo jestli je potřeba je vytvořit.

> [!NOTE]
> Pokud jako svůj místní federační server nepoužíváte AD FS, postupujte podle pokynů vašeho dodavatele a vytvořte odpovídající konfiguraci pro vystavování těchto deklarací identity.

### <a name="issue-account-type-claim"></a>Vystavení deklarace identity typu účtu

**`http://schemas.microsoft.com/ws/2012/01/accounttype`** – Tato deklarace identity musí obsahovat hodnotu **DJ**, která zařízení identifikuje jako počítač připojený k doméně. Ve službě AD FS můžete přidat pravidlo transformace vystavování, které vypadá přibližně takto:

    @RuleName = "Issue account type for domain-joined computers"
    c:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value = "DJ"
    );

### <a name="issue-objectguid-of-the-computer-account-on-premises"></a>Vystavení identity objectGUID místního účtu počítače

**`http://schemas.microsoft.com/identity/claims/onpremobjectguid`** – Tato deklarace identity musí obsahovat hodnotu **objectGUID** místního účtu počítače. Ve službě AD FS můžete přidat pravidlo transformace vystavování, které vypadá přibližně takto:

    @RuleName = "Issue object GUID for domain-joined computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        store = "Active Directory", 
        types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), 
        query = ";objectguid;{0}", 
        param = c2.Value
    );
 
### <a name="issue-objectsid-of-the-computer-account-on-premises"></a>Vystavení identity objectSID místního účtu počítače

**`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`** – Tato deklarace identity musí obsahovat hodnotu **objectSid** místního účtu počítače. Ve službě AD FS můžete přidat pravidlo transformace vystavování, které vypadá přibližně takto:

    @RuleName = "Issue objectSID for domain-joined computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(claim = c2);

### <a name="issue-issuerid-for-computer-when-multiple-verified-domain-names-in-azure-ad"></a>Vystavení identity issuerID pro počítač v případě více ověřených názvů domén v Azure AD

**`http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid`** – Tato deklarace identity musí obsahovat identifikátor URI některého z ověřených názvů domén, které se připojují k místní federační službě (služba AD FS nebo služba třetí strany) vystavující token. Ve službě AD FS můžete za výše uvedená pravidla transformace vystavování přidat přesně v uvedeném pořadí následující pravidla transformace vystavování. Mějte na paměti, že je potřeba jedno pravidlo, které explicitně vystaví pravidlo pro uživatele. V následujících pravidlech se přidalo první pravidlo, které identifikuje ověřování uživatele nebo počítače.

    @RuleName = "Issue account type with the value User when its not a computer"
    NOT EXISTS(
    [
        Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value == "DJ"
    ]
    )
    => add(
        Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value = "User"
    );
    
    @RuleName = "Capture UPN when AccountType is User and issue the IssuerID"
    c1:[
        Type == "http://schemas.xmlsoap.org/claims/UPN"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value == "User"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", 
        Value = regexreplace(
        c1.Value, 
        ".+@(?<domain>.+)", 
        "http://${domain}/adfs/services/trust/"
        )
    );
    
    @RuleName = "Issue issuerID for domain-joined computers"
    c:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", 
        Value = "http://<verified-domain-name>/adfs/services/trust/"
    );


Ve výše uvedené deklaraci identity:

- `<verified-domain-name>` je zástupná hodnota, kterou musíte nahradit některým z vašich ověřených názvů domén v Azure AD. Například: Value = "http://contoso.com/adfs/services/trust/"



Další podrobnosti o ověřených názvech domén najdete v tématu [Přidání vlastního názvu domény do Azure Active Directory](../active-directory-domains-add-azure-portal.md).  

Pokud chcete zobrazit seznam ověřených domén vaší společnosti, můžete použít rutinu [Get-MsolDomain](/powershell/module/msonline/get-msoldomain?view=azureadps-1.0). 

![Get-MsolDomain](./media/hybrid-azuread-join-manual-steps/01.png)

### <a name="issue-immutableid-for-computer-when-one-for-users-exist-eg-alternate-login-id-is-set"></a>Vystavení identity ImmutableID pro počítač v případě, že pro uživatele existuje (např. je nastavené alternativní přihlašovací ID)

**`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`** – Tato deklarace identity musí obsahovat platnou hodnotu pro počítače. Ve službě AD FS můžete vytvořit následující pravidlo transformace vystavování:

    @RuleName = "Issue ImmutableID for computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ] 
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        store = "Active Directory", 
        types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"), 
        query = ";objectguid;{0}", 
        param = c2.Value
    );

### <a name="helper-script-to-create-the-ad-fs-issuance-transform-rules"></a>Pomocný skript pro vytváření pravidel transformace vystavování ve službě AD FS

Následující skript vám pomůže s vytvořením výše popsaných pravidel transformace vystavování.

    $multipleVerifiedDomainNames = $false
    $immutableIDAlreadyIssuedforUsers = $false
    $oneOfVerifiedDomainNames = 'example.com'   # Replace example.com with one of your verified domains
    
    $rule1 = '@RuleName = "Issue account type for domain-joined computers"
    c:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value = "DJ"
    );'

    $rule2 = '@RuleName = "Issue object GUID for domain-joined computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        store = "Active Directory", 
        types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), 
        query = ";objectguid;{0}", 
        param = c2.Value
    );'

    $rule3 = '@RuleName = "Issue objectSID for domain-joined computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(claim = c2);'

    $rule4 = ''
    if ($multipleVerifiedDomainNames -eq $true) {
    $rule4 = '@RuleName = "Issue account type with the value User when it is not a computer"
    NOT EXISTS(
    [
        Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value == "DJ"
    ]
    )
    => add(
        Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value = "User"
    );
    
    @RuleName = "Capture UPN when AccountType is User and issue the IssuerID"
    c1:[
        Type == "http://schemas.xmlsoap.org/claims/UPN"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value == "User"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", 
        Value = regexreplace(
        c1.Value, 
        ".+@(?<domain>.+)", 
        "http://${domain}/adfs/services/trust/"
        )
    );
    
    @RuleName = "Issue issuerID for domain-joined computers"
    c:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", 
        Value = "http://' + $oneOfVerifiedDomainNames + '/adfs/services/trust/"
    );'
    }

    $rule5 = ''
    if ($immutableIDAlreadyIssuedforUsers -eq $true) {
    $rule5 = '@RuleName = "Issue ImmutableID for computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ] 
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        store = "Active Directory", 
        types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"), 
        query = ";objectguid;{0}", 
        param = c2.Value
    );'
    }

    $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules 

    $updatedRules = $existingRules + $rule1 + $rule2 + $rule3 + $rule4 + $rule5

    $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules 

    Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString 

### <a name="remarks"></a>Poznámky 

- Tento skript připojí pravidla k existujícím pravidlům. Nespouštějte tento skript dvakrát, protože by se sada pravidel přidala dvakrát. Před opětovným spuštěním skriptu se ujistěte, že pro tyto deklarace identity neexistují žádná odpovídající pravidla (za odpovídajících podmínek).

- Pokud máte více ověřených názvů domén (jak můžete zjistit na portálu Azure AD nebo pomocí rutiny Get-MsolDomains), nastavte hodnotu proměnné **$multipleVerifiedDomainNames** ve skriptu na **$true**. Nezapomeňte také odebrat případnou existující deklaraci identity issuerID, která se mohla vytvořit nástrojem Azure AD Connect nebo jiným způsobem. Tady je příklad tohoto pravidla:


        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"]
        => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)",  "http://${domain}/adfs/services/trust/")); 

- Pokud jste již pro uživatelské účty vystavili deklaraci identity **ImmutableID** nastavte hodnotu proměnné **$immutableIDAlreadyIssuedforUsers** ve skriptu na **$true**.

## <a name="enable-windows-down-level-devices"></a>Povolení zařízení s Windows nižší úrovně

Pokud jsou některá z vašich zařízení připojených k doméně zařízení s Windows nižší úrovně, je potřeba provést následující:

- Nastavit v Azure AD zásadu, která uživatelům umožní registrovat zařízení.
 
- Nakonfigurovat místní federační službu na vystavování deklarací identity s podporou **Integrovaného ověřování systému Windows** pro registraci zařízení.
 
- Přidat koncový bod ověřování zařízení Azure AD do zón místního intranetu, aby se zabránilo zobrazování výzev k zadání certifikátu při ověřování zařízení.

- Řízení zařízení s Windows nižší úrovně 


### <a name="set-policy-in-azure-ad-to-enable-users-to-register-devices"></a>Nastavení zásady v Azure AD, která uživatelům umožní registrovat zařízení

Pokud chcete registrovat zařízení s Windows nižší úrovně, je potřeba zajistit nastavení, které uživatelům umožní registrovat zařízení v Azure AD. Na webu Azure Portal najdete toto nastavení v části:

`Azure Active Directory > Users and groups > Device settings`
    
Následující zásada musí být nastavená na hodnotu **Všichni**: **Uživatelé můžou registrovat svoje zařízení do Azure AD**.

![Registrace zařízení](./media/hybrid-azuread-join-manual-steps/23.png)


### <a name="configure-on-premises-federation-service"></a>Konfigurace místní federační služby 

Vaše místní federační služba musí podporovat vystavování deklarací identity **authenticationmethod** a **wiaormultiauthn**, když přijímající strana Azure AD přijme žádost o ověření, která obsahuje parametr resouce_params se zašifrovanou hodnotou, jak je znázorněno níže:

    eyJQcm9wZXJ0aWVzIjpbeyJLZXkiOiJhY3IiLCJWYWx1ZSI6IndpYW9ybXVsdGlhdXRobiJ9XX0

    which decoded is {"Properties":[{"Key":"acr","Value":"wiaormultiauthn"}]}

Při přijetí takové žádosti musí místní federační služba ověřit uživatele pomocí Integrovaného ověřování systému Windows a v případě úspěchu musí vystavit následující dvě deklarace identity:

    http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/windows
    http://schemas.microsoft.com/claims/wiaormultiauthn

Ve službě AD FS musíte přidat pravidlo transformace vystavování, které projde přes metodu ověřování.  

**Přidání tohoto pravidla:**

1. V konzole pro správu služby AD FS přejděte do části `AD FS > Trust Relationships > Relying Party Trusts`.
2. Klikněte pravým tlačítkem na objekt důvěryhodnosti přijímající strany služby Microsoft Office 365 Identity Platform a pak vyberte **Upravit pravidla deklarace identity**.
3. Na kartě **Pravidla transformace vystavování** vyberte **Přidat pravidlo**.
4. V seznamu šablon **Pravidlo deklarace identity** vyberte **Odesílat deklarace pomocí vlastního pravidla**.
5. Vyberte **Další**.
6. Do pole **Název pravidla deklarace identity** zadejte **Pravidlo deklarace identity metody ověřování**.
7. Do pole **Pravidlo deklarace identity** zadejte následující pravidlo:

    `c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"] => issue(claim = c);`

8. Na federačním serveru zadejte následující příkaz PowerShellu, ve kterém nejprve nahraďte **\<RPObjectName\>** názvem objektu přijímající strany pro váš objekt důvěryhodnosti přijímající strany Azure AD. Tento objekt má obvykle název **Microsoft Office 365 Identity Platform**.
   
    `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

### <a name="add-the-azure-ad-device-authentication-end-point-to-the-local-intranet-zones"></a>Přidání koncového bodu ověřování zařízení Azure AD do zón místního intranetu

Pokud se chcete vyhnout zobrazování výzev k zadání certifikátu při ověřování uživatelů na zaregistrovaných zařízeních v Azure AD, můžete do zařízení připojených k doméně odeslat nabízenou zásadu, která přidá následující adresy URL do zóny Místní intranet v aplikaci Internet Explorer:

`https://device.login.microsoftonline.com`


### <a name="control-windows-down-level-devices"></a>Řízení zařízení s Windows nižší úrovně 

Pokud chcete registrovat zařízení s Windows nižší úrovně, musíte si z webu Download Center stáhnout a nainstalovat balíček Instalační služby systému Windows (.msi). Další informace získáte kliknutím [sem](hybrid-azuread-join-control.md#control-windows-down-level-devices). 



## <a name="verify-joined-devices"></a>Ověření připojených zařízení

Úspěšně připojená zařízení ve vaší organizaci můžete zkontrolovat pomocí rutiny [Get-MsolDevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice) v [modulu Azure Active Directory PowerShell](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

Výstup této rutiny ukazuje zařízení zaregistrovaná a připojená k Azure AD. Pokud chcete získat všechna zařízení, použijte parametr **-All**, a pak je vyfiltrujte pomocí vlastnosti **deviceTrustType**. U zařízení připojených k doméně má tato vlastnost hodnotu **Domain Joined** (Připojeno k doméně).



## <a name="troubleshoot-your-implementation"></a>Řešení potíží s implementací

Pokud máte problémy s dokončením hybridního připojení k Azure AD pro zařízení s Windows připojená k doméně, přečtěte si následující témata:

- [Řešení potíží s hybridním připojením aktuálních zařízení s Windows k Azure AD](troubleshoot-hybrid-join-windows-current.md)
- [Řešení potíží s hybridním připojením zařízení s Windows nižší úrovně k Azure AD](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Další kroky

* [Úvod do správy zařízení v Azure Active Directory](overview.md)



<!--Image references-->
[1]: ./media/hybrid-azuread-join-manual-steps/12.png
