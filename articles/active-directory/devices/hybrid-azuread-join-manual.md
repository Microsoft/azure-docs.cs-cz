---
title: Ruční konfigurace hybridních zařízení připojených k Azure Active Directory | Microsoft Docs
description: Naučte se, jak ručně nakonfigurovat zařízení připojená k hybridním Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 04/16/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1aafcd8ace846d7da65d95d4148872d5a6eddeee
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587846"
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

## <a name="prerequisites"></a>Požadavky

V tomto kurzu se předpokládá, že jste obeznámeni s:

* [Úvod do správy zařízení v Azure Active Directory](./overview.md)
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

> [!WARNING]
> Pokud vaše organizace používá proxy servery, které zachycují provoz SSL pro scénáře, jako je prevence ztráty dat nebo omezení tenanta Azure AD, zajistěte, aby byl provoz do ' https://device.login.microsoftonline.com ' vyloučený z přerušení TLS a prověřený. Vyloučení se nepovedlo https://device.login.microsoftonline.com , může způsobit rušení s ověřováním klientským certifikátem a způsobuje problémy s registrací zařízení a podmíněným přístupem na základě zařízení.

Pokud vaše organizace plánuje použití bezproblémového jednotného přihlašování, musí být přístupná z počítačů v rámci vaší organizace, aby byla dostupná následující adresa URL. Musí se taky přidat do zóny Místní intranet uživatele.

* `https://autologon.microsoftazuread-sso.com`

Kromě toho by v zóně intranetu uživatele mělo být povolené následující nastavení: Povolit aktualizace stavového řádku přes skript.

Pokud vaše organizace používá spravované (nefederované) nastavení s místní službou Active Directory a nepoužívá Active Directory Federation Services (AD FS) (AD FS) k federovatí se službou Azure AD, pak se připojení ke službě Azure AD ve Windows 10 spoléhá na objekty počítače ve službě Active Directory, které se budou synchronizovat do Azure AD. Ujistěte se, že všechny organizační jednotky, které obsahují objekty počítačů, které musí být připojené k hybridní službě Azure AD, jsou povolené pro synchronizaci v konfiguraci Azure AD Connect synchronizace.

U zařízení s Windows 10 ve verzi 1703 nebo starší, pokud vaše organizace vyžaduje přístup k Internetu prostřednictvím odchozího proxy serveru, musíte implementovat automatické zjišťování webového proxy serveru (WPAD), aby se počítače s Windows 10 mohly registrovat do Azure AD.

Počínaje systémem Windows 10 1803, a to i v případě, že pokus o připojení k hybridní službě Azure AD prostřednictvím zařízení ve federované doméně prostřednictvím AD FS selže a pokud je Azure AD Connect nakonfigurovaná tak, aby synchronizoval objekty počítače nebo zařízení do Azure AD, zařízení se pokusí dokončit připojení k hybridní službě Azure AD pomocí synchronizovaného počítače nebo zařízení.

> [!NOTE]
> Abyste mohli úspěšně připojit synchronizaci zařízení, můžete jako součást konfigurace registrace zařízení vyloučit z konfigurace Azure AD Connect synchronizace výchozí atributy zařízení. Další informace o výchozích atributech zařízení synchronizovaných se službou Azure AD najdete v článku o [atributech synchronizovaných pomocí Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-sync-attributes-synchronized#windows-10).

Pokud chcete ověřit, jestli má zařízení přístup k výše uvedeným prostředkům Microsoftu pod účtem System, můžete použít skript pro [připojení k registraci testovacího zařízení](/samples/azure-samples/testdeviceregconnectivity/testdeviceregconnectivity/) .

## <a name="verify-configuration-steps"></a>Ověření kroků konfigurace

Hybridní zařízení připojená k Azure AD můžete nakonfigurovat pro různé typy platforem zařízení s Windows. Toto téma obsahuje požadované kroky pro všechny obvyklé scénáře konfigurace.  

V následující tabulce najdete přehled požadovaných kroků pro váš scénář:  

| Postup | Aktuální Windows a synchronizace hodnoty hash hesel | Aktuální Windows a federace | Windows nižší úrovně |
| :--- | :---: | :---: | :---: |
| Konfigurace spojovacího bodu služby | ![Zaškrtnout][1] | ![Zaškrtnout][1] | ![Zaškrtnout][1] |
| Nastavení vystavování deklarací identity |     | ![Zaškrtnout][1] | ![Zaškrtnout][1] |
| Povolení zařízení bez Windows 10 |       |        | ![Zaškrtnout][1] |
| Ověření připojených zařízení | ![Zaškrtnout][1] | ![Zaškrtnout][1] | ![Zaškrtnout][1] |

## <a name="configure-a-service-connection-point"></a>Konfigurace spojovacího bodu služby

Vaše zařízení během registrace používají objekt spojovací bod služby (SCP) a zjišťují informace o tenantovi Azure AD. V místní instanci služby Active Directory musí být objekt SCP pro zařízení připojená k hybridní službě Azure AD v oddílu konfigurace názvového kontextu v doménové struktuře počítače. Pro každou doménovou strukturu existuje pouze jeden názvový kontext konfigurace. V konfiguraci služby Active Directory s více doménovými strukturami musí spojovací bod služby existovat ve všech doménových strukturách, které obsahují počítače připojené k doméně.

Pomocí rutiny [**Get-ADRootDSE**](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee617246(v=technet.10)) můžete načíst názvový kontext konfigurace vaší doménové struktury.  

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

* Používá modul PowerShellu služby Active Directory a nástroje Active Directory Domain Services (služba AD DS). Tyto nástroje spoléhají na službu Active Directory Web Services běžící na řadiči domény. Služba Active Directory Web Services se podporuje v řadičích domény s Windows Serverem 2008 R2 nebo novějším.
* Se podporuje pouze v modulu MSOnline PowerShell verze 1.1.166.0. Tento modul můžete stáhnout pomocí [tohoto odkazu](https://www.powershellgallery.com/packages/MSOnline/1.1.166.0).
* Pokud nejsou nainstalované nástroje služba AD DS, `Initialize-ADSyncDomainJoinedComputerSync` selže. Nástroje pro služba AD DS můžete nainstalovat přes Správce serveru v části **funkce**  >    >  **Nástroje pro správu rolí** nástroje pro vzdálenou správu serveru.

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

V předchozím skriptu `$verifiedDomain = "contoso.com"` je zástupný symbol. Nahraďte ho jedním z ověřených názvů domén ve službě Azure AD. Abyste mohli doménu použít, musíte ji vlastnit.

Další informace o ověřených názvech domén najdete v tématu [Přidání vlastního názvu domény do Azure Active Directory](../fundamentals/add-custom-domain.md).

Pokud chcete zobrazit seznam ověřených domén vaší společnosti, můžete použít rutinu [Get-AzureADDomain](/powershell/module/Azuread/Get-AzureADDomain).

![Seznam domén společnosti](./media/hybrid-azuread-join-manual/01.png)

## <a name="set-up-issuance-of-claims"></a>Nastavení vystavování deklarací identity

V konfiguraci federované služby Azure AD se zařízení spoléhají na AD FS nebo místní federační službu od partnera Microsoftu k ověření ve službě Azure AD. Zařízení se ověřují za účelem získání přístupového tokenu, pomocí kterého se můžou zaregistrovat ve službě Azure Active Directory Device Registration (Azure DRS).

Aktuální zařízení s Windows se ověřují pomocí integrovaného ověřování systému Windows pro aktivní WS-Trust koncový bod (verze 1,3 nebo 2005) hostované místní službou FS (Federation Service).

Pokud používáte AD FS, musíte povolit následující WS-Trust koncové body.
- `/adfs/services/trust/2005/windowstransport`
- `/adfs/services/trust/13/windowstransport`
- `/adfs/services/trust/2005/usernamemixed`
- `/adfs/services/trust/13/usernamemixed`
- `/adfs/services/trust/2005/certificatemixed`
- `/adfs/services/trust/13/certificatemixed`

> [!WARNING]
> **AD FS/Services/Trust/2005/windowstransport** a **AD FS/Services/Trust/13/windowstransport** by měly být povolené jenom jako intranetové koncové body a nesmí být zveřejněné jako extranetové koncové body prostřednictvím proxy webových aplikací. Další informace o tom, jak zakázat WS-Trust koncové body Windows, najdete v tématu [zakázání WS-Trust koncových bodů Windows na proxy serveru](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet). Pomocí konzoly pro správu AD FS v části   >  **koncové body** služby můžete zjistit, jaké koncové body jsou povolené.

> [!NOTE]
>Pokud nemáte AD FS jako místní federační službu, postupujte podle pokynů od dodavatele a ujistěte se, že podporují koncové body WS-Trust 1,3 nebo 2005 a že jsou publikované prostřednictvím souboru výměny metadat (MEX).

Aby se registrace zařízení dokončila, musí v tokenu, který přijímá Azure DRS, existovat následující deklarace identity. Azure DRS vytvoří objekt zařízení ve službě Azure AD s některými z těchto informací. Azure AD Connect pak tyto informace použije k přidružení nově vytvořeného objektu zařízení k místnímu účtu počítače.

* `http://schemas.microsoft.com/ws/2012/01/accounttype`
* `http://schemas.microsoft.com/identity/claims/onpremobjectguid`
* `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`

Pokud máte více než jeden ověřený název domény, musíte počítačům poskytnout následující deklaraci identity:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid`

Pokud už vydáváte deklaraci identity ImmutableID (například použitím `mS-DS-ConsistencyGuid` nebo jiného atributu jako zdrojové hodnoty pro ImmutableID), musíte pro počítače zadat jednu odpovídající deklaraci identity:

* `http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`

V následujících částech najdete informace o těchto tématech:

* Hodnoty, které by měly mít jednotlivé deklarace identity.
* Definice by vypadala jako v AD FS.

Definice vám pomůže ověřit, jestli požadované hodnoty existují, nebo jestli je potřeba je vytvořit.

> [!NOTE]
> Pokud jako svůj místní federační server nepoužíváte AD FS, postupujte podle pokynů vašeho dodavatele a vytvořte odpovídající konfiguraci pro vystavování těchto deklarací identity.

### <a name="issue-account-type-claim"></a>Vystavení deklarace identity typu účtu

`http://schemas.microsoft.com/ws/2012/01/accounttype`Deklarace identity musí obsahovat hodnotu **přehrávače**, která identifikuje zařízení jako počítač připojený k doméně. Ve službě AD FS můžete přidat pravidlo transformace vystavování, které vypadá přibližně takto:

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

`http://schemas.microsoft.com/identity/claims/onpremobjectguid`Deklarace identity musí obsahovat hodnotu **objectGUID** účtu místního počítače. Ve službě AD FS můžete přidat pravidlo transformace vystavování, které vypadá přibližně takto:

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

`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`Deklarace identity musí obsahovat hodnotu **objectSID** účtu místního počítače. Ve službě AD FS můžete přidat pravidlo transformace vystavování, které vypadá přibližně takto:

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

`http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid`Deklarace identity musí obsahovat identifikátor URI (Uniform Resource Identifier) libovolného ověřeného názvu domény, který se připojuje k místní službě FS (AD FS nebo partner), který vydává token. V AD FS můžete přidat pravidla transformace vystavování, která v určitém pořadí vypadají jako následující, a to za předchozí. Všimněte si, že jedno pravidlo, které explicitně vydává pravidlo pro uživatele, je nezbytné. V následujících pravidlech se přidá první pravidlo, které identifikuje uživatele proti ověření počítače.

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

V předchozí deklaraci identity `<verified-domain-name>` je zástupný symbol. Nahraďte ho jedním z ověřených názvů domén ve službě Azure AD. Například použijte `Value = "http://contoso.com/adfs/services/trust/"` .

Další informace o ověřených názvech domén najdete v tématu [Přidání vlastního názvu domény do Azure Active Directory](../fundamentals/add-custom-domain.md).  

Pokud chcete zobrazit seznam ověřených domén vaší společnosti, můžete použít rutinu [Get-MsolDomain](/powershell/module/msonline/get-msoldomain).

![Seznam domén společnosti](./media/hybrid-azuread-join-manual/01.png)

### <a name="issue-immutableid-for-the-computer-when-one-for-users-exists-for-example-using-ms-ds-consistencyguid-as-the-source-for-immutableid"></a>Vydejte ImmutableID pro počítač, když pro uživatele existuje (třeba pomocí mS-DS-ConsistencyGuid jako zdroje pro ImmutableID).

`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`Deklarace identity musí obsahovat platnou hodnotu pro počítače. Ve službě AD FS můžete vytvořit následující pravidlo transformace vystavování:

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
* Pokud máte více ověřených názvů domén (jak je znázorněno na portálu Azure AD nebo prostřednictvím rutiny **Get-MsolDomain** ), nastavte ve skriptu **$true** hodnotu **$multipleVerifiedDomainNames** . Také se ujistěte, že jste odebrali všechny existující **issuerid** deklarace identity, kterou mohl vytvořit Azure AD Connect nebo prostřednictvím jiných prostředků. Tady je příklad pro toto pravidlo:

   ```
   c:[Type == "http://schemas.xmlsoap.org/claims/UPN"]
   => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)",  "http://${domain}/adfs/services/trust/")); 
   ```

Pokud jste již pro uživatelské účty vystavili deklaraci identity **ImmutableID** nastavte hodnotu proměnné **$immutableIDAlreadyIssuedforUsers** ve skriptu na **$true**.

## <a name="enable-windows-down-level-devices"></a>Povolení zařízení s Windows nižší úrovně

Pokud jsou některá z vašich zařízení připojených k doméně zařízení s Windows nižší úrovně, je potřeba provést následující:

* Nastavit v Azure AD zásadu, která uživatelům umožní registrovat zařízení.
* Nakonfigurovat místní federační službu na vystavování deklarací identity s podporou Integrovaného ověřování systému Windows pro registraci zařízení.
* Přidejte koncový bod ověřování zařízení Azure AD do zón místního intranetu, abyste se vyhnuli zobrazování výzev k certifikátu při ověřování zařízení.
* Ovládací prvek zařízení se systémem Windows nižší úrovně.

### <a name="set-a-policy-in-azure-ad-to-enable-users-to-register-devices"></a>Nastavení zásady v Azure AD, aby uživatelé mohli registrovat zařízení

Pokud chcete zaregistrovat zařízení Windows na nižší úrovni, ujistěte se, že je povolené nastavení Povolit uživatelům registraci zařízení v Azure AD. V Azure Portal můžete najít toto nastavení v části **Azure Active Directory**  >  **Uživatelé a skupiny**  >  **nastavení zařízení**.

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

1. V konzole pro správu AD FS přejít na **AD FS** vztahy  >  **důvěryhodnosti**  >  **předávající strany**.
1. Klikněte pravým tlačítkem na objekt důvěryhodnosti přijímající strany služby Microsoft Office 365 Identity Platform a pak vyberte **Upravit pravidla deklarace identity**.
1. Na kartě **Pravidla transformace vystavování** vyberte **Přidat pravidlo**.
1. V seznamu šablon **Pravidlo deklarace identity** vyberte **Odesílat deklarace pomocí vlastního pravidla**.
1. Vyberte **Další**.
1. Do pole **název pravidla deklarace identity** zadejte **pravidlo deklarace identity metody ověřování**.
1. Do pole **pravidlo deklarace identity** zadejte následující pravidlo:

   `c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"] => issue(claim = c);`

1. Na federačním serveru zadejte následující příkaz prostředí PowerShell. Nahraďte **\<RPObjectName\>** názvem objektu předávající strany pro objekt vztahu důvěryhodnosti předávající strany Azure AD. Tento objekt má obvykle název **Microsoft Office 365 Identity Platform**.

   `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

### <a name="add-the-azure-ad-device-authentication-endpoint-to-the-local-intranet-zones"></a>Přidat koncový bod ověřování zařízení Azure AD do zón místního intranetu

Abyste se vyhnuli zobrazování výzev k certifikátům při ověřování uživatelů registrovaných zařízení ve službě Azure AD, můžete na zařízení připojená k doméně vložit zásadu, která přidá následující adresu URL do zóny Místní intranet v Internet Exploreru:

`https://device.login.microsoftonline.com`

### <a name="control-windows-down-level-devices"></a>Řízení zařízení s Windows nižší úrovně

Pokud chcete registrovat zařízení s Windows nižší úrovně, musíte si z webu Download Center stáhnout a nainstalovat balíček Instalační služby systému Windows (.msi). Další informace najdete v části [řízení ověřování hybridního připojení ke službě Azure AD na zařízeních nižší úrovně Windows](hybrid-azuread-join-control.md#controlled-validation-of-hybrid-azure-ad-join-on-windows-down-level-devices).

## <a name="verify-joined-devices"></a>Ověření připojených zařízení

Tady jsou tři způsoby, jak vyhledat a ověřit stav zařízení:

### <a name="locally-on-the-device"></a>Místně na zařízení

1. Otevřete Windows PowerShell.
2. Zadejte `dsregcmd /status`.
3. Ověřte, že jsou **AzureAdJoined** i **DomainJoined** nastavené na **Ano**.
4. Můžete použít **DeviceID** a porovnat stav služby pomocí Azure Portal nebo PowerShellu.

### <a name="using-the-azure-portal"></a>Použití webu Azure Portal

1. V případě, že přejdete na stránku zařízení, použijte [přímý odkaz](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices).
2. Informace o tom, jak najít zařízení, najdete v tématu [Správa identit zařízení pomocí Azure Portal](./device-management-azure-portal.md#manage-devices).
3. Pokud **zaregistrovaný** sloupec **čeká na vyřízení**, připojení k hybridní službě Azure AD se nedokončilo. Ve federovaném prostředí se to může stát jenom v případě, že se nepovedlo zaregistrovat a AAD Connect je nakonfigurované k synchronizaci zařízení.
4. Pokud **zaregistrovaný** sloupec obsahuje **Datum a čas**, připojení k hybridní službě Azure AD se dokončilo.

### <a name="using-powershell"></a>Pomocí prostředí PowerShell

Ověřte stav registrace zařízení v tenantovi Azure pomocí **[Get-MsolDevice](/powershell/module/msonline/get-msoldevice)**. Tato rutina je v [modulu Azure Active Directory PowerShellu](/powershell/azure/active-directory/install-msonlinev1).

Při kontrole podrobností služby použijte rutinu **Get-MSolDevice** :

- Musí existovat objekt s **ID zařízení** , které odpovídá ID na klientském počítači se systémem Windows.
- Hodnota pro **DeviceTrustType** je **připojená k doméně**. Toto nastavení se rovná stavu **připojenému k hybridní službě Azure AD** na stránce **zařízení** na portálu Azure AD.
- U zařízení, která se používají v podmíněném přístupu, je **povolená** hodnota **true** a **DeviceTrustLevel** je **spravovaná**.

1. Spusťte Windows PowerShell jako správce.
2. Zadejte `Connect-MsolService` , abyste se připojili k vašemu Tenantovi Azure.

#### <a name="count-all-hybrid-azure-ad-joined-devices-excluding-pending-state"></a>Spočítat všechna hybridní zařízení připojená k Azure AD (s výjimkou stavu **čekání** )

```azurepowershell
(Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}).count
```

#### <a name="count-all-hybrid-azure-ad-joined-devices-with-pending-state"></a>Spočítat všechna zařízení připojená k hybridní službě Azure AD ve stavu **čekání**

```azurepowershell
(Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (-not([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}).count
```

#### <a name="list-all-hybrid-azure-ad-joined-devices"></a>Zobrazit všechna zařízení připojená k hybridní službě Azure AD

```azurepowershell
Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}
```

#### <a name="list-all-hybrid-azure-ad-joined-devices-with-pending-state"></a>Vypsat všechna zařízení připojená k hybridní službě Azure AD s **probíhajícím** stavem

```azurepowershell
Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (-not([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}
```

#### <a name="list-details-of-a-single-device"></a>Seznam podrobností o jednom zařízení:

1. Zadejte `get-msoldevice -deviceId <deviceId>` (Jedná se o **DeviceID** získanou místně na zařízení).
2. Ověřte, že je hodnota **Enabled** (Povoleno) nastavená na **True** (Pravda).

## <a name="troubleshoot-your-implementation"></a>Řešení potíží s implementací

Pokud dochází k problémům s dokončováním hybridního připojení služby Azure AD pro zařízení s Windows připojená k doméně, přečtěte si téma:

- [Řešení potíží se zařízeními pomocí příkazu dsregcmd](./troubleshoot-device-dsregcmd.md)
- [Řešení potíží se zařízeními připojenými službou Hybrid Azure Active Directory Join](troubleshoot-hybrid-join-windows-current.md)
- [Řešení potíží s modulem hybridní Azure Active Directory připojená zařízení nižší úrovně](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Další kroky

* [Úvod do správy zařízení v Azure Active Directory](overview.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-manual/12.png
