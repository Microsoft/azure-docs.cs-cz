---
title: Ruční konfigurace hybridních zařízení připojených k Azure Active Directory | Microsoft Docs
description: Zjistěte, jak ručně nakonfigurovat zařízení Azure Active Directory připojená k hybridní.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: markvi
ms.reviewer: sandeo
ms.openlocfilehash: 4c0a9ed77dfceb6ae4ce7affdeff852ba650e7eb
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55093461"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-joined-devices-manually"></a>Kurz: Ruční konfigurace hybridní služby Azure Active Directory připojené zařízení 

Se správou zařízení ve službě Azure Active Directory (Azure AD) můžete zajistit, že uživatelé přistupují ze zařízení, která splňují vaše standardy zabezpečení a dodržování předpisů vašich prostředků. Další informace najdete v tématu [Úvod ke správě zařízení ve službě Azure Active Directory](overview.md).


> [!TIP]
> Pokud pomocí služby Azure AD Connect je pro vás, přečtěte si téma související kurzy pro [spravované](hybrid-azuread-join-managed-domains.md) nebo [federované](hybrid-azuread-join-federated-domains.md) domén. Pomocí služby Azure AD Connect může výrazně zjednodušit konfiguraci připojení k hybridní službě Azure AD.

Pokud máte místní prostředí Active Directory a chcete připojit svá zařízení připojená k doméně k Azure AD, můžete to provést tak, že nakonfigurujete hybridní zařízení připojená k Azure AD. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Ruční konfigurace připojení k hybridní službě Azure AD
> * Konfigurace spojovacího bodu služby
> * Nastavit vystavování deklarací identity
> * Povolení zařízení s Windows nižší úrovně
> * Ověření připojených zařízení
> * Řešení potíží s implementací
 




## <a name="prerequisites"></a>Požadavky

Tento kurz předpokládá, že už znáte:
    
-  [Úvod do správy zařízení v Azure Active Directory](../device-management-introduction.md)    
-  [Naplánování vaší implementace připojení k hybridní službě Azure Active Directory](hybrid-azuread-join-plan.md)
-  [Řízení připojení vašich zařízení k hybridní službě Azure AD](hybrid-azuread-join-control.md)


Než začnete, povolení hybridních zařízení připojených k Azure AD ve vaší organizaci, ujistěte se, že:

- Už běží aktuální verze služby Azure AD Connect.
- Azure AD Connect synchronizaci počítačových objektů zařízení, které chcete k hybridní službě Azure AD připojená k Azure AD. Pokud objekty počítače patří do konkrétní organizační jednotky (OU), je potřeba nakonfigurovat pro synchronizaci ve službě Azure AD Connect i tyto organizační jednotky.

  

Azure AD Connect:

- Udržuje přidružení účtu počítače ve vaší místní instanci Active Directory a objektu zařízení ve službě Azure AD. 
- Povolí další funkce související s zařízení, jako je Windows Hello pro firmy.

Ujistěte se, že je přístupný z počítačů v síti vaší organizace pro registraci počítače do služby Azure AD následující adresy URL:

- https://enterpriseregistration.windows.net
- https://login.microsoftonline.com
- https://device.login.microsoftonline.com
- Vaše organizace službu tokenů zabezpečení (u federovaných domén), které by měl být součástí nastavení místní intranet uživatele

Pokud vaše organizace plánuje pomocí bezproblémového jednotného přihlašování, následující adresa URL musí být dosažitelný z počítače ve vaší organizaci. Také je nutné přidat do zóny místního intranetu uživatele.

- https://autologon.microsoftazuread-sso.com

Kromě toho musí být v zóny intranet uživatele povolené následující nastavení: "Povolit aktualizace stavového řádku pomocí skriptu."

Pokud vaše organizace používá spravované instalace (jiné než federované) s místní služby Active Directory a Active Directory Federation Services (AD FS), abyste neklikli provést federaci se službou Azure AD a potom připojení k hybridní službě Azure AD ve Windows 10 spoléhá na počítačových objektů ve aktivní Adresář se synchronizují do Azure AD. Ujistěte se, že všechny organizační jednotky, které obsahují počítače, které objekty, které musí být připojená k hybridní Azure AD jsou povoleny pro synchronizaci v konfiguraci synchronizace Azure AD Connect.

Pro zařízení s Windows 10 ve verzi 1703 nebo starší Pokud vaše organizace potřebuje přístup k Internetu prostřednictvím odchozího proxy serveru, musíte implementovat Proxy Auto-Discovery WPAD (Web) umožňující počítače s Windows 10 k registraci do služby Azure AD. 

Od verze Windows 10 1803 i v případě neúspěšného pokusu o hybridní službě Azure AD join zařízení do federované domény prostřednictvím služby AD FS a Azure AD Connect je nakonfigurovaná pro synchronizaci objektů počítačů a zařízení do služby Azure AD, zařízení se pokusí provést připojení k hybridní službě Azure AD od nás zpřístupnění synchronizovaných počítačů a zařízení.

## <a name="verify-configuration-steps"></a>Prověřte kroky konfigurace

Hybridní zařízení připojená k Azure AD můžete nakonfigurovat pro různé typy platforem zařízení s Windows. Toto téma obsahuje požadované kroky pro všechny obvyklé scénáře konfigurace.  

V následující tabulce najdete přehled požadovaných kroků pro váš scénář:  



| Kroky                                      | Aktuální Windows a synchronizace hodnoty hash hesel | Aktuální Windows a federace | Windows nižší úrovně |
| :--                                        | :-:                                    | :-:                            | :-:                |
| Konfigurace spojovacího bodu služby | ![Zaškrtnout][1]                            | ![Zaškrtnout][1]                    | ![Zaškrtnout][1]        |
| Nastavit vystavování deklarací identity           |                                        | ![Zaškrtnout][1]                    | ![Zaškrtnout][1]        |
| Povolení zařízení bez Windows 10      |                                        |                                | ![Zaškrtnout][1]        |
| Ověření připojených zařízení          | ![Zaškrtnout][1]                            | ![Zaškrtnout][1]                    | ![Zaškrtnout][1]        |



## <a name="configure-a-service-connection-point"></a>Konfigurace spojovacího bodu služby

Vaše zařízení pomocí objektu Spojovacího bodu připojení služby během registrace zjistit informace o tenantovi Azure AD. V místní instanci Active Directory musí existovat objekt spojovací bod služby pro hybridních zařízení připojených k Azure AD v konfiguraci názvové kontextu počítače doménové struktury. Pro každou doménovou strukturu existuje pouze jeden názvový kontext konfigurace. V konfiguraci s více doménovými strukturami služby Active Directory musí existovat spojovací bod služby ve všech doménových struktur, které obsahují počítače připojené k doméně.

Pomocí rutiny [**Get-ADRootDSE**](https://technet.microsoft.com/library/ee617246.aspx) můžete načíst názvový kontext konfigurace vaší doménové struktury.  

Pro doménovou strukturu s názvem domény Active Directory *fabrikam.com* je názvový kontext konfigurace následující:

`CN=Configuration,DC=fabrikam,DC=com`

Ve vaší doménové struktuře se objekt spojovacího bodu služby pro automatickou registraci zařízení připojených k doméně nachází v tomto umístění:  

`CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,[Your Configuration Naming Context]`

V závislosti na tom, jak nasadíte Azure AD Connect objektu spojovacího bodu služby může mít už nakonfigurovaná.
Můžete ověřit existenci objektu a načíst hodnoty zjišťování s použitím následujícího skriptu prostředí Windows PowerShell: 

    $scp = New-Object System.DirectoryServices.DirectoryEntry;

    $scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=fabrikam,DC=com";

    $scp.Keywords;

**$Scp. Klíčová slova** výstup zobrazuje informace o tenantovi Azure AD. Tady je příklad:

    azureADName:microsoft.com
    azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47

Pokud spojovací bod služby neexistuje, můžete ho vytvořit spuštěním rutiny `Initialize-ADSyncDomainJoinedComputerSync` na vašem serveru Azure AD Connect. Ke spuštění této rutiny jsou nutné přihlašovací údaje podnikového správce.  

Tato rutina:

- Spojovací bod služby se vytvoří v doménové struktuře služby Active Directory, připojený k Azure AD Connect. 
- Vyžaduje, abyste zadali parametr `AdConnectorAccount`. Tento účet je nakonfigurovaný jako účet konektoru služby Active Directory ve službě Azure AD Connect. 


Následující skript ukazuje příklad použití této rutiny. V tomto skriptu příkaz `$aadAdminCred = Get-Credential` vyžaduje, abyste zadali uživatelské jméno. Musíte zadat uživatelské jméno ve formátu hlavního názvu uživatele (UPN) (`user@example.com`). 


    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;

Rutina `Initialize-ADSyncDomainJoinedComputerSync`:

- Používá modul Active Directory PowerShell a nástroje Azure Active Directory Domain Services (Azure AD DS). Tyto nástroje spoléhají na Active Directory Web Services běží na řadiči domény. Služba Active Directory Web Services se podporuje v řadičích domény s Windows Serverem 2008 R2 nebo novějším.
- Je podporován pouze pomocí prostředí PowerShell verze 1.1.166.0 modulu MSOnline. Chcete-li stáhnout tento modul, použijte [tento odkaz](https://msconfiggallery.cloudapp.net/packages/MSOnline/1.1.166.0/).   
- Pokud nejsou nainstalovány nástroje Azure AD DS, `Initialize-ADSyncDomainJoinedComputerSync` se nezdaří. Můžete nainstalovat nástroje Azure AD DS přes správce serveru pod **funkce** > **nástroje pro vzdálenou správu serveru** > **nástroje pro správu rolí** .

Pro řadiče domény se systémem Windows Server 2008 nebo dřívější verze pomocí následujícího skriptu vytvořte spojovací bod služby. V konfiguraci s více doménovými strukturami pomocí následujícího skriptu vytvořte spojovací bod služby v každé doménové struktuře, pokud existují počítače.
 
    $verifiedDomain = "contoso.com"    # Replace this with any of your verified domain names in Azure AD
    $tenantID = "72f988bf-86f1-41af-91ab-2d7cd011db47"    # Replace this with you tenant ID
    $configNC = "CN=Configuration,DC=corp,DC=contoso,DC=com"    # Replace this with your Active Directory configuration naming context

    $de = New-Object System.DirectoryServices.DirectoryEntry
    $de.Path = "LDAP://CN=Services," + $configNC
    $deDRC = $de.Children.Add("CN=Device Registration Configuration", "container")
    $deDRC.CommitChanges()

    $deSCP = $deDRC.Children.Add("CN=62a0ff2e-97b9-4513-943f-0d221bd30080", "serviceConnectionPoint")
    $deSCP.Properties["keywords"].Add("azureADName:" + $verifiedDomain)
    $deSCP.Properties["keywords"].Add("azureADId:" + $tenantID)

    $deSCP.CommitChanges()

V předchozím skriptu `$verifiedDomain = "contoso.com"` je zástupný symbol. Nahraďte jedním názvů ověřených domén ve službě Azure AD. Budete muset tuto doménu vlastnit, než budete moct použít.

Další informace o názvech ověřenou doménu, najdete v části [přidání názvu vlastní domény do Azure Active Directory](../active-directory-domains-add-azure-portal.md). 

Pokud chcete zobrazit seznam ověřených domén vaší společnosti, můžete použít rutinu [Get-AzureADDomain](/powershell/module/Azuread/Get-AzureADDomain?view=azureadps-2.0). 

![Seznam domén společnosti](./media/hybrid-azuread-join-manual/01.png)

## <a name="set-up-issuance-of-claims"></a>Nastavit vystavování deklarací identity

V federované Azure AD konfigurace zařízení využívají služby AD FS nebo federační službou místní od partnera Microsoftu k ověření do služby Azure AD. Zařízení se ověřují za účelem získání přístupového tokenu, pomocí kterého se můžou zaregistrovat ve službě Azure Active Directory Device Registration (Azure DRS).

Aktuální zařízení Windows ověřovat pomocí integrovaného ověřování Windows na aktivní koncový bod WS-Trust (verze 1.3 nebo 2005) hostuje místní federační služby.

> [!NOTE]
> Když používáte službu AD FS, buď **adfs/services/vztah důvěryhodnosti/13/windowstransport** nebo **adfs/services/vztah důvěryhodnosti/2005/windowstransport** musí být povolené. Pokud používáte webový proxy server ověřování, zkontrolujte také, že je tento koncový bod publikované prostřednictvím proxy serveru. Uvidíte, jaké koncové body jsou povolené prostřednictvím konzoly pro správu služby AD FS v části **služby** > **koncové body**.
>
>Pokud nemáte jako místní federační služby AD FS, postupujte podle pokynů od dodavatele, abyste měli jistotu, že podporují WS-Trust 1.3 nebo 2005 koncových bodů a tyto publikují v souboru metadat systému Exchange (MEX).

Pro dokončení registrace zařízení musí existovat následující deklarace identity v tokenu, který přijímá DRS pro Azure. Služba DRS Azure vytvoří objekt zařízení ve službě Azure AD pomocí některé z těchto informací. Azure AD Connect pomocí těchto informací k přidružení počítače účtu místní zařízení nově vytvořený objekt.

* `http://schemas.microsoft.com/ws/2012/01/accounttype`
* `http://schemas.microsoft.com/identity/claims/onpremobjectguid`
* `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`

Pokud máte více než jeden ověřený název domény, musíte počítačům poskytnout následující deklaraci identity:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid`

Pokud již vystavujete deklaraci identity ImmutableID (například alternativního přihlašovacího ID), budete muset zadat jeden odpovídající deklarace identity pro počítače:

* `http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`

V následujících částech najdete informace o těchto tématech:
 
- Hodnoty, které by měly mít každou deklaraci.
- Co definici by vypadalo podobně jako ve službě AD FS.

Definice vám pomůže ověřit, jestli požadované hodnoty existují, nebo jestli je potřeba je vytvořit.

> [!NOTE]
> Pokud jako svůj místní federační server nepoužíváte AD FS, postupujte podle pokynů vašeho dodavatele a vytvořte odpovídající konfiguraci pro vystavování těchto deklarací identity.

### <a name="issue-account-type-claim"></a>Vystavení deklarace identity typu účtu

`http://schemas.microsoft.com/ws/2012/01/accounttype` Deklarace identity musí obsahovat hodnotu **DJ**, který identifikuje zařízení jako počítače připojené k doméně. Ve službě AD FS můžete přidat pravidlo transformace vystavování, které vypadá přibližně takto:

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

`http://schemas.microsoft.com/identity/claims/onpremobjectguid` Musí obsahovat deklarace identity **objectGUID** hodnotu účtu v místním počítači. Ve službě AD FS můžete přidat pravidlo transformace vystavování, které vypadá přibližně takto:

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

`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid` Musí obsahovat deklarace identity **objectSid** hodnotu účtu v místním počítači. Ve službě AD FS můžete přidat pravidlo transformace vystavování, které vypadá přibližně takto:

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

### <a name="issue-issuerid-for-the-computer-when-multiple-verified-domain-names-are-in-azure-ad"></a>Vydat issuerID pro počítač po několik názvů ověřené domény ve službě Azure AD

`http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid` Deklarace identity musí obsahovat identifikátor URI (Uniform Resource) u všech názvy ověřenou doménu, která se připojují ke službě federation místní (služby AD FS nebo partnera) vydání tokenu. Ve službě AD FS můžete přidat pravidla transformace vystavování, které vypadají podobně jako následující dotazy v tomto konkrétním pořadí po ta předchozí. Všimněte si, že jedno pravidlo explicitně vydat pravidlo pro uživatele je nezbytné. V následující pravidla je přidat první pravidlo, které identifikuje uživatele a ověření počítače.

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


V předchozí deklaraci identity `<verified-domain-name>` je zástupný symbol. Nahraďte jedním názvů ověřených domén ve službě Azure AD. Například použít `Value = "http://contoso.com/adfs/services/trust/"`.



Další informace o názvech ověřenou doménu, najdete v části [přidání názvu vlastní domény do Azure Active Directory](../active-directory-domains-add-azure-portal.md).  

Pokud chcete zobrazit seznam ověřených domén vaší společnosti, můžete použít rutinu [Get-MsolDomain](/powershell/module/msonline/get-msoldomain?view=azureadps-1.0). 

![Seznam domén společnosti](./media/hybrid-azuread-join-manual/01.png)

### <a name="issue-immutableid-for-the-computer-when-one-for-users-exists-for-example-an-alternate-login-id-is-set"></a>Vydat ImmutableID pro počítače, jeden pro uživatele existuje (například alternativních přihlašovacích údajů, které je nastavené ID)

`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID` Deklarace identity musí obsahovat platné hodnoty pro počítače. Ve službě AD FS můžete vytvořit následující pravidlo transformace vystavování:

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

Následující skript pomáhá při vytváření vystavení transformovat pravidel popsaných výše.

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

#### <a name="remarks"></a>Poznámky 

- Tento skript připojí pravidla k existujícím pravidlům. Skript nespouštěli, dvakrát, protože sadu pravidel, která by měl být přidán dvakrát. Před opětovným spuštěním skriptu se ujistěte, že pro tyto deklarace identity neexistují žádná odpovídající pravidla (za odpovídajících podmínek).

- Pokud máte více názvů ověřenou doménu (jak je znázorněno na portálu Azure AD nebo prostřednictvím **Get-MsolDomain** rutiny), nastavte hodnotu **$multipleVerifiedDomainNames** ve skriptu na **$true** . Také se ujistěte, že odeberete všechny existující **issuerid** požadavek, aby mohla být vytvořena pomocí služby Azure AD Connect nebo jiným způsobem. Tady je příklad pro toto pravidlo:

      c:[Type == "http://schemas.xmlsoap.org/claims/UPN"]
      => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)",  "http://${domain}/adfs/services/trust/")); 

- Pokud jste již pro uživatelské účty vystavili deklaraci identity **ImmutableID** nastavte hodnotu proměnné **$immutableIDAlreadyIssuedforUsers** ve skriptu na **$true**.

## <a name="enable-windows-down-level-devices"></a>Povolení zařízení s Windows nižší úrovně

Pokud jsou některá z vašich zařízení připojených k doméně zařízení s Windows nižší úrovně, je potřeba provést následující:

- Nastavit v Azure AD zásadu, která uživatelům umožní registrovat zařízení. 
- Konfiguraci vaší místní federační služby pro vydávání deklarací identity pro podporu integrované ověřování Windows (IWA) pro registraci zařízení. 
- Koncový bod ověřování zařízení Azure AD přidejte do zóny místního intranetu se vyhnout výzev ohledně certifikátů při ověřování zařízení.
- Řízení zařízení Windows nižší úrovně. 


### <a name="set-a-policy-in-azure-ad-to-enable-users-to-register-devices"></a>Nastavit zásady ve službě Azure AD umožňují uživatelům registraci zařízení

K registraci zařízení s Windows nižší úrovně, ujistěte se, že je povoleno nastavení umožňují uživatelům registraci zařízení ve službě Azure AD. Na webu Azure Portal, můžete toto nastavení v části najdete **Azure Active Directory** > **uživatelů a skupin** > **nastavení zařízení**.
    
Tyto zásady musí být nastaveno na **všechny**: **Uživatelé můžou registrovat svoje zařízení s Azure AD**.

![Všechna tlačítka, které umožňuje uživatelům k registraci zařízení](./media/hybrid-azuread-join-manual/23.png)


### <a name="configure-the-on-premises-federation-service"></a>Konfigurovat federační službu na místě 

Místní služby FS, musí podporovat vydání **authenticationmethod** a **wiaormultiauthn** deklarací identity, když přijme požadavek na ověření předávající straně služby Azure AD obsahující resource_params parametru s následující hodnotou kódovaného:

    eyJQcm9wZXJ0aWVzIjpbeyJLZXkiOiJhY3IiLCJWYWx1ZSI6IndpYW9ybXVsdGlhdXRobiJ9XX0

    which decoded is {"Properties":[{"Key":"acr","Value":"wiaormultiauthn"}]}

Pokud takový požadavek pochází, musíte místní federační služby ověření uživatele pomocí integrovaného ověřování Windows. Když je ověření úspěšné, federační služby musí vydat následující dvě deklarace identity:

    http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/windows
    http://schemas.microsoft.com/claims/wiaormultiauthn

Ve službě AD FS je nutné přidat pravidel transformace pro vystavování, který prochází metodu ověřování. Chcete-li přidat toto pravidlo:

1. V konzole pro správu služby AD FS přejděte k **služby AD FS** > **vztahy důvěryhodnosti** > **vztahy důvěryhodnosti předávající strany**.
2. Klikněte pravým tlačítkem na objekt důvěryhodnosti přijímající strany služby Microsoft Office 365 Identity Platform a pak vyberte **Upravit pravidla deklarace identity**.
3. Na kartě **Pravidla transformace vystavování** vyberte **Přidat pravidlo**.
4. V seznamu šablon **Pravidlo deklarace identity** vyberte **Odesílat deklarace pomocí vlastního pravidla**.
5. Vyberte **Další**.
6. V **název pravidla deklarace identity** zadejte **pravidlo deklarace identity metoda ověřování**.
7. V **pravidlo deklarace identity** zadejte následující pravidlo:

    `c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"] => issue(claim = c);`

8. Na federačním serveru zadejte následující příkaz Powershellu. Nahraďte **\<RPObjectName\>** s název přijímající strany objektu pro objekt vztahu důvěryhodnosti Azure AD předávající strany. Tento objekt má obvykle název **Microsoft Office 365 Identity Platform**.
   
    `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

### <a name="add-the-azure-ad-device-authentication-endpoint-to-the-local-intranet-zones"></a>Přidat koncový bod ověřování zařízení Azure AD do zóny místního intranetu

Aby se zabránilo výzev ohledně certifikátů při ověřování uživatele registrovaná zařízení do služby Azure AD, můžete odeslat zásady do zařízení připojených k doméně přidat následující adresy URL do zóny místního intranetu v aplikaci Internet Explorer:

`https://device.login.microsoftonline.com`


### <a name="control-windows-down-level-devices"></a>Řízení zařízení s Windows nižší úrovně 

Pokud chcete registrovat zařízení s Windows nižší úrovně, musíte si z webu Download Center stáhnout a nainstalovat balíček Instalační služby systému Windows (.msi). Další informace najdete v tématu [řízení připojení k hybridní službě Azure AD. zařízení](hybrid-azuread-join-control.md#control-windows-down-level-devices). 



## <a name="verify-joined-devices"></a>Ověření připojených zařízení

Můžete vyhledat úspěšně připojil zařízení ve vaší organizaci pomocí [Get-MsolDevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice) rutiny v [modul Powershellu pro Azure Active Directory](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

Výstup této rutiny ukazuje zařízení zaregistrovaná a připojená k Azure AD. Chcete-li získat všechna zařízení, použijte **– všechny** parametr a filtrovat je pomocí **deviceTrustType** vlastnost. Zařízení připojená k doméně mají hodnotu **připojený k doméně**.



## <a name="troubleshoot-your-implementation"></a>Řešení potíží s implementací

Pokud je máte problémy s dokončením připojení k hybridní službě Azure AD pro zařízení s Windows připojených k doméně, přečtěte si:

- [Řešení potíží s hybridním připojením aktuálních zařízení s Windows k Azure AD](troubleshoot-hybrid-join-windows-current.md)
- [Řešení potíží s hybridním připojením zařízení s Windows nižší úrovně k Azure AD](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Další postup

* [Úvod do správy zařízení v Azure Active Directory](overview.md)



<!--Image references-->
[1]: ./media/hybrid-azuread-join-manual/12.png
