---
title: Ruční konfigurace hybridních zařízení připojených k Azure Active Directory | Microsoft Docs
description: Přečtěte si, jak ručně nakonfigurovat hybridní zařízení připojená ke službě Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6754393bdeabcd67dcf6514102e3c825a26fc3e9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239081"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-joined-devices-manually"></a>Kurz: Ruční konfigurace hybridních zařízení připojených k Azure Active Directory

Díky správě zařízení ve službě Azure Active Directory (Azure AD) můžete zajistit, aby uživatelé přistupovali k vašim prostředkům ze zařízení, která splňují vaše standardy zabezpečení a dodržování předpisů. Další informace najdete [v tématu Úvod ke správě zařízení ve službě Azure Active Directory](overview.md).

> [!TIP]
> Pokud je pro vás možnost používat Azure AD Connect, přečtěte si související kurzy pro [spravované](hybrid-azuread-join-managed-domains.md) nebo [federované](hybrid-azuread-join-federated-domains.md) domény. Pomocí Azure AD Connect můžete výrazně zjednodušit konfiguraci hybridního připojení Azure AD.

Pokud máte místní prostředí Active Directory a chcete připojit svá zařízení připojená k doméně k Azure AD, můžete to provést tak, že nakonfigurujete hybridní zařízení připojená k Azure AD. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Ruční konfigurace hybridního připojení Azure AD
> * Konfigurace spojovacího bodu služby
> * Nastavit vystavení pohledávek
> * Povolení zařízení s Windows nižší úrovně
> * Ověření připojených zařízení
> * Řešení potíží s implementací

## <a name="prerequisites"></a>Požadavky

Tento kurz předpokládá, že jste obeznámeni s:

* [Úvod do správy zařízení v Azure Active Directory](../device-management-introduction.md)
* [Plánování implementace hybridního připojení k azure active directory](hybrid-azuread-join-plan.md)
* [Řízení připojení vašich zařízení k hybridní službě Azure AD](hybrid-azuread-join-control.md)

Než začnete povolovat hybridní zařízení Azure AD připojená ve vaší organizaci, ujistěte se, že:

* Používáte aktuální verzi Služby Azure AD Connect.
* Azure AD Connect synchronizovala objekty počítače zařízení, která chcete být hybridní Azure AD připojen k Azure AD. Pokud objekty počítače patří do konkrétníorganizační jednotky (ORGANIZAČNÍ JEDNOTKY), tyto organizační jednotky musí být nakonfigurovánpro synchronizaci v Azure AD Connect také.

Azure AD Connect:

* Udržuje přidružení mezi účtem počítače v místní instanci služby Active Directory a objektem zařízení ve službě Azure AD.
* Povolí další funkce související se zařízením, jako je Windows Hello pro firmy.

Ujistěte se, že následující adresy URL jsou přístupné z počítačů v síti vaší organizace pro registraci počítačů do Azure AD:

* `https://enterpriseregistration.windows.net`
* `https://login.microsoftonline.com`
* `https://device.login.microsoftonline.com`
* StS vaší organizace (pro federované domény), které by měly být zahrnuty do nastavení místního intranetu uživatele

Pokud vaše organizace plánuje používat bezproblémové jednotné přihlašování, musí být následující adresa URL dostupná z počítačů ve vaší organizaci. Musí být také přidán do místní intranetové zóny uživatele.

* `https://autologon.microsoftazuread-sso.com`

Kromě toho by v zóně intranetu uživatele mělo být povolené následující nastavení: Povolit aktualizace stavového řádku přes skript.

Pokud vaše organizace používá spravované (nefederované) nastavení s místní službou Active Directory a nepoužívá službu AD FS (Active) služby Active (Active) služby Active. Adresář, který se má synchronizovat se službou Azure AD. Ujistěte se, že všechny hlavní uživatele, které obsahují objekty počítače, které musí být hybridní Azure AD připojeny jsou povoleny pro synchronizaci v konfiguraci synchronizace Azure AD Connect.

Pokud vaše organizace vyžaduje přístup k Internetu prostřednictvím odchozího proxy serveru, je nutné implementovat automatické zjišťování webového proxy serveru (WPAD), aby se počítače s Windows 10 mohly zaregistrovat do Azure AD, pokud vaše organizace vyžaduje přístup k internetu prostřednictvím odchozího proxy serveru, musíte implementovat automatické zjišťování webového proxy serveru (WPAD), aby se počítače s Windows 10 mohly zaregistrovat do Azure AD.

Počínaje Windows 10 1803, i v případě, že hybridní Připojení Azure AD připojit zařízení ve federované doméně prostřednictvím služby AD FS selže a pokud Azure AD Connect je nakonfigurovaný pro synchronizaci objektů počítače nebo zařízení azure ad, zařízení se pokusí dokončit hybridní připojení Azure AD pomocí synchronizovaného počítače nebo zařízení.

Chcete-li ověřit, zda je zařízení schopno přistupovat k výše uvedeným prostředkům společnosti Microsoft v rámci systémového účtu, můžete použít skript [Test Device Registration Connectivity.](https://gallery.technet.microsoft.com/Test-Device-Registration-3dc944c0)

## <a name="verify-configuration-steps"></a>Ověření kroků konfigurace

Hybridní zařízení připojená k Azure AD můžete nakonfigurovat pro různé typy platforem zařízení s Windows. Toto téma obsahuje požadované kroky pro všechny obvyklé scénáře konfigurace.  

V následující tabulce najdete přehled požadovaných kroků pro váš scénář:  

| Kroky | Aktuální Windows a synchronizace hodnoty hash hesel | Aktuální Windows a federace | Windows nižší úrovně |
| :--- | :---: | :---: | :---: |
| Konfigurace spojovacího bodu služby | ![Zaškrtnout][1] | ![Zaškrtnout][1] | ![Zaškrtnout][1] |
| Nastavit vystavení pohledávek |     | ![Zaškrtnout][1] | ![Zaškrtnout][1] |
| Povolení zařízení bez Windows 10 |       |        | ![Zaškrtnout][1] |
| Ověření připojených zařízení | ![Zaškrtnout][1] | ![Zaškrtnout][1] | [Zkontrolujte][1] |

## <a name="configure-a-service-connection-point"></a>Konfigurace spojovacího bodu služby

Vaše zařízení používají objekt spojovacího bodu služby (SCP) během registrace ke zjištění informací o klientovi Azure AD. V místní instanci služby Active Directory musí existovat objekt SCP pro hybridní zařízení připojená k azure ad v oddílu kontextu pojmenování konfigurace doménové struktury počítače. Pro každou doménovou strukturu existuje pouze jeden názvový kontext konfigurace. V konfiguraci služby Active Directory s více doménovými strukturami musí spojovací bod služby existovat ve všech doménových strukturách, které obsahují počítače spojené s doménou.

Pomocí rutiny [**Get-ADRootDSE**](https://technet.microsoft.com/library/ee617246.aspx) můžete načíst názvový kontext konfigurace vaší doménové struktury.  

Pro doménovou strukturu s názvem domény Active Directory *fabrikam.com* je názvový kontext konfigurace následující:

`CN=Configuration,DC=fabrikam,DC=com`

Ve vaší doménové struktuře se objekt spojovacího bodu služby pro automatickou registraci zařízení připojených k doméně nachází v tomto umístění:  

`CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,[Your Configuration Naming Context]`

V závislosti na tom, jak jste nasadili Azure AD Connect, objekt SCP už možná byl nakonfigurovaný.
Můžete ověřit existenci objektu a načíst hodnoty zjišťování pomocí následujícího skriptu prostředí Windows PowerShell:

   ```PowerShell
   $scp = New-Object System.DirectoryServices.DirectoryEntry;

   $scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=fabrikam,DC=com";

   $scp.Keywords;
   ```

Ten **$scp. Výstup klíčových slov** zobrazuje informace o tenantovi Azure AD. Tady je příklad:

   ```
   azureADName:microsoft.com
   azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47
   ```

Pokud spojovací bod služby neexistuje, můžete ho vytvořit spuštěním rutiny `Initialize-ADSyncDomainJoinedComputerSync` na vašem serveru Azure AD Connect. Ke spuštění této rutiny jsou vyžadována pověření pro správu rozlehlé sítě.  

Tato rutina:

* Vytvoří bod připojení služby v doménové struktuře služby Active Directory, ke které je azure ad připojení připojeno.
* Vyžaduje, abyste zadali parametr `AdConnectorAccount`. Tento účet je nakonfigurovaný jako účet konektoru služby Active Directory ve službě Azure AD Connect.


Následující skript ukazuje příklad použití této rutiny. V tomto skriptu příkaz `$aadAdminCred = Get-Credential` vyžaduje, abyste zadali uživatelské jméno. Musíte zadat uživatelské jméno ve formátu hlavního názvu uživatele (UPN) (`user@example.com`).

   ```PowerShell
   Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

   $aadAdminCred = Get-Credential;

   Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;
   ```

Rutina `Initialize-ADSyncDomainJoinedComputerSync`:

* Používá modul Active Directory PowerShell a nástroje Azure Active Directory Domain Services (Azure AD DS). Tyto nástroje spoléhají na službu Active Directory Web Services spuštěnou na řadiči domény. Služba Active Directory Web Services se podporuje v řadičích domény s Windows Serverem 2008 R2 nebo novějším.
* Se podporuje pouze v modulu MSOnline PowerShell verze 1.1.166.0. Chcete-li stáhnout tento modul, použijte [tento odkaz](https://msconfiggallery.cloudapp.net/packages/MSOnline/1.1.166.0/).
* Pokud nejsou nainstalovány nástroje ad `Initialize-ADSyncDomainJoinedComputerSync` DS, nezdaří se. Nástroje služby AD DS můžete nainstalovat prostřednictvím Správce serveru v části **Nástroje** > **pro správu rolí**vzdálené správy > **serveru**.

U řadičů domény se systémem Windows Server 2008 nebo starších verzí vytvořte spojovací bod služby pomocí následujícího skriptu. V konfiguraci s více doménovými strukturami vytvořte pomocí následujícího skriptu bod připojení služby v každé doménové struktuře, kde existují počítače.

   ```PowerShell
   $verifiedDomain = "contoso.com" # Replace this with any of your verified domain names in Azure AD
   $tenantID = "72f988bf-86f1-41af-91ab-2d7cd011db47" # Replace this with you tenant ID
   $configNC = "CN=Configuration,DC=corp,DC=contoso,DC=com" # Replace this with your Active Directory configuration naming context

   $de = New-Object System.DirectoryServices.DirectoryEntry
   $de.Path = "LDAP://CN=Services," + $configNC
   $deDRC = $de.Children.Add("CN=Device Registration Configuration", "container")
   $deDRC.CommitChanges()

   $deSCP = $deDRC.Children.Add("CN=62a0ff2e-97b9-4513-943f-0d221bd30080", "serviceConnectionPoint")
   $deSCP.Properties["keywords"].Add("azureADName:" + $verifiedDomain)
   $deSCP.Properties["keywords"].Add("azureADId:" + $tenantID)

   $deSCP.CommitChanges()
   ```

V předchozím skriptu `$verifiedDomain = "contoso.com"` je zástupný symbol. Nahraďte ho jedním z ověřených názvů domén ve službě Azure AD. Před použitím musíte doménu vlastnit.

Další informace o ověřených názvech domén najdete [v tématu Přidání vlastního názvu domény do služby Azure Active Directory](../active-directory-domains-add-azure-portal.md).

Pokud chcete zobrazit seznam ověřených domén vaší společnosti, můžete použít rutinu [Get-AzureADDomain](/powershell/module/Azuread/Get-AzureADDomain?view=azureadps-2.0).

![Seznam domén společnosti](./media/hybrid-azuread-join-manual/01.png)

## <a name="set-up-issuance-of-claims"></a>Nastavit vystavení pohledávek

Ve federované konfiguraci Azure AD zařízení spoléhají na službu AD FS nebo místní federační službu od partnera Microsoftu k ověření ve službě Azure AD. Zařízení se ověřují za účelem získání přístupového tokenu, pomocí kterého se můžou zaregistrovat ve službě Azure Active Directory Device Registration (Azure DRS).

Aktuální zařízení systému Windows se ověřují pomocí integrovaného ověřování systému Windows v aktivním koncovém bodu WS-Trust (verze 1.3 nebo 2005) hostovaném místní federační službou.

Pokud používáte službu AD FS, musíte povolit následující koncové body WS-Trust
- `/adfs/services/trust/2005/windowstransport`
- `/adfs/services/trust/13/windowstransport`
- `/adfs/services/trust/2005/usernamemixed`
- `/adfs/services/trust/13/usernamemixed`
- `/adfs/services/trust/2005/certificatemixed`
- `/adfs/services/trust/13/certificatemixed`

> [!WARNING]
> Oba **adfs/services/trust/2005/windowstransport** nebo **adfs/services/trust/13/windowstransport** by měly být povoleny jako intranet směřující pouze koncové body a nesmí být vystaveny jako koncové body směřující k extranetu prostřednictvím proxy webové aplikace. Další informace o zakázání koncových bodů systému WS-Trust systému Windows naleznete [v tématu Zakázání koncových bodů systému WS-Trust systému Windows na serveru proxy](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet). Můžete vidět, jaké koncové body jsou povoleny prostřednictvím konzoly pro správu služby AD FS v části Koncové body **služby** > **Endpoints**.

> [!NOTE]
>Pokud nemáte službu AD FS jako místní federační službu, postupujte podle pokynů od dodavatele a ujistěte se, že podporují koncové body WS-Trust 1.3 nebo 2005 a že jsou publikovány prostřednictvím souboru Serveru metadat (MEX).

Pro dokončení registrace zařízení musí existovat následující deklarace identity v tokenu, který přijímá Azure DRS. Azure DRS vytvoří objekt zařízení ve službě Azure AD s některými z těchto informací. Azure AD Connect pak používá tyto informace k přidružení nově vytvořeného objektu zařízení k místnímu účtu počítače.

* `http://schemas.microsoft.com/ws/2012/01/accounttype`
* `http://schemas.microsoft.com/identity/claims/onpremobjectguid`
* `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`

Pokud máte více než jeden ověřený název domény, musíte počítačům poskytnout následující deklaraci identity:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid`

Pokud již vydáváte deklaraci IMAID (například alternativní přihlašovací ID), musíte pro počítače zadat jednu odpovídající deklaraci:

* `http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`

V následujících částech najdete informace o těchto tématech:

* Hodnoty, které by měly mít každé tvrzení.
* Jak by definice vypadala ve sf.

Definice vám pomůže ověřit, jestli požadované hodnoty existují, nebo jestli je potřeba je vytvořit.

> [!NOTE]
> Pokud jako svůj místní federační server nepoužíváte AD FS, postupujte podle pokynů vašeho dodavatele a vytvořte odpovídající konfiguraci pro vystavování těchto deklarací identity.

### <a name="issue-account-type-claim"></a>Vystavení deklarace identity typu účtu

Deklarace `http://schemas.microsoft.com/ws/2012/01/accounttype` musí obsahovat hodnotu **DJ**, která identifikuje zařízení jako počítač spojený s doménou. Ve službě AD FS můžete přidat pravidlo transformace vystavování, které vypadá přibližně takto:

   ```
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
   ```

### <a name="issue-objectguid-of-the-computer-account-on-premises"></a>Vystavení identity objectGUID místního účtu počítače

Deklarace `http://schemas.microsoft.com/identity/claims/onpremobjectguid` musí obsahovat hodnotu **objectGUID** místního účtu počítače. Ve službě AD FS můžete přidat pravidlo transformace vystavování, které vypadá přibližně takto:

   ```
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
   ```

### <a name="issue-objectsid-of-the-computer-account-on-premises"></a>Vystavení identity objectSID místního účtu počítače

Deklarace `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid` musí obsahovat hodnotu **objectSid** účtu místního počítače. Ve službě AD FS můžete přidat pravidlo transformace vystavování, které vypadá přibližně takto:

   ```
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
   ```

### <a name="issue-issuerid-for-the-computer-when-multiple-verified-domain-names-are-in-azure-ad"></a>Problém issuerID pro počítač, pokud více ověřených názvů domén jsou ve službě Azure AD

Deklarace `http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid` musí obsahovat identifikátor URI (Uniform Resource Identifier) některého z ověřených názvů domén, které se připojují k místní federační službě (AD FS nebo partnerovi) vydávající token. Ve správě AD FS můžete přidat pravidla transformace vystavování, která vypadají jako následující pravidla v tomto konkrétním pořadí za předchozí. Všimněte si, že jedno pravidlo explicitně vydat pravidlo pro uživatele je nezbytné. V následujících pravidlech je přidáno první pravidlo, které identifikuje ověřování uživatelů a počítačů.

   ```
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
   ```

V předchozí deklaraci `<verified-domain-name>` je zástupný symbol. Nahraďte ho jedním z ověřených názvů domén ve službě Azure AD. Použijte například `Value = "http://contoso.com/adfs/services/trust/"`.

Další informace o ověřených názvech domén najdete [v tématu Přidání vlastního názvu domény do služby Azure Active Directory](../active-directory-domains-add-azure-portal.md).  

Pokud chcete zobrazit seznam ověřených domén vaší společnosti, můžete použít rutinu [Get-MsolDomain](/powershell/module/msonline/get-msoldomain?view=azureadps-1.0).

![Seznam domén společnosti](./media/hybrid-azuread-join-manual/01.png)

### <a name="issue-immutableid-for-the-computer-when-one-for-users-exists-for-example-an-alternate-login-id-is-set"></a>Problém ImmutableID pro počítač, pokud existuje jeden pro uživatele (například alternativní přihlašovací ID je nastavena)

Deklarace `http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID` musí obsahovat platnou hodnotu pro počítače. Ve službě AD FS můžete vytvořit následující pravidlo transformace vystavování:

   ```
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
   ```

### <a name="helper-script-to-create-the-ad-fs-issuance-transform-rules"></a>Pomocný skript pro vytváření pravidel transformace vystavování ve službě AD FS

Následující skript vám pomůže s vytvořením dříve popsaných pravidel transformace vystavování.

   ```
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
   ```

#### <a name="remarks"></a>Poznámky

* Tento skript připojí pravidla k existujícím pravidlům. Nespouštějte skript dvakrát, protože sada pravidel by byla přidána dvakrát. Před opětovným spuštěním skriptu se ujistěte, že pro tyto deklarace identity neexistují žádná odpovídající pravidla (za odpovídajících podmínek).
* Pokud máte více ověřených názvů domén (jak je znázorněno na portálu Azure AD nebo prostřednictvím rutiny **Get-MsolDomain),** nastavte hodnotu **$multipleVerifiedDomainNames** ve skriptu na **$true**. Také se ujistěte, že odeberete všechny existující **deklarace problémového ridu,** které mohly být vytvořeny službou Azure AD Connect nebo jinými prostředky. Zde je příklad pro toto pravidlo:

   ```
   c:[Type == "http://schemas.xmlsoap.org/claims/UPN"]
   => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)",  "http://${domain}/adfs/services/trust/")); 
   ```

Pokud jste již pro uživatelské účty vystavili deklaraci identity **ImmutableID** nastavte hodnotu proměnné **$immutableIDAlreadyIssuedforUsers** ve skriptu na **$true**.

## <a name="enable-windows-down-level-devices"></a>Povolení zařízení s Windows nižší úrovně

Pokud jsou některá z vašich zařízení připojených k doméně zařízení s Windows nižší úrovně, je potřeba provést následující:

* Nastavit v Azure AD zásadu, která uživatelům umožní registrovat zařízení.
* Nakonfigurovat místní federační službu na vystavování deklarací identity s podporou Integrovaného ověřování systému Windows pro registraci zařízení.
* Přidejte koncový bod ověřování zařízení Azure AD do místních intranetových zón, abyste se vyhnuli výzvám k certifikátu při ověřování zařízení.
* Ovládejte zařízení windows nižší úrovně.

### <a name="set-a-policy-in-azure-ad-to-enable-users-to-register-devices"></a>Nastavení zásad ve službě Azure AD, které uživatelům umožní registraci zařízení

Chcete-li zaregistrovat zařízení windows nižší úrovně, ujistěte se, že nastavení povolit uživatelům zaregistrovat zařízení ve službě Azure AD je povolena. Na webu Azure Portal najdete toto nastavení v části Uživatelé **služby Azure Active Directory** > **a seskupí** > **nastavení zařízení**.

Následující zásady musí být **nastaveny**na všechny : **Uživatelé mohou zaregistrovat svá zařízení s Azure AD**.

![Tlačítko Vše, které umožňuje uživatelům registrovat zařízení](./media/hybrid-azuread-join-manual/23.png)

### <a name="configure-the-on-premises-federation-service"></a>Konfigurace místní federační služby

Vaše místní federační služba musí podporovat vydávání **metody ověřování** a deklarací **wiaormultiauthn,** když obdrží požadavek na ověření předávající straně Azure AD, která drží parametr resource_params s následující kódovnou hodnotou:

   ```
   eyJQcm9wZXJ0aWVzIjpbeyJLZXkiOiJhY3IiLCJWYWx1ZSI6IndpYW9ybXVsdGlhdXRobiJ9XX0

   which decoded is {"Properties":[{"Key":"acr","Value":"wiaormultiauthn"}]}
   ```

Pokud takový požadavek přijde, musí místní federační služba ověřit uživatele pomocí integrovaného ověřování systému Windows. Pokud je ověření úspěšné, musí federační služba vydat následující dva deklarace identity:

   `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/windows` `http://schemas.microsoft.com/claims/wiaormultiauthn`

Ve službě AD FS je nutné přidat pravidlo transformace vystavování, které prochází metodou ověřování. Přidání tohoto pravidla:

1. V konzole pro správu služby AD FS přejděte na vztah**důvěryhodnosti důvěryhodných vztahů důvěryhodnosti vztahů důvěryhodnosti vztahů důvěryhodnosti** > **služby** **AD FS** > .
1. Klikněte pravým tlačítkem na objekt důvěryhodnosti přijímající strany služby Microsoft Office 365 Identity Platform a pak vyberte **Upravit pravidla deklarace identity**.
1. Na kartě **Pravidla transformace vystavování** vyberte **Přidat pravidlo**.
1. V seznamu šablon **Pravidlo deklarace identity** vyberte **Odesílat deklarace pomocí vlastního pravidla**.
1. Vyberte **další**.
1. Do pole **Název pravidla deklarace** zadejte **pravidlo deklarace metody auth**.
1. Do pole **Pravidlo deklarace** zadejte následující pravidlo:

   `c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"] => issue(claim = c);`

1. Na federačním serveru zadejte následující příkaz PowerShellu. Nahraďte ** \<\> RPObjectName** názvem objektu předávající strany pro objekt důvěryhodnosti předávající strany Azure AD. Tento objekt má obvykle název **Microsoft Office 365 Identity Platform**.

   `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

### <a name="add-the-azure-ad-device-authentication-endpoint-to-the-local-intranet-zones"></a>Přidání koncového bodu ověřování zařízení Azure AD do místních zón intranetu

Chcete-li se vyhnout výzvám certifikátu, když se uživatelé registrovaných zařízení ověřují ve službě Azure AD, můžete do zařízení spojených s doménou vyčlenit zásadu a přidat následující adresu URL do místní zóny intranet v aplikaci Internet Explorer:

`https://device.login.microsoftonline.com`

### <a name="control-windows-down-level-devices"></a>Řízení zařízení s Windows nižší úrovně

Pokud chcete registrovat zařízení s Windows nižší úrovně, musíte si z webu Download Center stáhnout a nainstalovat balíček Instalační služby systému Windows (.msi). Další informace najdete v části [Řízené ověření hybridního připojení Azure AD na zařízeních s windows nižší úrovní](hybrid-azuread-join-control.md#controlled-validation-of-hybrid-azure-ad-join-on-windows-down-level-devices).

## <a name="verify-joined-devices"></a>Ověření připojených zařízení

Úspěšně připojená zařízení ve vaší organizaci můžete zkontrolovat pomocí rutiny [Get-MsolDevice](/powershell/msonline/v1/get-msoldevice) v [modulu Azure Active Directory PowerShell](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

Výstup této rutiny ukazuje zařízení zaregistrovaná a připojená k Azure AD. Chcete-li získat všechna zařízení, použijte parametr **-All** a pak je filtrujte pomocí vlastnosti **deviceTrustType.** Zařízení připojená k doméně mají hodnotu **Doména připojena**.

## <a name="troubleshoot-your-implementation"></a>Řešení potíží s implementací

Pokud máte problémy s dokončením hybridního připojení azure ad pro zařízení s Windows připojená k doméně, přečtěte si:

* [Řešení potíží s hybridním připojením aktuálních zařízení s Windows k Azure AD](troubleshoot-hybrid-join-windows-current.md)
* [Řešení potíží s hybridním připojením zařízení s Windows nižší úrovně k Azure AD](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Další kroky

* [Úvod do správy zařízení v Azure Active Directory](overview.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-manual/12.png
