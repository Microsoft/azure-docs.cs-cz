---
title: Ruční konfigurace zařízení Azure Active Directory připojená k hybridní | Dokumentace Microsoftu
description: Zjistěte, jak ručně nakonfigurovat zařízení Azure Active Directory připojená k hybridní.
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
ms.topic: article
ms.date: 08/02/2018
ms.author: markvi
ms.reviewer: sandeo
ms.openlocfilehash: 546717330a08b348800ea9c4c9cd7784f54595eb
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39618519"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-joined-devices-manually"></a>Kurz: Konfigurace zařízení Azure Active Directory připojená k hybridní ručně 

Se správou zařízení ve službě Azure Active Directory (Azure AD) můžete zajistit, že vaši uživatelé přistupují ze zařízení, která splňují vaše standardy zabezpečení a dodržování předpisů vašich prostředků. Další podrobnosti najdete v tématu [Úvod ke správě zařízení ve službě Azure Active Directory](overview.md).

Pokud máte v místním prostředí služby Active Directory a chcete připojovat zařízení připojených k doméně do Azure AD, můžete to provést pomocí konfigurace hybridních zařízení připojených k Azure AD. Tento článek obsahuje související kroky. 



> [!TIP]
> Pokud pomocí služby Azure AD Connect je pro vás, přečtěte si téma [vyberte váš scénář](hybrid-azuread-join-plan.md#select-your-scenario). Pomocí služby Azure AD Connect, můžete výrazně zjednodušit konfiguraci připojení k hybridní službě Azure AD.


## <a name="before-you-begin"></a>Než začnete

Před zahájením konfigurace hybridních zařízení připojených k Azure AD ve vašem prostředí, měli byste seznámit s Podporované scénáře a omezení.  

Pokud se spoléháte na [nástroj pro přípravu systému (Sysprep)](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-vista/cc721940(v=ws.10)), Zkontrolujte prosím, že vytvoříte Image z instalace systému Windows, který nebyl dosud zaregistrován v Azure AD.

Všechna zařízení připojená k doméně spuštěný systém Windows 10 Anniversary Update a Windows Server 2016 automaticky zaregistrují do služby Azure AD při restartování zařízení nebo uživatele přihlásit po dokončení kroků konfigurace uvedených níže. **Pokud toto chování automatického register není upřednostňovaný nebo v případě potřeby řízeně uvádět**, postupujte prosím podle pokynů v části "Krok 4: řízení nasazení a zavedení" abyste mohli selektivně povolit nebo zakázat automatické zavedení před následující další kroky konfigurace.  

Aby se zlepšila čitelnost popisů, tento článek používá následující výraz: 

- **Aktuální zařízení Windows** – tento výraz odkazuje na připojeném k doméně zařízení se systémem Windows 10 nebo Windows serveru 2016.
- **Zařízení Windows nižší úrovně** -tento výraz odkazuje na všechny **podporované** domény zařízení připojených k doméně Windows, které nejsou spuštěné Windows 10 ani Windows serveru 2016.  

### <a name="windows-current-devices"></a>Aktuální zařízení Windows

- Pro zařízení s operačním systémem klasické pracovní plochy Windows, je podporovaná verze Windows 10 Anniversary Update (verze 1607) nebo novější. 
- Registrace zařízení Windows **je** podporována v jiné než federované prostředí, jako je konfigurace synchronizace hodnoty hash hesla.  


### <a name="windows-down-level-devices"></a>Zařízení Windows nižší úrovně

- Jsou podporovány následující zařízení Windows nižší úrovně:
    - Windows 8.1
    - Windows 7
    - Windows Server 2012 R2
    - Windows Server 2012
    - Windows Server 2008 R2
- Registrace zařízení Windows nižší úrovně **je** podporována v jiné než federované prostředí pomocí bezproblémového jednotného přihlašování [Azure Active Directory bezproblémové jednotné přihlašování](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso-quick-start). 
- Registrace zařízení Windows nižší úrovně **není** podporováno při použití předávacího ověřování Azure AD bez bezproblémové jednotné přihlašování.
- Registrace zařízení Windows nižší úrovně **není** podporovaná pro zařízení s použitím profilů roamingu. Pokud se spoléháte na cestovní profily nebo nastavení, používají systém Windows 10.



## <a name="prerequisites"></a>Požadavky

Než začnete, povolení hybridních zařízení připojených k Azure AD ve vaší organizaci, je třeba Ujistěte se, že:

- Používáte aktuální verzi služby Azure AD connect.

- Služby Azure AD connect synchronizaci počítačových objektů zařízení chcete k hybridní službě Azure AD připojená k Azure AD. Pokud objekty počítače patří do konkrétní organizační jednotky (OU), pak tyto organizačních jednotek je potřeba nakonfigurovat pro synchronizaci ve službě Azure AD connect i.

  

Azure AD Connect:

- Udržuje přidružení účtu počítače ve vaší místní služby Active Directory (AD) a objekt zařízení ve službě Azure AD. 
- Povoluje další zařízení související funkce, jako je Windows Hello pro firmy.

Ujistěte se, že je přístupný z počítačů v síti vaší organizace pro registraci počítače do služby Azure AD následující adresy URL:

- https://enterpriseregistration.windows.net

- https://login.microsoftonline.com Povolit
- https://device.login.microsoftonline.com

- Služba tokenů zabezpečení vaší organizace (federovaných domén)

Pokud ještě neučinili, služba tokenů zabezpečení vaší organizace (u federovaných domén) by měl být součástí nastavení místní intranet uživatele.

Pokud vaše organizace plánuje pomocí bezproblémového jednotného přihlašování, následující adresy URL musí být dosažitelný z počítače ve vaší organizaci a také je nutné přidat do zóny místního intranetu uživatele:

- https://autologon.microsoftazuread-sso.com

- Kromě toho musí být v zóny intranet uživatele povolené následující nastavení: "Povolit aktualizace stavového řádku pomocí skriptu."

Pokud vaše organizace používá spravované instalace (jiné než federované) s místním AD a nepoužívá služby AD FS pro federaci s Azure AD, a připojení k hybridní službě Azure AD na Windows 10 spoléhá na počítačových objektů ve službě AD být sync'ed do služby Azure AD. Ujistěte se, že jsou povolené všechny organizační jednotky (OU), které obsahují objekty počítače, které musí být připojená k hybridní Azure AD pro synchronizaci v konfiguraci synchronizace Azure AD Connect.

Pro zařízení s Windows 10 ve verzi 1703 nebo starší Pokud vaše organizace potřebuje přístup k Internetu prostřednictvím odchozího proxy serveru, musíte implementovat Proxy Auto-Discovery WPAD (Web) umožňující počítače s Windows 10 k registraci do služby Azure AD. 

## <a name="configuration-steps"></a>Postup konfigurace

Můžete nakonfigurovat hybridních zařízení připojených k Azure AD pro různé typy platforem zařízení Windows. Toto téma obsahuje kroky pro všechny scénáře Typická konfigurace.  

Použijte následující tabulku k získání přehledu o krocích, které jsou požadovány pro váš scénář:  



| Kroky                                      | Synchronizace hodnot hash pro aktuální a heslo Windows | Aktuální Windows a federace | Windows nižší úrovně |
| :--                                        | :-:                                    | :-:                            | :-:                |
| Konfigurace spojovacího bodu služby | ![Zaškrtnout][1]                            | ![Zaškrtnout][1]                    | ![Zaškrtnout][1]        |
| Instalační program vystavování deklarací identity           |                                        | ![Zaškrtnout][1]                    | ![Zaškrtnout][1]        |
| Povolit zařízení s Windows 10      |                                        |                                | ![Zaškrtnout][1]        |
| Řízení nasazení a uvedení do provozu     | ![Zaškrtnout][1]                            | ![Zaškrtnout][1]                    | ![Zaškrtnout][1]        |
| Ověřit zařízení připojené k doméně          | ![Zaškrtnout][1]                            | ![Zaškrtnout][1]                    | ![Zaškrtnout][1]        |



## <a name="configure-service-connection-point"></a>Konfigurace spojovacího bodu služby

Objekt připojení Spojovací bod služby se používá vaše zařízení během registrace ke zjišťování informací z tenanta Azure AD. Ve vašem místním Active Directory (AD) musí existovat objekt spojovací bod služby pro hybridních zařízení připojených k Azure AD v konfiguraci názvové kontextu počítače doménové struktury. Existuje pouze jeden názvovém kontextu konfigurace pro každou doménovou strukturu. V konfiguraci s více doménovými strukturami služby Active Directory musí existovat spojovací bod služby ve všech doménových struktur obsahujících počítače připojené k doméně.

Můžete použít [ **Get-ADRootDSE** ](https://technet.microsoft.com/library/ee617246.aspx) rutina pro načtení názvovém kontextu konfigurace doménové struktury.  

Pro doménovou strukturu s názvem domény služby Active Directory *fabrikam.com*, je v názvovém kontextu konfigurace:

`CN=Configuration,DC=fabrikam,DC=com`

V doménové struktuře je umístěn na objekt spojovací bod služby pro automatickou registraci zařízení připojených k doméně:  

`CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,[Your Configuration Naming Context]`

V závislosti na tom, jak nasadíte Azure AD Connect objekt spojovací bod služby může mít už nakonfigurovaná.
Můžete ověřit existenci objektu a načtení zjišťování hodnot pomocí následujícího skriptu prostředí Windows PowerShell: 

    $scp = New-Object System.DirectoryServices.DirectoryEntry;

    $scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=fabrikam,DC=com";

    $scp.Keywords;

**$Scp. Klíčová slova** výstup zobrazuje informaci o tenantovi Azure AD, například:

    azureADName:microsoft.com
    azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47

Pokud je spojovací bod služby neexistuje, které můžete vytvořit spuštěním `Initialize-ADSyncDomainJoinedComputerSync` rutinu na serveru služby Azure AD Connect. Přihlašovací údaje správce organizace se vyžaduje ke spuštění této rutiny.  
Tuto rutinu:

- Spojovací bod služby se vytvoří v doménové struktuře služby Active Directory, který Azure AD Connect je připojen k. 
- Musíte zadat `AdConnectorAccount` parametru. Toto je účet, který je nakonfigurovaný jako účet konektoru v Azure AD connect služby Active Directory. 


Tento skript ukazuje příklad použití rutiny. V tomto skriptu `$aadAdminCred = Get-Credential` vyžaduje zadání uživatelského jména. Budete muset zadat uživatelské jméno ve formátu hlavního názvu (UPN) uživatele (`user@example.com`). 


    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;

`Initialize-ADSyncDomainJoinedComputerSync` Rutiny:

- Používá modul Active Directory PowerShell a nástroje služby AD DS, které jsou založeny na Active Directory Web Services běží na řadiči domény. Active Directory Web Services je podporována v řadičích domény se systémem Windows Server 2008 R2 nebo novějším.
- Je podporována pouze **MSOnline modul PowerShell verze 1.1.166.0**. Chcete-li stáhnout tento modul, použijte toto [odkaz](https://msconfiggallery.cloudapp.net/packages/MSOnline/1.1.166.0/).   
- Pokud nejsou nainstalované nástroje služby AD DS, `Initialize-ADSyncDomainJoinedComputerSync` se nezdaří.  Nástroje služby AD DS se dá nainstalovat pomocí Správce serveru v části funkce – nástroje pro vzdálenou správu – nástroje pro správu rolí.

Pro řadiče domény se systémem Windows Server 2008 nebo dřívější verze použijte níže uvedený skript k vytvoření spojovací bod služby.

V konfiguraci s více doménovými strukturami používejte následující skript k vytvoření spojovací bod služby v každé doménové struktuře, pokud existují počítače:
 
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

Ve výše uvedeného skriptu

- `$verifiedDomain = "contoso.com"` je zástupný symbol, který je potřeba nahradit jeden z názvů ověřené domény ve službě Azure AD. Budete muset tuto doménu vlastnit, než budete moct použít.

Další informace o názvech ověřenou doménu, najdete v článku [přidání názvu vlastní domény do Azure Active Directory](../active-directory-domains-add-azure-portal.md).  
Pokud chcete získat seznam domén ověřené společnosti, můžete použít [Get-AzureADDomain](/powershell/module/Azuread/Get-AzureADDomain?view=azureadps-2.0) rutiny. 

![Get-AzureADDomain](./media/hybrid-azuread-join-manual-steps/01.png)

## <a name="setup-issuance-of-claims"></a>Instalační program vystavování deklarací identity

V federované Azure AD konfigurace závisí na službě Active Directory Federation Services (AD FS) nebo 3. stran místní federační služba k ověření do služby Azure AD. Ověření zařízení k získání přístupového tokenu pro registraci proti Azure Active Directory Device Registration Service (Azure DRS).

Windows, které aktuální zařízení ověřování pomocí integrovaného Windows ověřování na aktivní koncový bod WS-Trust (verze 1.3 nebo 2005) hostuje místní federační služby.

> [!NOTE]
> Při použití služby AD FS buď **adfs/services/vztah důvěryhodnosti/13/windowstransport** nebo **adfs/services/vztah důvěryhodnosti/2005/windowstransport** musí být povolené. Pokud používáte webový proxy server ověřování, zkontrolujte také, že je tento koncový bod publikované prostřednictvím proxy serveru. Uvidíte, jaké koncové body jsou povolené prostřednictvím konzoly pro správu služby AD FS v části **služby > Koncové body**.
>
>Pokud nemáte jako místní federační služby AD FS, postupujte podle pokynů dodavatele, ujistěte se, že podporují WS-Trust 1.3 nebo koncové body 2005 a tyto publikují v souboru metadat systému Exchange (MEX).

Následující deklarace identity musí existovat v tokenu přijatých DRS Azure k registraci zařízení pro dokončení. Služba DRS Azure vytvoří objekt zařízení ve službě Azure AD pomocí některé z těchto informací, které se potom využijí služby Azure AD Connect k přidružení počítače účtu místní zařízení nově vytvořený objekt.

* `http://schemas.microsoft.com/ws/2012/01/accounttype`
* `http://schemas.microsoft.com/identity/claims/onpremobjectguid`
* `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`

Pokud máte více než jeden název ověřené domény, musíte poskytnout následující deklarace identity pro počítače:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid`

Pokud již poskytujete deklaraci identity ImmutableID (například alternativního přihlašovacího ID), že je potřeba zadat jeden odpovídající deklarace identity pro počítače:

* `http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`

V následující části najdete informace o:
 
- Hodnoty, které by měly mít jednotlivých deklarací identity
- Jak bude vypadat definici ve službě AD FS

Definici vám umožní ověřit, zda jsou k dispozici hodnoty, nebo pokud potřebujete k jejich vytvoření.

> [!NOTE]
> Pokud nepoužíváte místní federačního serveru služby AD FS, postupujte podle pokynů od dodavatele k vytvoření odpovídající konfiguraci vydání tyto deklarace.

### <a name="issue-account-type-claim"></a>Deklarace typu účtu problém

**`http://schemas.microsoft.com/ws/2012/01/accounttype`** – Tato deklarace identity musí obsahovat hodnotu **DJ**, který identifikuje zařízení jako počítače připojené k doméně. Ve službě AD FS můžete přidat pravidlo transformace pro vystavování, který vypadá takto:

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

### <a name="issue-objectguid-of-the-computer-account-on-premises"></a>Problém objectGUID počítači účet místního

**`http://schemas.microsoft.com/identity/claims/onpremobjectguid`** – Musí obsahovat tento požadavek **objectGUID** hodnotu účtu v místním počítači. Ve službě AD FS můžete přidat pravidlo transformace pro vystavování, který vypadá takto:

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
 
### <a name="issue-objectsid-of-the-computer-account-on-premises"></a>Atributy objectSID problém počítači účet místního

**`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`** – Musí obsahovat tento požadavek **objectSid** hodnotu účtu v místním počítači. Ve službě AD FS můžete přidat pravidlo transformace pro vystavování, který vypadá takto:

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

### <a name="issue-issuerid-for-computer-when-multiple-verified-domain-names-in-azure-ad"></a>Vydat issuerID pro počítač více ověření názvů domén ve službě Azure AD

**`http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid`** – Tato deklarace identity musí obsahovat identifikátor URI (Uniform Resource) u všech názvy ověřenou doménu, která se připojují ke službě federation místní (služby AD FS nebo 3. stran) vydání tokenu. Ve službě AD FS můžete přidat pravidla transformace vystavování, které vypadají jako ty níže v tomto konkrétním pořadí po ty výše. Upozorňujeme, že je nutné tuto jedno pravidlo explicitně vydat pravidlo pro uživatele. V pravidlech níže je přidat první pravidlo identifikace uživatele nebo ověřování počítače.

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

- `<verified-domain-name>` je zástupný symbol, který je potřeba nahradit jeden z názvů ověřené domény ve službě Azure AD. Například, hodnota = "http://contoso.com/adfs/services/trust/"



Další informace o názvech ověřenou doménu, najdete v článku [přidání názvu vlastní domény do Azure Active Directory](../active-directory-domains-add-azure-portal.md).  

Pokud chcete získat seznam domén ověřené společnosti, můžete použít [Get-MsolDomain](/powershell/module/msonline/get-msoldomain?view=azureadps-1.0) rutiny. 

![Get-MsolDomain](./media/hybrid-azuread-join-manual-steps/01.png)

### <a name="issue-immutableid-for-computer-when-one-for-users-exist-eg-alternate-login-id-is-set"></a>Vydat ImmutableID pro počítač, pokud jeden pro uživatele existuje (například alternativních přihlašovacích údajů, které je nastavené ID)

**`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`** – Tato deklarace identity musí obsahovat platné hodnoty pro počítače. Ve službě AD FS můžete vytvořit pravidel transformace pro vystavování takto:

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

### <a name="helper-script-to-create-the-ad-fs-issuance-transform-rules"></a>Pomocné rutiny skript k vytvoření pravidla transformace vystavení služby AD FS

Následující skript pomáhá při vytváření vystavení transformovat výše popsaná pravidla.

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

- Tento skript připojí pravidla do existujícího pravidla. Skript nespouštěli dvakrát vzhledem k tomu, že sadu pravidel, která by měl být přidán dvakrát. Ujistěte se, že žádná pravidla odpovídající neexistuje pro tyto deklarace identity (v rámci příslušné podmínky) před spuštěním skriptu znovu.

- Pokud máte více názvů ověřenou doménu (jak je vidět na portálu Azure AD nebo pomocí rutiny Get-MsolDomains), nastavte hodnotu **$multipleVerifiedDomainNames** ve skriptu na **$true**. Také se ujistěte, že odeberete všechny existující deklarace identity issuerid, který možná nebyl vytvořen, Azure AD Connect nebo jiným způsobem. Tady je příklad pro toto pravidlo:


        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"]
        => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)",  "http://${domain}/adfs/services/trust/")); 

- Pokud jste už vydali **ImmutableID** deklarací identity pro uživatelské účty, nastavte hodnotu **$immutableIDAlreadyIssuedforUsers** ve skriptu na **$true**.

## <a name="enable-windows-down-level-devices"></a>Povolení zařízením s Windows nižší úrovně

Pokud jsou některé z vašich zařízení připojených k doméně Windows nižší úrovně zařízení, budete muset:

- Nastavte zásady ve službě Azure AD umožňují uživatelům registraci zařízení.
 
- Konfiguraci vaší místní federační služby pro vydávání deklarací identity pro podporu **integrované ověřování Windows (IWA)** pro registraci zařízení.
 
- Přidáte koncový bod ověřování zařízení Azure AD do místní zóny intranetu, aby se zabránilo výzev ohledně certifikátů při ověřování zařízení.

### <a name="set-policy-in-azure-ad-to-enable-users-to-register-devices"></a>Nastavení zásad v Azure AD a umožňuje uživatelům zaregistrovat zařízení

K registraci zařízení s Windows nižší úrovně, budete muset Ujistěte se, že bude uživatelům k registraci zařízení ve službě Azure AD povolit nastavení. Na webu Azure Portal, tato nastavení najdete v části:

`Azure Active Directory > Users and groups > Device settings`
    
Tyto zásady musí být nastaveno na **všechny**: **uživatelé můžou registrovat svoje zařízení s Azure AD**

![Registrace zařízení](./media/hybrid-azuread-join-manual-steps/23.png)


### <a name="configure-on-premises-federation-service"></a>Konfigurovat federační službu na místě 

Místní služby FS, musí podporovat vydání **authenticationmethod** a **wiaormultiauthn** deklarací identity, když přijme požadavek ověření do přijímající strany Azure AD obsahující resouce_params parametr s hodnotou kódovaného jak je znázorněno níže:

    eyJQcm9wZXJ0aWVzIjpbeyJLZXkiOiJhY3IiLCJWYWx1ZSI6IndpYW9ybXVsdGlhdXRobiJ9XX0

    which decoded is {"Properties":[{"Key":"acr","Value":"wiaormultiauthn"}]}

Pokud takový požadavek pochází, místní federační služby musí ověřovat uživatele pomocí integrovaného ověřování Windows a po úspěšném nasazení, vydá následující dvě deklarace identity:

    http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/windows
    http://schemas.microsoft.com/claims/wiaormultiauthn

Ve službě AD FS je nutné přidat pravidel transformace pro vystavování, která předá prostřednictvím metody ověřování.  

**Chcete-li přidat toto pravidlo:**

1. V konzole pro správu služby AD FS přejděte k `AD FS > Trust Relationships > Relying Party Trusts`.
2. Klikněte pravým tlačítkem na objekt vztahu důvěryhodnosti platforma identit Microsoft Office 365 předávající strany a pak vyberte **upravit pravidla deklarací identity**.
3. Na **pravidlech transformace vystavení** kartu, vyberte možnost **přidat pravidlo**.
4. V **pravidlo deklarace identity** seznamu šablon vyberte **odesílat deklarace pomocí vlastního pravidla**.
5. Vyberte **Další**.
6. V **název pravidla deklarace identity** zadejte **pravidlo deklarace identity metoda ověřování**.
7. V **pravidlo deklarace identity** zadejte následující pravidlo:

    `c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"] => issue(claim = c);`

8. Na federačním serveru, zadejte následující příkaz prostředí PowerShell po nahrazení **\<RPObjectName\>** s název přijímající strany objektu pro objekt vztahu důvěryhodnosti Azure AD předávající strany. Obvykle název tohoto objektu **platforma identit Microsoft Office 365**.
   
    `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

### <a name="add-the-azure-ad-device-authentication-end-point-to-the-local-intranet-zones"></a>Přidat koncový bod ověřování zařízení Azure AD do zóny místního intranetu

Aby se zabránilo certifikát výzvy při ověřování uživatelů v registrovat zařízení do služby Azure AD, můžete zásadu odeslat do zařízení připojených k doméně přidat následující adresy URL do zóny místního intranetu v aplikaci Internet Explorer:

`https://device.login.microsoftonline.com`

## <a name="control-deployment-and-rollout"></a>Řízení nasazení a uvedení do provozu

Po dokončení požadovaných kroků zařízení připojených k doméně připraveni automaticky připojí k Azure AD:

- Všech připojených k doméně zařízení se systémem Windows 10 Anniversary Update a Windows serveru 2016 se registrují v Azure AD na zařízení restartovat automaticky nebo přihlášení uživatele. 

- Nové zařízení zaregistrovat v Azure AD při restartování zařízení po dokončení operace připojení k doméně.

- Zaregistrovaná zařízení, které byly dříve Azure AD (například pro Intune) přejít na "*připojený k doméně, zaregistrováno v AAD*"; však bude trvat nějakou dobu pro dokončení napříč všemi zařízeními z důvodu normálního toku domény tohoto procesu a aktivity uživatelů.

### <a name="remarks"></a>Poznámky

- Můžete použít objekt zásad skupiny nebo nastavení ovládejte Automatická registrace Windows 10 a Windows serverem 2016 připojená k doméně počítače klienta System Center Configuration Manager. **Pokud chcete tato zařízení, aby se automaticky zaregistrují do služby Azure AD nebo chcete řídit registraci**, pak musí zavedení zásad skupiny, nejprve zakázat automatické registrace do těchto zařízení, nebo pokud používáte konfiguraci Správce musíte nakonfigurovat klientské nastavení v rámci cloudové služby > automaticky zaregistrovat nová zařízení s Windows 10 připojených k doméně v Azure Active Directory na "Ne", než začnete s žádným z kroků konfigurace. 

> [!Important]
> Protože potenciální zpoždění v aplikaci objekt zásad skupiny na nově přidaní do domény počítače, během kterých může dojít, pokus o automatickou registraci zařízení s Windows 10, musíte vytvořit nové bitové kopie nástroje sysprep ze zařízení s Windows 10, který se nikdy dříve automaticky zaregistrováni a, který již má objekt zásad skupiny a zakázat automatické registrace zařízení s Windows 10 pomocí této bitové kopie nástroje sysprep zřídit nové počítače, které se připojí domény vaší organizace.

Po dokončení konfigurace, a až budete připravení k testování, je nutné zavést zásady skupiny, povolení automatické registrace jenom na testovacích zařízeních a na všech zařízeních, jako je tlačítko.

- K zavedení počítačů Windows nižší úrovně, můžete nasadit [balíček Instalační služby systému Windows](#windows-installer-packages-for-non-windows-10-computers) do počítačů, které jste vybrali.

- Pokud se objekt zásad skupiny, který nahrajete do zařízení Windows 8.1 připojených k doméně, dojde k pokusu o spojení; nicméně doporučujeme použít [balíček Instalační služby systému Windows](#windows-installer-packages-for-non-windows-10-computers) připojit všechna svá zařízení Windows nižší úrovně. 

### <a name="create-a-group-policy-object"></a>Vytvořit objekt zásad skupiny 

Ovládejte aktuální počítačů Windows, měli byste nasadit **zaregistrovat počítače připojené k doméně jako zařízení** objekt zásad skupiny zařízení, kterou chcete zaregistrovat. Například můžete nasadit zásady do organizační jednotky nebo do skupiny zabezpečení.

**Nastavení zásad:**

1. Otevřít **správce serveru**a pak přejděte na `Tools > Group Policy Management`.
2. Přejděte na uzel domény, který odpovídá k doméně, ve které chcete aktivovat automatické registrace Windows aktuálního počítače.
3. Klikněte pravým tlačítkem na **objekty zásad skupiny**a pak vyberte **nový**.
4. Zadejte název objektu zásad skupiny. Například * připojení k hybridní službě Azure AD. 
5. Klikněte na **OK**.
6. Klikněte pravým tlačítkem na nový objekt zásad skupiny a pak vyberte **upravit**.
7. Přejděte na **konfigurace počítače** > **zásady** > **šablony pro správu** > **Windows Součásti** > **registrace zařízení**. 
8. Klikněte pravým tlačítkem na **zaregistrovat počítače připojené k doméně jako zařízení**a pak vyberte **upravit**.
   
   > [!NOTE]
   > Tato šablona zásad skupiny byl přejmenován z dřívějších verzích konzoly pro správu zásad skupiny. Pokud používáte starší verzi konzoly, přejděte na `Computer Configuration > Policies > Administrative Templates > Windows Components > Workplace Join > Automatically workplace join client computers`. 

7. Vyberte **povoleno**a potom klikněte na tlačítko **použít**. Musíte vybrat **zakázané** Pokud chcete, aby zásady pro blokování zařízení řídit pomocí zásad skupiny automaticky registraci v Azure AD.

8. Klikněte na **OK**.
9. Propojte objekt zásad skupiny do umístění podle vašeho výběru. Například můžete propojit k určité organizační jednotce. Můžete také propojit ji na konkrétní skupiny zabezpečení počítačů, které se automaticky připojí k službě Azure AD. Pokud chcete tuto zásadu nastavit u všech připojených k doméně systému Windows 10 a Windows serveru 2016 počítačů ve vaší organizaci, propojte objekt zásad skupiny k doméně.

### <a name="windows-installer-packages-for-non-windows-10-computers"></a>Balíčky Instalační služby systému Windows pro počítače s Windows 10

Připojit se k doméně počítače Windows nižší úrovně ve federovaném prostředí, můžete stáhnout a nainstalovat balíčky Instalační služby systému Windows (MSI) ze služby Stažení softwaru na [Microsoft Workplace Join pro počítače s Windows 10](https://www.microsoft.com/en-us/download/details.aspx?id=53554) stránka.

Balíček můžete nasadit pomocí systém distribuce softwaru jako je System Center Configuration Manager. Balíček podporuje možnosti standardní bezobslužné instalace se *quiet* parametru. Aktuální větev nástroje System Center Configuration Manager nabízí další výhody z dřívějších verzí, jako je schopnost sledování dokončení registrace. Další informace najdete v tématu [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager).

Instalační program vytvoří naplánovanou úlohu v systému, na kterém běží v kontextu uživatele. Úloha se aktivuje, když uživatel přihlásí do Windows. Úloha tiše připojí zařízení s Azure AD s přihlašovacími údaji uživatele po ověření pomocí integrovaného ověřování Windows. Pokud chcete zobrazit naplánované úlohy na zařízení, přejděte na **Microsoft** > **připojení pracovního místa**a potom přejděte ke knihovně plánovače úloh.

## <a name="verify-joined-devices"></a>Ověřit zařízení připojené k doméně

Úspěšná zařízení připojené k doméně ve vaší organizaci můžete zkontrolovat pomocí [Get-MsolDevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice) rutiny v [modul Powershellu pro Azure Active Directory](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

Výstup této rutiny zobrazuje zařízení, která jsou zaregistrované a místa k Azure AD. Chcete-li získat všechna zařízení, použijte **– všechny** parametr a pak je filtrovat pomocí **deviceTrustType** vlastnost. Připojené k doméně zařízení mají hodnotu **připojený k doméně**.



## <a name="troubleshoot-your-implementation"></a>Řešení potíží s vaší implementace

Pokud dochází k problémům s dokončením hybridní připojení ke službě Azure AD pro doménu zařízení připojená k Windows, naleznete v tématu:

- [Řešení potíží s hybridní službě Azure AD join pro aktuální zařízení s Windows](troubleshoot-hybrid-join-windows-current.md)
- [Řešení potíží s připojení k hybridní službě Azure AD pro zařízení s Windows nižší úrovně](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Další postup

* [Úvod do správy zařízení ve službě Azure Active Directory](overview.md)



<!--Image references-->
[1]: ./media/hybrid-azuread-join-manual-steps/12.png
