---
title: Ruční konfigurace hybridních zařízení připojených k Azure Active Directory | Microsoft Docs
description: Naučte se, jak ručně nakonfigurovat zařízení připojená k hybridním Azure Active Directory.
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
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78672275"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-joined-devices-manually"></a>Kurz: Ruční konfigurace hybridních zařízení připojených k Azure Active Directory

Pomocí správy zařízení v Azure Active Directory (Azure AD) můžete zajistit, aby uživatelé měli přístup k vašim prostředkům ze zařízení, která splňují vaše standardy pro zabezpečení a dodržování předpisů. Další informace najdete v tématu [Úvod do správy zařízení v Azure Active Directory](overview.md).

> [!TIP]
> Pokud je pro vás možnost použití Azure AD Connect, přečtěte si související kurzy ke [spravovaným](hybrid-azuread-join-managed-domains.md) nebo [federovaným](hybrid-azuread-join-federated-domains.md) doménám. Pomocí Azure AD Connect můžete významně zjednodušit konfiguraci hybridního připojení ke službě Azure AD.

Pokud máte místní prostředí Active Directory a chcete připojit svá zařízení připojená k doméně k Azure AD, můžete to provést tak, že nakonfigurujete hybridní zařízení připojená k Azure AD. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Ruční konfigurace služby hybridního připojení k Azure AD
> * Konfigurace spojovacího bodu služby
> * Nastavení vystavování deklarací identity
> * Povolení zařízení s Windows nižší úrovně
> * Ověření připojených zařízení
> * Řešení potíží s implementací

## <a name="prerequisites"></a>Předpoklady

V tomto kurzu se předpokládá, že jste obeznámeni s:

* [Úvod do správy zařízení v Azure Active Directory](../device-management-introduction.md)
* [Plánování implementace služby Hybrid Azure Active Directory JOIN](hybrid-azuread-join-plan.md)
* [Řízení připojení vašich zařízení k hybridní službě Azure AD](hybrid-azuread-join-control.md)

Než začnete ve své organizaci povolit zařízení připojená k hybridní službě Azure AD, ujistěte se, že máte tyto možnosti:

* Máte spuštěnou aktuální verzi Azure AD Connect.
* Azure AD Connect synchronizovali počítačové objekty zařízení, která chcete mít k Azure AD připojená k hybridní službě Azure AD. Pokud objekty počítače patří konkrétním organizačním jednotkám (OU), je nutné tyto organizační jednotky nakonfigurovat pro synchronizaci v Azure AD Connect.

Azure AD Connect:

* Zachovává přidružení mezi účtem počítače v místní instanci služby Active Directory a objektem zařízení ve službě Azure AD.
* Povolí další funkce související se zařízením, jako je Windows Hello pro firmy.

Ujistěte se, že jsou následující adresy URL přístupné z počítačů v síti vaší organizace pro registraci počítačů do služby Azure AD:

* `https://enterpriseregistration.windows.net`
* `https://login.microsoftonline.com`
* `https://device.login.microsoftonline.com`
* Služba STS vaší organizace (pro federované domény), která by měla být zahrnutá v nastavení místního intranetu uživatele

Pokud vaše organizace plánuje použití bezproblémového jednotného přihlašování, musí být přístupná z počítačů v rámci vaší organizace, aby byla dostupná následující adresa URL. Musí se taky přidat do zóny Místní intranet uživatele.

* `https://autologon.microsoftazuread-sso.com`

Kromě toho by v zóně intranetu uživatele mělo být povolené následující nastavení: Povolit aktualizace stavového řádku přes skript.

Pokud vaše organizace používá spravované (nefederované) nastavení s místní službou Active Directory a nepoužívá Active Directory Federation Services (AD FS) (AD FS) k federovatí s Azure AD, pak se připojení k hybridní službě Azure AD ve Windows 10 spoléhá na objekty počítače v aktivním prostředí. Adresář, který se má synchronizovat s Azure AD. Ujistěte se, že všechny organizační jednotky, které obsahují objekty počítačů, které musí být připojené k hybridní službě Azure AD, jsou povolené pro synchronizaci v konfiguraci Azure AD Connect synchronizace.

U zařízení s Windows 10 ve verzi 1703 nebo starší, pokud vaše organizace vyžaduje přístup k Internetu prostřednictvím odchozího proxy serveru, musíte implementovat automatické zjišťování webového proxy serveru (WPAD), aby se počítače s Windows 10 mohly registrovat do Azure AD.

Počínaje systémem Windows 10 1803, a to i v případě, že pokus o připojení k hybridní službě Azure AD prostřednictvím zařízení ve federované doméně prostřednictvím AD FS selže a pokud je Azure AD Connect nakonfigurovaná tak, aby synchronizoval objekty počítače nebo zařízení do Azure AD, zařízení se pokusí dokončit připojení k hybridní službě Azure AD pomocí synchronizovaného počítače nebo zařízení.

Pokud chcete ověřit, jestli má zařízení přístup k výše uvedeným prostředkům Microsoftu pod účtem System, můžete použít skript pro [připojení k registraci testovacího zařízení](https://gallery.technet.microsoft.com/Test-Device-Registration-3dc944c0) .

## <a name="verify-configuration-steps"></a>Ověření kroků konfigurace

Hybridní zařízení připojená k Azure AD můžete nakonfigurovat pro různé typy platforem zařízení s Windows. Toto téma obsahuje požadované kroky pro všechny obvyklé scénáře konfigurace.  

V následující tabulce najdete přehled požadovaných kroků pro váš scénář:  

| Kroky | Aktuální Windows a synchronizace hodnoty hash hesel | Aktuální Windows a federace | Windows nižší úrovně |
| :--- | :---: | :---: | :---: |
| Konfigurace spojovacího bodu služby | ![Zaškrtnout][1] | ![Zaškrtnout][1] | ![Zaškrtnout][1] |
| Nastavení vystavování deklarací identity |     | ![Zaškrtnout][1] | ![Zaškrtnout][1] |
| Povolení zařízení bez Windows 10 |       |        | ![Zaškrtnout][1] |
| Ověření připojených zařízení | ![Zaškrtnout][1] | ![Zaškrtnout][1] | [Zda][1] |

## <a name="configure-a-service-connection-point"></a>Konfigurace spojovacího bodu služby

Vaše zařízení během registrace používají objekt spojovací bod služby (SCP) a zjišťují informace o tenantovi Azure AD. V místní instanci služby Active Directory musí být objekt SCP pro zařízení připojená k hybridní službě Azure AD v oddílu konfigurace názvového kontextu v doménové struktuře počítače. Pro každou doménovou strukturu existuje pouze jeden názvový kontext konfigurace. V konfiguraci služby Active Directory s více doménovými strukturami musí spojovací bod služby existovat ve všech doménových strukturách, které obsahují počítače připojené k doméně.

Pomocí rutiny [**Get-ADRootDSE**](https://technet.microsoft.com/library/ee617246.aspx) můžete načíst názvový kontext konfigurace vaší doménové struktury.  

Pro doménovou strukturu s názvem domény Active Directory *fabrikam.com* je názvový kontext konfigurace následující:

`CN=Configuration,DC=fabrikam,DC=com`

Ve vaší doménové struktuře se objekt spojovacího bodu služby pro automatickou registraci zařízení připojených k doméně nachází v tomto umístění:  

`CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,[Your Configuration Naming Context]`

V závislosti na tom, jak jste nasadili Azure AD Connect, je možné, že objekt spojovacího bodu služby již byl nakonfigurován.
Můžete ověřit existenci objektu a načíst hodnoty zjišťování pomocí následujícího skriptu Windows PowerShellu:

   ```PowerShell
   $scp = New-Object System.DirectoryServices.DirectoryEntry;

   $scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=fabrikam,DC=com";

   $scp.Keywords;
   ```

**$SCP. Výstup klíčových slov** zobrazuje informace o Tenantovi Azure AD. Tady je příklad:

   ```
   azureADName:microsoft.com
   azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47
   ```

Pokud spojovací bod služby neexistuje, můžete ho vytvořit spuštěním rutiny `Initialize-ADSyncDomainJoinedComputerSync` na vašem serveru Azure AD Connect. Ke spuštění této rutiny se vyžadují přihlašovací údaje podnikového správce.  

Tato rutina:

* Vytvoří spojovací bod služby v doménové struktuře služby Active Directory, ke které je Azure AD Connect připojen.
* Vyžaduje, abyste zadali parametr `AdConnectorAccount`. Tento účet je nakonfigurovaný jako účet konektoru služby Active Directory v Azure AD Connect.


Následující skript ukazuje příklad použití této rutiny. V tomto skriptu příkaz `$aadAdminCred = Get-Credential` vyžaduje, abyste zadali uživatelské jméno. Musíte zadat uživatelské jméno ve formátu hlavního názvu uživatele (UPN) (`user@example.com`).

   ```PowerShell
   Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

   $aadAdminCred = Get-Credential;

   Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;
   ```

Rutina `Initialize-ADSyncDomainJoinedComputerSync`:

* Používá modul PowerShellu služby Active Directory a nástroje Azure Active Directory Domain Services (Azure služba AD DS). Tyto nástroje spoléhají na službu Active Directory Web Services běžící na řadiči domény. Služba Active Directory Web Services se podporuje v řadičích domény s Windows Serverem 2008 R2 nebo novějším.
* Je podporovaný jenom modulem PowerShellu MSOnline verze 1.1.166.0. Tento modul můžete stáhnout pomocí [tohoto odkazu](https://msconfiggallery.cloudapp.net/packages/MSOnline/1.1.166.0/).
* Pokud nejsou nainstalované nástroje služba AD DS, `Initialize-ADSyncDomainJoinedComputerSync` se nezdaří. Nástroje pro služba AD DS můžete nainstalovat přes Správce serveru v části **funkce** > **Nástroje pro vzdálenou správu serveru** **Nástroje pro správu rolí** > .

Pro řadiče domény se systémem Windows Server 2008 nebo staršími verzemi použijte následující skript k vytvoření spojovacího bodu služby. V konfiguraci s více doménovými strukturami použijte následující skript k vytvoření spojovacího bodu služby v každé doménové struktuře, kde počítače existují.

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

V předchozím skriptu je `$verifiedDomain = "contoso.com"` zástupný symbol. Nahraďte ho jedním z ověřených názvů domén ve službě Azure AD. Abyste mohli doménu použít, musíte ji vlastnit.

Další informace o ověřených názvech domén najdete v tématu [Přidání vlastního názvu domény do Azure Active Directory](../active-directory-domains-add-azure-portal.md).

Pokud chcete zobrazit seznam ověřených domén vaší společnosti, můžete použít rutinu [Get-AzureADDomain](/powershell/module/Azuread/Get-AzureADDomain?view=azureadps-2.0).

![Seznam domén společnosti](./media/hybrid-azuread-join-manual/01.png)

## <a name="set-up-issuance-of-claims"></a>Nastavení vystavování deklarací identity

V konfiguraci federované služby Azure AD se zařízení spoléhají na AD FS nebo místní federační službu od partnera Microsoftu k ověření ve službě Azure AD. Zařízení se ověřují za účelem získání přístupového tokenu, pomocí kterého se můžou zaregistrovat ve službě Azure Active Directory Device Registration (Azure DRS).

Aktuální zařízení s Windows se ověřují pomocí integrovaného ověřování systému Windows pro aktivní koncový bod WS-Trust (verze 1,3 nebo 2005) hostované místní službou FS (Federation Service).

Pokud používáte AD FS, musíte povolit následující koncové body WS-Trust.
- `/adfs/services/trust/2005/windowstransport`
- `/adfs/services/trust/13/windowstransport`
- `/adfs/services/trust/2005/usernamemixed`
- `/adfs/services/trust/13/usernamemixed`
- `/adfs/services/trust/2005/certificatemixed`
- `/adfs/services/trust/13/certificatemixed`

> [!WARNING]
> **AD FS/Services/Trust/2005/windowstransport** , **AD FS/Services/Trust/13/windowstransport** by měly být povolené jenom jako intranetové koncové body a nesmí být zveřejněné jako extranetové koncové body prostřednictvím proxy webových aplikací. Další informace o tom, jak zakázat koncové body systému Windows WS-Trust, najdete v tématu [zakázání koncových bodů systému Windows WS-Trust na proxy serveru](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet). To, jaké koncové body jsou povolené, můžete zobrazit pomocí konzoly pro správu AD FS v části **koncové body** **služby** > .

> [!NOTE]
>Pokud nemáte AD FS jako místní federační službu, postupujte podle pokynů od dodavatele a ujistěte se, že podporují koncové body WS-Trust 1,3 nebo 2005 a že jsou publikované prostřednictvím souboru výměny metadat (MEX).

Aby se registrace zařízení dokončila, musí v tokenu, který přijímá Azure DRS, existovat následující deklarace identity. Azure DRS vytvoří objekt zařízení ve službě Azure AD s některými z těchto informací. Azure AD Connect pak tyto informace použije k přidružení nově vytvořeného objektu zařízení k místnímu účtu počítače.

* `http://schemas.microsoft.com/ws/2012/01/accounttype`
* `http://schemas.microsoft.com/identity/claims/onpremobjectguid`
* `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`

Pokud máte více než jeden ověřený název domény, musíte počítačům poskytnout následující deklaraci identity:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid`

Pokud už vydáváte deklaraci identity ImmutableID (například alternativním přihlašovacím ID), musíte pro počítače zadat jednu odpovídající deklaraci identity:

* `http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`

V následujících částech najdete informace o těchto tématech:

* Hodnoty, které by měly mít jednotlivé deklarace identity.
* Definice by vypadala jako v AD FS.

Definice vám pomůže ověřit, jestli požadované hodnoty existují, nebo jestli je potřeba je vytvořit.

> [!NOTE]
> Pokud jako svůj místní federační server nepoužíváte AD FS, postupujte podle pokynů vašeho dodavatele a vytvořte odpovídající konfiguraci pro vystavování těchto deklarací identity.

### <a name="issue-account-type-claim"></a>Vystavení deklarace identity typu účtu

Deklarace `http://schemas.microsoft.com/ws/2012/01/accounttype` musí obsahovat hodnotu **přehrávače DJ**, která identifikuje zařízení jako počítač připojený k doméně. Ve službě AD FS můžete přidat pravidlo transformace vystavování, které vypadá přibližně takto:

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

Deklarace `http://schemas.microsoft.com/identity/claims/onpremobjectguid` musí obsahovat hodnotu **objectGUID** účtu místního počítače. Ve službě AD FS můžete přidat pravidlo transformace vystavování, které vypadá přibližně takto:

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

Deklarace `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid` musí obsahovat hodnotu **objectSID** účtu místního počítače. Ve službě AD FS můžete přidat pravidlo transformace vystavování, které vypadá přibližně takto:

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

### <a name="issue-issuerid-for-the-computer-when-multiple-verified-domain-names-are-in-azure-ad"></a>Vystavení issuerID pro počítač, pokud jsou v Azure AD víc ověřených názvů domén

Deklarace `http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid` musí obsahovat identifikátor URI (Uniform Resource Identifier) libovolného ověřeného názvu domény, který se připojuje k místní službě FS (AD FS nebo partner), který vydává token. V AD FS můžete přidat pravidla transformace vystavování, která v určitém pořadí vypadají jako následující, a to za předchozí. Všimněte si, že jedno pravidlo, které explicitně vydává pravidlo pro uživatele, je nezbytné. V následujících pravidlech se přidá první pravidlo, které identifikuje uživatele proti ověření počítače.

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

V předchozí deklaraci identity je `<verified-domain-name>` zástupný symbol. Nahraďte ho jedním z ověřených názvů domén ve službě Azure AD. Použijte například `Value = "http://contoso.com/adfs/services/trust/"`.

Další informace o ověřených názvech domén najdete v tématu [Přidání vlastního názvu domény do Azure Active Directory](../active-directory-domains-add-azure-portal.md).  

Pokud chcete zobrazit seznam ověřených domén vaší společnosti, můžete použít rutinu [Get-MsolDomain](/powershell/module/msonline/get-msoldomain?view=azureadps-1.0).

![Seznam domén společnosti](./media/hybrid-azuread-join-manual/01.png)

### <a name="issue-immutableid-for-the-computer-when-one-for-users-exists-for-example-an-alternate-login-id-is-set"></a>Vystavení ImmutableID počítače, když pro uživatele existuje (například alternativní ID přihlášení je nastavené)

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

Následující skript vám pomůže s vytvořením pravidel transformace vystavování popsaných výše.

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

* Tento skript připojí pravidla k existujícím pravidlům. Nespouštějte skript dvakrát, protože sada pravidel by se přidala dvakrát. Před opětovným spuštěním skriptu se ujistěte, že pro tyto deklarace identity neexistují žádná odpovídající pravidla (za odpovídajících podmínek).
* Pokud máte více ověřených názvů domén (jak je znázorněno na portálu Azure AD nebo prostřednictvím rutiny **Get-MsolDomain** ), nastavte ve skriptu **$true**hodnotu **$multipleVerifiedDomainNames** . Také se ujistěte, že jste odebrali všechny existující **issuerid** deklarace identity, kterou mohl vytvořit Azure AD Connect nebo prostřednictvím jiných prostředků. Tady je příklad pro toto pravidlo:

   ```
   c:[Type == "http://schemas.xmlsoap.org/claims/UPN"]
   => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)",  "http://${domain}/adfs/services/trust/")); 
   ```

Pokud jste již pro uživatelské účty vystavili deklaraci identity **ImmutableID** nastavte hodnotu proměnné **$immutableIDAlreadyIssuedforUsers** ve skriptu na **$true**.

## <a name="enable-windows-down-level-devices"></a>Povolení zařízení s Windows nižší úrovně

Pokud jsou některá z vašich zařízení připojených k doméně zařízení s Windows nižší úrovně, je potřeba provést následující:

* Nastavit v Azure AD zásadu, která uživatelům umožní registrovat zařízení.
* Nakonfigurujte místní federační službu tak, aby vydávala deklarace identity pro podporu integrovaného ověřování systému Windows (IWA) pro registraci zařízení.
* Přidejte koncový bod ověřování zařízení Azure AD do zón místního intranetu, abyste se vyhnuli zobrazování výzev k certifikátu při ověřování zařízení.
* Ovládací prvek zařízení se systémem Windows nižší úrovně.

### <a name="set-a-policy-in-azure-ad-to-enable-users-to-register-devices"></a>Nastavení zásady v Azure AD, aby uživatelé mohli registrovat zařízení

Pokud chcete zaregistrovat zařízení Windows na nižší úrovni, ujistěte se, že je povolené nastavení Povolit uživatelům registraci zařízení v Azure AD. V Azure Portal můžete najít toto nastavení v části **Azure Active Directory** > **uživatelé a skupiny** > **nastavení zařízení**.

Následující zásady musí být nastavené na **All**: **Uživatelé můžou svá zařízení zaregistrovat ve službě Azure AD**.

![Tlačítko vše, které umožňuje uživatelům registrovat zařízení](./media/hybrid-azuread-join-manual/23.png)

### <a name="configure-the-on-premises-federation-service"></a>Konfigurace místní služby FS (Federation Service)

Vaše místní služba FS (Federation Service) musí podporovat vydávání deklarací **authenticationmethod** a **wiaormultiauthn** , když obdrží žádost o ověření do předávající strany Azure AD, která drží parametr resource_params s následující zakódovanou hodnotou:

   ```
   eyJQcm9wZXJ0aWVzIjpbeyJLZXkiOiJhY3IiLCJWYWx1ZSI6IndpYW9ybXVsdGlhdXRobiJ9XX0

   which decoded is {"Properties":[{"Key":"acr","Value":"wiaormultiauthn"}]}
   ```

V takovém případě musí místní služba FS (Federation Service) ověřit uživatele pomocí integrovaného ověřování systému Windows. Po úspěšném ověření musí federační služba vystavovat následující dvě deklarace identity:

   `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/windows` `http://schemas.microsoft.com/claims/wiaormultiauthn`

V AD FS musíte přidat pravidlo transformace vystavování, které projde metodou ověřování. Přidání tohoto pravidla:

1. V konzole pro správu AD FS klikněte na **AD FS** > **vztahy důvěryhodnosti** > vztahy důvěryhodnosti **předávající strany**.
1. Klikněte pravým tlačítkem na objekt důvěryhodnosti přijímající strany služby Microsoft Office 365 Identity Platform a pak vyberte **Upravit pravidla deklarace identity**.
1. Na kartě **Pravidla transformace vystavování** vyberte **Přidat pravidlo**.
1. V seznamu šablon **Pravidlo deklarace identity** vyberte **Odesílat deklarace pomocí vlastního pravidla**.
1. Vyberte **Další**.
1. Do pole **název pravidla deklarace identity** zadejte **pravidlo deklarace identity metody ověřování**.
1. Do pole **pravidlo deklarace identity** zadejte následující pravidlo:

   `c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"] => issue(claim = c);`

1. Na federačním serveru zadejte následující příkaz prostředí PowerShell. **\>\<RPObjectName** nahraďte názvem objektu předávající strany pro objekt vztahu důvěryhodnosti předávající strany Azure AD. Tento objekt má obvykle název **Microsoft Office 365 Identity Platform**.

   `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

### <a name="add-the-azure-ad-device-authentication-endpoint-to-the-local-intranet-zones"></a>Přidat koncový bod ověřování zařízení Azure AD do zón místního intranetu

Abyste se vyhnuli zobrazování výzev k certifikátům při ověřování uživatelů registrovaných zařízení ve službě Azure AD, můžete na zařízení připojená k doméně vložit zásadu, která přidá následující adresu URL do zóny Místní intranet v Internet Exploreru:

`https://device.login.microsoftonline.com`

### <a name="control-windows-down-level-devices"></a>Řízení zařízení s Windows nižší úrovně

Pokud chcete registrovat zařízení s Windows nižší úrovně, musíte si z webu Download Center stáhnout a nainstalovat balíček Instalační služby systému Windows (.msi). Další informace najdete v části [řízení ověřování hybridního připojení ke službě Azure AD na zařízeních nižší úrovně Windows](hybrid-azuread-join-control.md#controlled-validation-of-hybrid-azure-ad-join-on-windows-down-level-devices).

## <a name="verify-joined-devices"></a>Ověření připojených zařízení

Úspěšně připojená zařízení ve vaší organizaci můžete vyhledat pomocí rutiny [Get-MsolDevice](/powershell/msonline/v1/get-msoldevice) v [modulu Azure Active Directory PowerShellu](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

Výstup této rutiny ukazuje zařízení zaregistrovaná a připojená k Azure AD. Pokud chcete získat všechna zařízení, použijte parametr **-All** a pak je filtrujte pomocí vlastnosti **deviceTrustType** . Zařízení připojená k doméně mají hodnotu **připojeno k doméně**.

## <a name="troubleshoot-your-implementation"></a>Řešení potíží s implementací

Pokud máte problémy s dokončením hybridního připojení ke službě Azure AD pro zařízení s Windows připojená k doméně, přečtěte si téma:

* [Řešení potíží s hybridním připojením aktuálních zařízení s Windows k Azure AD](troubleshoot-hybrid-join-windows-current.md)
* [Řešení potíží s hybridním připojením zařízení s Windows nižší úrovně k Azure AD](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Další kroky

* [Úvod do správy zařízení v Azure Active Directory](overview.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-manual/12.png
