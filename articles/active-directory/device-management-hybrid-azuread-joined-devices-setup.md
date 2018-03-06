---
title: "Jak nakonfigurovat hybridní připojená k Azure Active Directory zařízení | Microsoft Docs"
description: "Naučte se konfigurovat hybridní Azure Active Directory připojené zařízení."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 203e36b198186db63b7e902db296adeaa9ffb4ee
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2018
---
# <a name="how-to-configure-hybrid-azure-active-directory-joined-devices"></a>Postup konfigurace hybridní Azure Active Directory připojené zařízení

Se správou zařízení ve službě Azure Active Directory (Azure AD) můžete zajistit, že vaši uživatelé přistupují k prostředkům ze zařízení, která splňují vaše standardy zabezpečení a dodržování předpisů. Další podrobnosti najdete v tématu [Úvod do správy zařízení v Azure Active Directory](device-management-introduction.md).

Pokud máte prostředí místní služby Active Directory a chcete pro připojení zařízení připojených k doméně ke službě Azure AD, můžete k tomu lze konfigurace hybridní Azure AD, které jsou připojené k zařízení. Téma vám poskytne související kroky. 


## <a name="before-you-begin"></a>Než začnete

Před zahájením konfigurace zařízení služby Azure AD, které jsou připojené k hybridní ve vašem prostředí, by měl Seznamte se s Podporované scénáře a omezení.  

Pokud se spoléhat na [nástroj pro přípravu systému (Sysprep)](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-vista/cc721940(v=ws.10)), Zkontrolujte prosím, že vytvoření bitové kopie z instalace systému Windows, který nebyl dosud zaregistrován v Azure AD.

Všechna zařízení připojená k doméně spuštěný Windows 10 Anniversary Update a Windows Server 2016 automatickou registraci v rámci Azure AD při restartování zařízení nebo uživatele přihlásit až po dokončení konfiguračních kroků uvedených níže. Pokud není upřednostňovaný toto chování automatické registrace nebo pokud se požaduje řízené zavedení, postupujte podle pokynů v následující části řízení nasazení a zavedení nejprve selektivně povolit nebo zakázat automatické zavedení před provedením dalších kroky konfigurace.  

Toto téma ke zlepšení čitelnosti popisy, používá následující období: 

- **Aktuální zařízení se systémem Windows** -tento termín se vztahuje k doméně, zařízení se systémem Windows 10 nebo Windows Server 2016.
- **Zařízení se systémem Windows nižší úrovně** -tento termín se vztahuje na všechny **podporované** připojený k doméně zařízení Windows, která nejsou spuštěné Windows 10 ani systému Windows Server 2016.  

### <a name="windows-current-devices"></a>Aktuální zařízení se systémem Windows

- Pro zařízení se systémem Windows desktop operačního systému, je podporovaná verze Windows 10 Anniversary Update (verze 1607) nebo novější. 
- Registrace zařízení se systémem Windows aktuální **je** podporované v nefederované prostředích, jako je konfigurace synchronizace hodnoty hash hesla.  


### <a name="windows-down-level-devices"></a>Zařízení se systémem Windows nižší úrovně

- Jsou podporovány následující zařízení se systémem Windows nižší úrovně:
    - Windows 8.1
    - Windows 7
    - Windows Server 2012 R2
    - Windows Server 2012
    - Windows Server 2008 R2
- Registrace zařízení se systémem Windows nižší úrovně **je** podporované v prostředích nefederovaných prostřednictvím bezproblémové jednotné přihlašování [Azure Active Directory bezproblémové jednotné přihlašování](https://aka.ms/hybrid/sso).
- Registrace zařízení se systémem Windows nižší úrovně **není** podporovaná pro zařízení s použitím profilů roamingu. Pokud se spoléháte na cestovních profilů nebo nastavení, používají systém Windows 10.



## <a name="prerequisites"></a>Požadavky

Než začnete, povolení hybridního Azure AD, které jsou připojené k zařízení ve vaší organizaci, musíte zajistit, že používáte aktuální verzi Azure AD connect.

Azure AD Connect:

- Zachová přidružení mezi účet počítače v místní službě Active Directory (AD) a objektu zařízení ve službě Azure AD. 
- Povoluje další zařízení související s funkcí, jako Windows Hello pro firmy.

Ujistěte se, že následující adresy URL jsou přístupné z počítače v síti vaší organizace pro registraci počítačů do Azure AD:

- https://enterpriseregistration.windows.net

- https://login.microsoftonline.com

- https://device.login.microsoftonline.com

Pokud vaše organizace vyžaduje přístup k Internetu prostřednictvím odchozího proxy serveru, musíte implementací Proxy Auto-Discovery WPAD (Web) Chcete-li povolit počítače s Windows 10 k registraci do služby Azure AD.


## <a name="configuration-steps"></a>Kroky konfigurace

Můžete nakonfigurovat hybridní zařízení připojených k Azure AD pro různé typy zařízení platformy systému Windows. Toto téma obsahuje požadované kroky pro všechny scénáře obvyklá konfigurace.  

Následující tabulku použijte k získání přehledu kroky, které jsou požadovány pro váš scénář:  



| Kroky                                      | Synchronizace hodnot hash aktuální a heslo systému Windows | Aktuální Windows a federation | Windows nižší úrovně |
| :--                                        | :-:                                    | :-:                            | :-:                |
| Krok 1: Konfigurace spojovacího bodu služby | ![Zaškrtnout][1]                            | ![Zaškrtnout][1]                    | ![Zaškrtnout][1]        |
| Krok 2: Nastavení vystavování deklarací identity           |                                        | ![Zaškrtnout][1]                    | ![Zaškrtnout][1]        |
| Krok 3: Zapnutí zařízení s Windows 10      |                                        |                                | ![Zaškrtnout][1]        |
| Krok 4: Řízení nasazení a zavedení     | ![Zaškrtnout][1]                            | ![Zaškrtnout][1]                    | ![Zaškrtnout][1]        |
| Krok 5: Ověření připojeného k zařízení          | ![Zaškrtnout][1]                            | ![Zaškrtnout][1]                    | ![Zaškrtnout][1]        |



## <a name="step-1-configure-service-connection-point"></a>Krok 1: Konfigurace spojovacího bodu služby

Objekt připojení Spojovací bod služby se používá vaše zařízení během registrace ke zjišťování informace klienta Azure AD. Ve vaší místní služby Active Directory (AD) musí existovat objekt spojovací bod služby pro zařízení Azure AD, které jsou připojené k hybridní v kontextu názvové počítače doménové struktury. Není k dispozici pouze jeden názvovém kontextu konfigurace pro každou doménovou strukturu. V konfiguraci s více doménovými strukturami služby Active Directory musí existovat spojovací bod služby ve všech doménových strukturách obsahující počítače, připojený k doméně.

Můžete použít [ **Get-ADRootDSE** ](https://technet.microsoft.com/library/ee617246.aspx) rutiny načíst názvovém kontextu konfigurace doménové struktury.  

Pro doménovou strukturu s názvem domény služby Active Directory *fabrikam.com*, je v názvovém kontextu konfigurace:

`CN=Configuration,DC=fabrikam,DC=com`

V doménové struktuře se nachází v objektu spojovací bod služby pro automatickou registraci zařízení připojená k doméně:  

`CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,[Your Configuration Naming Context]`

V závislosti na tom, jak jste nasadili Azure AD Connect objekt spojovací bod služby může mít již byla konfigurována.
Můžete ověřit existenci objekt a načíst hodnoty zjišťování pomocí následujícího skriptu prostředí Windows PowerShell: 

    $scp = New-Object System.DirectoryServices.DirectoryEntry;

    $scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=fabrikam,DC=com";

    $scp.Keywords;

**$Scp. Klíčová slova** výstup zobrazuje informace o klientovi Azure AD, například:

    azureADName:microsoft.com
    azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47

Pokud spojovací bod služby neexistuje, můžete ho vytvořit spuštěním `Initialize-ADSyncDomainJoinedComputerSync` rutiny na serveru Azure AD Connect. Ke spuštění této rutiny je vyžadováno přihlašovací údaje podnikového správce.  
Rutinu:

- Vytvoří spojovací bod služby v doménové struktuře služby Active Directory, Azure AD Connect je připojena k. 
- Vyžaduje, abyste zadali `AdConnectorAccount` parametr. Toto je účet, který je nakonfigurovaný jako účet konektoru ve službě Azure AD connect služby Active Directory. 


Tento skript je ukázkou příklad, pomocí rutiny. V tento skript `$aadAdminCred = Get-Credential` vyžaduje, abyste zadejte uživatelské jméno. Je třeba zadat uživatelské jméno ve formátu hlavního názvu (UPN) uživatele (`user@example.com`). 


    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;

`Initialize-ADSyncDomainJoinedComputerSync` Rutiny:

- Používá modul Active Directory PowerShell a nástroje služby AD DS, které jsou založeny na spuštěných na řadiči domény Active Directory Web Services. Služba Active Directory Web Services je podporována v řadičích domény se systémem Windows Server 2008 R2 nebo novější.
- Je podporován pouze serverem **MSOnline PowerShell verze modulu 1.1.166.0**. Chcete-li stáhnout tento modul, použijte toto [odkaz](http://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185).   
- Pokud nejsou nainstalovány nástroje služby AD DS, `Initialize-ADSyncDomainJoinedComputerSync` se nezdaří.  Nástroje služby AD DS se dá nainstalovat pomocí Správce serveru v části funkce – nástrojů pro vzdálenou správu – nástroje pro správu rolí.

Pro řadiče domény se systémem Windows Server 2008 nebo dřívějších verzí použijte níže uvedeném skriptu k vytvoření spojovací bod služby.

V konfiguraci s více doménovými strukturami používejte následující skript k vytvoření spojovací bod služby v každé doménové struktuře, kde existují počítače:
 
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


## <a name="step-2-setup-issuance-of-claims"></a>Krok 2: Nastavení vystavování deklarací identity

V federované Azure AD konfigurace zařízení závisí na službě Active Directory Federation Services (AD FS) nebo 3. stran místní služby FS k ověření do služby Azure AD. Zařízení ověření získání přístupového tokenu k registraci proti Azure Active Directory Device Registration Service (Azure DRS).

Windows, které aktuální zařízení ověřování pomocí integrovaného ověřování systému Windows na aktivní koncový bod WS-Trust (verze 1.3 nebo 2005) hostované službou federation service na místě.

> [!NOTE]
> Při použití služby AD FS buď **adfs/services/vztah důvěryhodnosti nebo 13 nebo windowstransport** nebo **služby AD FS nebo služby nebo vztahu důvěryhodnosti/2005/windowstransport** musí být povolena. Pokud používáte webový proxy server ověřování, ujistěte se také, že tento koncový bod publikovaný prostřednictvím proxy serveru. Uvidíte, jaké koncových bodů jsou povolené prostřednictvím konzoly pro správu služby AD FS v části **služby > Koncové body**.
>
>Pokud nemáte jako místní federační služby AD FS, postupujte podle pokynů dodavatele a ujistěte se, že podporují WS-Trust 1.3 nebo 2005 koncových bodů a že jsou tyto publikovaných prostřednictvím soubor metadat Exchange (MEX).

Následující deklarace identity musí existovat v tokenu přijatých Azure DRS pro registraci zařízení pro dokončení. Azure DRS vytvoří objekt zařízení ve službě Azure AD pomocí některé z těchto informací, které se pak použije přes Azure AD Connect k objektu nově vytvořený zařízení přidružit počítači účet místní.

* `http://schemas.microsoft.com/ws/2012/01/accounttype`
* `http://schemas.microsoft.com/identity/claims/onpremobjectguid`
* `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`

Pokud máte více než jeden název ověřené domény, budete muset zadat následující deklarace identity pro počítače:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid`

Pokud jsou již vydání deklaraci identity ImmutableID (například alternativního přihlašovacího ID), budete muset zadat jeden odpovídající deklarace identity pro počítače:

* `http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`

V následujících částech najdete informace o:
 
- Hodnoty, které by měly mít jednotlivých deklarací identity
- Jak bude vypadat definice ve službě AD FS

Definice pomáhá ověřte, zda jsou hodnoty přítomen nebo pokud potřebujete k jejich vytvoření.

> [!NOTE]
> Pokud nepoužíváte místní federačního serveru služby AD FS, postupujte podle pokynů od dodavatele vytvořit odpovídající konfiguraci vystavovat tyto deklarace identity.

### <a name="issue-account-type-claim"></a>Deklarace typu účtu problém

**`http://schemas.microsoft.com/ws/2012/01/accounttype`** – Tato deklarace identity musí obsahovat hodnotu **DJ**, které identifikují zařízení jako počítač připojený k doméně. Ve službě AD FS můžete přidat pravidlo transformace vystavování, které vypadá takto:

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

### <a name="issue-objectguid-of-the-computer-account-on-premises"></a>Problém objectGUID na počítači účet místního

**`http://schemas.microsoft.com/identity/claims/onpremobjectguid`** – Tato deklarace identity musí obsahovat **objectGUID** hodnota účtu místního počítače. Ve službě AD FS můžete přidat pravidlo transformace vystavování, které vypadá takto:

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
 
### <a name="issue-objectsid-of-the-computer-account-on-premises"></a>Problém objectSID na počítači účet místního

**`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`** – Tato deklarace identity musí obsahovat **objectSid** hodnota účtu místního počítače. Ve službě AD FS můžete přidat pravidlo transformace vystavování, které vypadá takto:

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

### <a name="issue-issuerid-for-computer-when-multiple-verified-domain-names-in-azure-ad"></a>Vystavovat issuerID pro počítač, pokud ověření více názvů domén ve službě Azure AD

**`http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid`** – Tato deklarace identity musí obsahovat identifikátor URI (Uniform Resource) všech názvy ověřené domény, které se připojují pomocí místní služby federation service (AD FS nebo 3. stran) vydání tokenu. Ve službě AD FS můžete přidat pravidla transformace vystavení, které vypadají jako jsou níže v tomto konkrétní pořadí po ty výše. Upozorňujeme, že je nutné tento jedno pravidlo explicitně vystavit pravidlo pro uživatele. V pravidlech níže se přidá první pravidlo identifikace uživatele nebo ověřování počítače.

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


Ve výše uvedené, deklarace identity

- `<verified-domain-name>` je zástupný symbol, který potřebujete nahradit s jedním názvů ověřené domény ve službě Azure AD. Například hodnota = "http://contoso.com/adfs/services/trust/"



Další informace o názvech ověřené domény najdete v tématu [přidání vlastního názvu domény do Azure Active Directory](active-directory-domains-add-azure-portal.md).  
Chcete-li získat seznam ověřených společnosti domény, můžete použít [Get-MsolDomain](/powershell/module/msonline/get-msoldomain?view=azureadps-1.0) rutiny. 

![Get-MsolDomain](./media/active-directory-conditional-access-automatic-device-registration-setup/01.png)

### <a name="issue-immutableid-for-computer-when-one-for-users-exist-eg-alternate-login-id-is-set"></a>Vystavovat ImmutableID pro počítač, pokud pro uživatele k dispozici jeden (například alternativního přihlašovacího ID, je nastavena)

**`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`** – Tato deklarace identity musí obsahovat platnou hodnotu pro počítače. Ve službě AD FS můžete vytvořit pravidlo pro vystavování transformace následujícím způsobem:

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

### <a name="helper-script-to-create-the-ad-fs-issuance-transform-rules"></a>Pomocník skript pro vytvoření pravidel transformace pro vystavení služby AD FS

Následující skript pomáhá s vytvoření vystavení transformace pravidel popsaných výše.

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

- Tento skript připojí k existující pravidla pravidla. Nelze spustit skript dvakrát vzhledem k tomu, že sadu pravidel, které by byl přidán dvakrát. Ujistěte se, že neexistují žádná pravidla odpovídající před spuštěním skriptu znovu v těchto deklarací identity (v odpovídajících podmínkách).

- Pokud máte více názvů ověřené domény (jak je znázorněno na portálu Azure AD nebo pomocí rutiny Get-MsolDomains), nastavte hodnotu **$multipleVerifiedDomainNames** ve skriptu **$true**. Také se ujistěte, že odeberete všechny existující issuerid deklaraci, která může být vytvořen přes Azure AD Connect nebo prostřednictvím jiným způsobem. Tady je příklad pro toto pravidlo:


        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"]
        => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)",  "http://${domain}/adfs/services/trust/")); 

- Pokud jste již vystavily **ImmutableID** deklarace identity pro uživatelské účty, nastavte hodnotu **$immutableIDAlreadyIssuedforUsers** ve skriptu **$true**.

## <a name="step-3-enable-windows-down-level-devices"></a>Krok 3: Zapnutí zařízení se systémem Windows nižší úrovně

Pokud některé z vašich zařízení připojených k doméně Windows nižší úrovně zařízení, budete muset:

- Nastavte zásady ve službě Azure AD umožňuje uživatelům registrovat zařízení.
 
- Místní službu FS vystavovat deklarace identity pro podporu nakonfigurovat **integrované ověřování systému Windows (IWA)** pro registraci zařízení.
 
- Přidáte koncový bod ověřování zařízení služby Azure AD do místní zóny intranetu, abyste se vyhnuli výzev ohledně certifikátů při ověřování zařízení.

### <a name="set-policy-in-azure-ad-to-enable-users-to-register-devices"></a>Nastavit zásady ve službě Azure AD umožňuje uživatelům registrovat zařízení

Chcete-li zaregistrovat zařízení se systémem Windows nižší úrovně, ujistěte se, že bude uživatelům k registraci zařízení ve službě Azure AD povolit nastavení. Na portálu Azure můžete najít toto nastavení v části:

`Azure Active Directory > Users and groups > Device settings`
    
Tyto zásady musí být nastavena na **všechny**: **uživatelé mohou registrovat svá zařízení s Azure AD**

![Registrace zařízení](./media/active-directory-conditional-access-automatic-device-registration-setup/23.png)


### <a name="configure-on-premises-federation-service"></a>Nakonfigurujte místní služby FS 

Vaše místní federační služba musí podporovat vydání **authenticationmehod** a **wiaormultiauthn** deklarací při přijímání požadavek ověřování Azure AD předávající strany, která uchovává resouce_params parametr s hodnotou kódovaného, jak je uvedeno níže:

    eyJQcm9wZXJ0aWVzIjpbeyJLZXkiOiJhY3IiLCJWYWx1ZSI6IndpYW9ybXVsdGlhdXRobiJ9XX0

    which decoded is {"Properties":[{"Key":"acr","Value":"wiaormultiauthn"}]}

Pokud takový požadavek pochází, místní federační služby musí ověřit uživatele pomocí integrovaného ověřování systému Windows a na úspěch, vydá následující dvě deklarace identity:

    http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/windows
    http://schemas.microsoft.com/claims/wiaormultiauthn

Ve službě AD FS musíte přidat pravidel transformace vystavení, který předává prostřednictvím metody ověřování.  

**Chcete-li přidat toto pravidlo:**

1. V konzole pro správu služby AD FS, přejděte na `AD FS > Trust Relationships > Relying Party Trusts`.
2. Klikněte pravým tlačítkem na objekt pro vztah důvěryhodnosti předávající strany ze serveru Microsoft Office 365 Identity Platform a vyberte **upravit pravidla deklarací identity**.
3. Na **pravidlech transformace vystavení** vyberte **přidat pravidlo**.
4. V **pravidlo deklarace identity** seznam šablon, vyberte **odesílat deklarace pomocí vlastního pravidla**.
5. Vyberte **Next** (Další).
6. V **název pravidla deklarací** zadejte **pravidlo deklarace identity metoda ověřování**.
7. V **pravidlo deklarace identity** zadejte následující pravidlo:

    `c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"] => issue(claim = c);`

8. Na federačním serveru, zadejte příkaz prostředí PowerShell níže po nahrazení  **\<RPObjectName\>**  s předávající strany název objektu pro vztah důvěryhodnosti objektu předávající strany služby Azure AD. Tento objekt obvykle jmenuje **Microsoft Office 365 Identity Platform**.
   
    `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

### <a name="add-the-azure-ad-device-authentication-end-point-to-the-local-intranet-zones"></a>Přidejte do zóny místního intranetu koncový bod ověřování zařízení služby Azure AD

Aby se zabránilo certifikát vyzve při ověřování uživatelů v registraci zařízení do služby Azure AD můžete posouvat nastavení zásad pro vaše zařízení připojených k doméně, přidat následující adresy URL do zóny místního intranetu v aplikaci Internet Explorer:

`https://device.login.microsoftonline.com`

## <a name="step-4-control-deployment-and-rollout"></a>Krok 4: Řízení nasazení a zavedení

Po dokončení požadovaných kroků připojená k doméně jsou připravené k automatickému připojení Azure AD:

- Všechny připojené k doméně zařízení se systémem Windows 10 Anniversary Update a Windows Server 2016 automaticky zaregistrovat s Azure AD v zařízení restartujte nebo přihlášení uživatele. 

- Zaregistrovat nová zařízení s Azure AD při restartování zařízení po dokončení operace připojení k doméně.

- Registruje zařízení, které byly dříve Azure AD (například pro Intune) přechod do "*připojený k doméně, zaregistrováno v AAD*"; však bude trvat delší dobu pro tento proces dokončit ve všech zařízeních z důvodu normálního toku domény a aktivity uživatelů.

### <a name="remarks"></a>Poznámky

- Můžete objekt zásad skupiny a ovládejte automatické registrace Windows 10 a Windows Server 2016 počítačů připojených k doméně.

- Windows 10. listopadu 2015 aktualizace automaticky spojí se službou Azure AD **pouze** nastaveného zavedení objekt zásad skupiny.

- K zavedení počítačů se systémem Windows nižší úrovně, můžete nasadit [balíček služby Windows Installer](#windows-installer-packages-for-non-windows-10-computers) do počítačů, které jste vybrali.

- Pokud jste nabízená objekt zásad skupiny Windows 8.1 připojená k doméně, dojde k pokusu o spojení; nedoporučujeme ale, že používáte [balíček služby Windows Installer](#windows-installer-packages-for-non-windows-10-computers) připojit všechna zařízení Windows nižší úrovně. 

### <a name="create-a-group-policy-object"></a>Vytvoření objektu zásad skupiny 

Ovládejte aktuální počítačů se systémem Windows, měli byste nasadit **registraci připojený k doméně počítače jako zařízení** objekt zásad skupiny zařízení, který chcete zaregistrovat. Například můžete nasadit zásady do organizační jednotky nebo do skupiny zabezpečení.

**Nastavení zásad:**

1. Otevřete **správce serveru**a pak přejděte na `Tools > Group Policy Management`.
2. Přejděte na uzel domény, který odpovídá k doméně, ve které chcete aktivovat automatickou registraci aktuální počítačů se systémem Windows.
3. Klikněte pravým tlačítkem na **objekty zásad skupiny**a potom vyberte **nový**.
4. Zadejte název objektu zásad skupiny. Například * hybridní Azure AD join. 
5. Klikněte na **OK**.
6. Klikněte pravým tlačítkem na váš nový objekt zásad skupiny a potom vyberte **upravit**.
7. Přejděte na **konfigurace počítače** > **zásady** > **šablony pro správu** > **součásti systému Windows** > **registrace zařízení**. 
8. Klikněte pravým tlačítkem na **registraci připojený k doméně počítače jako zařízení**a potom vyberte **upravit**.
   
   > [!NOTE]
   > Tato šablona zásad skupiny byla přejmenována z dřívějších verzí konzoly pro správu zásad skupiny. Pokud používáte starší verzi konzoly, přejděte na `Computer Configuration > Policies > Administrative Templates > Windows Components > Workplace Join > Automatically workplace join client computers`. 

7. Vyberte **povoleno**a potom klikněte na **použít**. Je nutné vybrat **zakázané** Pokud chcete, aby zásady pro blokování zařízení ovládaná zásadami této skupiny automaticky registraci v Azure AD.

8. Klikněte na **OK**.
9. Propojte objekt zásad skupiny k umístění podle vaší volby. Například můžete propojit k určité organizační jednotce. Můžete také může ho propojit s konkrétní skupiny zabezpečení počítačů, které automaticky připojit k službě Azure AD. Nastavení této zásady pro všechny připojené k doméně Windows 10 a Windows Server 2016 počítače ve vaší organizaci, propojte objekt zásad skupiny do domény.

### <a name="windows-installer-packages-for-non-windows-10-computers"></a>Balíčky Instalační služby systému Windows pro počítače s Windows 10

K připojení k doméně počítače se systémem Windows nižší úrovně ve federovaném prostředí, můžete stáhnout a nainstalovat tyto balíček Instalační služby systému Windows (.msi) ze stažení softwaru společnosti Microsoft [připojení k síti na pracovišti Microsoft pro počítače s Windows 10](https://www.microsoft.com/en-us/download/details.aspx?id=53554) stránka.

Balíček lze nasadit pomocí systém distribuce softwaru jako je System Center Configuration Manager. Balíček podporuje možnosti standardní bezobslužnou instalaci s *quiet* parametr. System Center Configuration Manager aktuální větev nabízí další výhody z dřívějších verzí, jako je schopnost sledovat dokončené registrace. Další informace najdete v tématu [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager).

Instalační program vytvoří naplánovanou úlohu na systém, který běží v kontextu uživatele. Úloha se aktivuje, když se uživatel přihlásí systému Windows. Úloha bezobslužně připojí zařízení s Azure AD s pověřeními uživatele po ověřování pomocí integrovaného ověřování systému Windows. Chcete-li zobrazit naplánované úlohy, v zařízení, přejděte na **Microsoft** > **k síti na pracovišti**a potom přejděte na Knihovna plánovače úloh.

## <a name="step-5-verify-joined-devices"></a>Krok 5: Ověření připojeného k zařízení

Úspěšné připojeného k zařízení ve vaší organizaci můžete zkontrolovat pomocí [Get-MsolDevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice) rutiny v [modul Azure Active Directory PowerShell](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

Výstup této rutiny zobrazuje zařízení, které jsou zaregistrované a propojit s Azure AD. Všechna zařízení, použijte **– všechny** parametr a pak je filtrování pomocí **deviceTrustType** vlastnost. Připojené k doméně zařízení mít hodnotu **připojený k doméně**.

## <a name="next-steps"></a>Další postup

* [Úvod do správy zařízení v Azure Active Directory](device-management-introduction.md)



<!--Image references-->
[1]: ./media/active-directory-conditional-access-automatic-device-registration-setup/12.png
