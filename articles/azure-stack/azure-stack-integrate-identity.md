---
title: Zásobník datacenter integrace se službou Azure - Identity
description: Zjistěte, jak integrovat zásobník Azure AD FS s vaším datovým centrem služby AD FS
services: azure-stack
author: jeffgilb
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 05/01/2018
ms.author: jeffgilb
ms.reviewer: wfayed
keywords: ''
ms.openlocfilehash: dc6c8ef2953b7495c734ec8b16530cdd812ac792
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="azure-stack-datacenter-integration---identity"></a>Zásobník datacenter integrace se službou Azure - Identity
Zásobník Azure pomocí Azure Active Directory (Azure AD) nebo Active Directory Federation Services (AD FS) můžete nasadit jako zprostředkovatele identity. Volba je nutné provést před nasazením Azure zásobníku. Nasazení pomocí služby AD FS se také označuje jako nasazení Azure zásobníku v odpojeném režimu.

V následující tabulce jsou uvedeny rozdíly mezi dvěma identity volby:

||Odpojení od Internetu|Připojení k Internetu|
|---------|---------|---------|
|Fakturace|Musí být kapacity<br> Smlouva Enterprise Agreement (EA) pouze|Kapacitu nebo platím jako jste – použití<br>EA nebo poskytovatele Cloud Solution Provider (CSP)|
|Identita|Musí být služba AD FS|Azure AD ani AD FS|
|Syndikace Marketplace.|Podporováno<br>BYOL licencování|Podporováno<br>BYOL licencování|
|Registrace|Doporučené, vyžaduje vyměnitelné médium<br> a samostatné připojené zařízení.|Automatizované|
|Opravy a aktualizace|Vyžaduje, vyžaduje vyměnitelné médium<br> a samostatné připojené zařízení.|Balíček aktualizace můžete stáhnout přímo<br> z Internetu do Azure zásobníku.|

> [!IMPORTANT]
> Bez opětovného nasazení celé řešení Azure zásobníku nelze přepnout zprostředkovatele identity.

## <a name="active-directory-federation-services-and-graph"></a>Graf a služby Active Directory Federation Services

Nasazení se službou AD FS umožňuje identit v existující doménové struktury služby Active Directory k ověření s prostředky v Azure zásobníku. Tento existující doménové struktury služby Active Directory vyžaduje nasazení služby AD FS umožňuje vytvoření vztahu důvěryhodnosti federace služby AD FS.

Ověřování je jednou ze součástí identity. Ke správě na základě řízení přístupu Role (RBAC) v Azure zásobníku, musí být nakonfigurované komponentu grafu. Když je delegovaný přístup k prostředku, vyhledá komponentu grafu uživatelský účet v existující doménové struktuře služby Active Directory pomocí protokolu LDAP.

![Architektura Azure zásobníku služby AD FS](media/azure-stack-integrate-identity/Azure-Stack-ADFS-architecture.png)

Existující služby AD FS je účet služby tokenů zabezpečení (STS), odešle deklarace identity ve službě AD FS Azure zásobníku (prostředků služby tokenů zabezpečení). V zásobníku Azure automation vytvoří vztah důvěryhodnosti zprostředkovatele deklarací s koncovým bodem metadat pro stávající službu AD FS.

Na stávající služby AD FS musí být nakonfigurované vztah důvěryhodnosti předávající strany. Tento krok není potřeba automatizace a musí být nakonfigurované operátorem. Koncový bod metadat zásobník Azure je popsána v souboru AzureStackStampDeploymentInfo.JSON, nebo prostřednictvím privilegované koncový bod spuštěním příkazu `Get-AzureStackInfo`.

Konfigurace vztahu důvěryhodnosti předávající strany také vyžaduje, abyste nakonfigurovali pravidla transformace deklarací identity, které jsou k dispozici společností Microsoft.

Pro konfiguraci grafu musí být účet služby za předpokladu, že má oprávnění v existující služby Active Directory pro čtení. Tento účet je nutný jako vstup pro automatizaci k povolení scénářů RBAC.

Poslední krok je pro výchozí zprostředkovatel předplatné nakonfigurované nového vlastníka. Tento účet má úplný přístup ke všem prostředkům při přihlášení k portálu správce Azure zásobníku.

Požadavky:


|Komponenta|Požadavek|
|---------|---------|
|Graph|Microsoft Active Directory 2012 nebo 2012 R2/2016|
|AD FS|Windows Server 2012/2012 R2/2016|

## <a name="setting-up-graph-integration"></a>Nastavení integrace grafu

Graf pouze podporuje integraci s jednou doménovou strukturou Active Directory. Pokud existuje několik doménových struktur, pouze struktuře zadaný v konfiguraci se použije k načtení uživatelů a skupin.

Tyto informace se vyžaduje jako vstupy pro automatizaci parametry:


|Parametr|Popis|Příklad:|
|---------|---------|---------|
|CustomADGlobalCatalog|Plně kvalifikovaný název domény cílového doménové struktury služby Active Directory<br>Chcete-li integrovat|contoso.com|
|CustomADAdminCredentials|Uživatel s oprávněním ke čtení protokolu LDAP|YOURDOMAIN\graphservice|

### <a name="create-user-account-in-the-existing-active-directory-optional"></a>Vytvoření uživatelského účtu ve stávající službě Active Directory (volitelné)

Volitelně můžete vytvořit účet pro službu grafu v existující služby Active Directory. Tento krok proveďte, pokud ještě nemáte účet, který chcete použít.

1. V existující služby Active Directory vytvořte následující uživatelský účet (doporučení):
   - **Uživatelské jméno**: graphservice
   - **Heslo**: použijte silné heslo<br>Nakonfigurujte hesla nikdy nevyprší.

   Není potřeba žádná zvláštní oprávnění nebo členství.

#### <a name="trigger-automation-to-configure-graph"></a>Aktivační události automatizace konfigurace grafu

Pro tento postup použijte počítač v síti datového centra, který může komunikovat s privilegované koncového bodu v zásobníku Azure.

2. Otevřete relaci prostředí Windows PowerShell zvýšenými oprávněními (Spustit jako správce) a připojte na IP adresu privilegované koncového bodu. Použijte pověření pro **CloudAdmin** k ověření.

   ```powershell
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

3. Teď, když jste připojení ke koncovému bodu privilegované, spusťte následující příkaz: 

   ```powershell
   Register-DirectoryService -CustomADGlobalCatalog contoso.com
   ```

   Po zobrazení výzvy zadejte přihlašovací údaje pro uživatelský účet, který chcete použít pro službu grafu (například graphservice). Vstup pro rutinu Register-DirectoryService musí být název doménové struktury nebo kořen domény v doménové struktuře a nikoli všech ostatních domén v doménové struktuře.

   > [!IMPORTANT]
   > Počkejte místní přihlašovací údaje (Get-Credential není podporována v privilegované koncového bodu) a zadejte přihlašovací údaje účtu grafu.

#### <a name="graph-protocols-and-ports"></a>Graf protokoly a porty

Grafu služby v zásobníku Azure používá následující protokoly a porty pro komunikaci s možností zápisu Server globální katalog (GC) a Center KDC (Key Distribution), může zpracovat žádosti o přihlášení v cílové doménové struktury služby Active Directory.

Služba grafu v zásobníku Azure používá následující protokoly a porty pro komunikaci s cílem služby Active Directory:

|Typ|Port|Protocol (Protokol)|
|---------|---------|---------|
|LDAP|389|TCP A UDP|
|LDAP SSL|636|TCP|
|GLOBÁLNÍ KATALOG LDAP|3268|TCP|
|SSL GLOBÁLNÍ KATALOG LDAP|3269|TCP|

## <a name="setting-up-ad-fs-integration-by-downloading-federation-metadata"></a>Nastavení integrace služby AD FS stažením federačních metadat

Tyto informace o požadované jako vstup pro automatizaci parametry:

|Parametr|Popis|Příklad:|
|---------|---------|---------|
|CustomAdfsName|Název zprostředkovatele deklarací identity. <cr>Zobrazí se tak na cílovou stránku služby AD FS.|Contoso|
|CustomAD<br>FSFederationMetadataEndpointUri|Federační metadata odkaz|https://ad01.contoso.com/federationmetadata/2007-06/federationmetadata.xml|


### <a name="trigger-automation-to-configure-claims-provider-trust-in-azure-stack"></a>Automation aktivační události. ke konfiguraci vztahu důvěryhodnosti zprostředkovatele deklarací identity v Azure zásobníku

Pro tento postup použijte počítač, který může komunikovat s privilegované koncovým bodem v zásobníku Azure. Očekává se, že certifikát použít účet **služby tokenů zabezpečení AD FS** důvěřují zásobník Azure.

1. Otevřete relaci prostředí Windows PowerShell zvýšenými oprávněními a připojení k privilegované koncový bod.

   ```powershell
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Teď, když jste připojení ke koncovému bodu privilegované, spusťte následující příkaz pomocí parametrů, které jsou vhodné pro vaše prostředí:

   ```powershell
   Register-CustomAdfs -CustomAdfsName Contoso -CustomADFSFederationMetadataEndpointUri https://win-SQOOJN70SGL.contoso.com/federationmetadata/2007-06/federationmetadata.xml
   ```

3. Spusťte následující příkaz k aktualizaci vlastník předplatného poskytovatele výchozí, s parametry, které jsou vhodné pro vaše prostředí:

   ```powershell
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "administrator@contoso.com"
   ```

## <a name="setting-up-ad-fs-integration-by-providing-federation-metadata-file"></a>Nastavení integrace služby AD FS tím, že poskytuje soubor federačních metadat

Tuto metodu použijte, pokud platí některá z následujících podmínek:

- Řetěz certifikátů se liší pro službu AD FS ve srovnání s všechny ostatní koncové body v zásobníku Azure.
- Neexistuje žádné připojení k síti na existující server služby AD FS z Azure zásobníku instanci služby AD FS.

Tyto informace o požadované jako vstup pro automatizaci parametry:


|Parametr|Popis|Příklad:|
|---------|---------|---------|
|CustomAdfsName|Název zprostředkovatele deklarací identity. Zobrazí se tak na cílovou stránku služby AD FS.|Contoso|
|CustomADFSFederationMetadataFile|Soubor metadat federace|https://ad01.contoso.com/federationmetadata/2007-06/federationmetadata.xml|

### <a name="create-federation-metadata-file"></a>Vytvoření souboru federační metadata

Pro následující postup musíte použít počítač, který má síťové připojení k existující nasazení služby AD FS, který se stane účet služby tokenů zabezpečení. Navíc musí být nainstalován potřebné certifikáty.

1. Otevřete relaci prostředí Windows PowerShell zvýšenými oprávněními a spusťte následující příkaz, parametry, které jsou vhodné pro vaše prostředí:

   ```powershell
   [XML]$Metadata = Invoke-WebRequest -URI https://win-SQOOJN70SGL.contoso.com/federationmetadata/2007-06/federationmetadata.xml -UseBasicParsing

   $Metadata.outerxml|out-file c:\metadata.xml
   ```

2. Zkopírujte soubor metadat do sdílené složky, která je přístupná z privilegovaných koncového bodu.


### <a name="trigger-automation-to-configure-claims-provider-trust-in-azure-stack"></a>Automation aktivační události. ke konfiguraci vztahu důvěryhodnosti zprostředkovatele deklarací identity v Azure zásobníku

Pro tento postup použijte počítač, který může komunikovat s privilegované koncovým bodem v zásobníku Azure.

1. Otevřete relaci prostředí Windows PowerShell zvýšenými oprávněními a připojení k privilegované koncový bod.

   ```powershell
   $creds=Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Teď, když jste připojení ke koncovému bodu privilegované, spusťte následující příkaz pomocí parametrů, které jsou vhodné pro vaše prostředí:

   ```powershell
   Register-CustomAdfs -CustomAdfsName Contoso – CustomADFSFederationMetadataFile \\share\metadataexample.xml
   ```

3. Spusťte následující příkaz k aktualizaci vlastník předplatného poskytovatele výchozí, s parametry, které jsou vhodné pro vaše prostředí:

   ```powershell
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "administrator@contoso.com"
   ```

## <a name="configure-relying-party-on-existing-ad-fs-deployment-account-sts"></a>Nakonfigurovat předávající strany na existující nasazení služby AD FS (účet služby tokenů zabezpečení)

Společnost Microsoft poskytuje skript, který nakonfiguruje důvěryhodnosti předávající strany, včetně pravidla transformace deklarací identity. Pomocí skriptu je nepovinný, protože příkazy můžete spustit ručně.

Si můžete stáhnout skript pomocné rutiny z [nástroje Azure zásobníku](https://github.com/Azure/AzureStack-Tools/tree/vnext/DatacenterIntegration/Identity) na Githubu.

Pokud se rozhodnete ručně spustit příkazy, postupujte takto:

1. Zkopírujte následující obsah do souboru .txt (například uložit jako c:\ClaimRules.txt) ve vašem datovém centru člen instance nebo farmy služby AD FS:

   ```text
   @RuleTemplate = "LdapClaims"
   @RuleName = "Name claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"), query = ";userPrincipalName;{0}", param = c.Value);

   @RuleTemplate = "LdapClaims"
   @RuleName = "UPN claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"), query = ";userPrincipalName;{0}", param = c.Value);

   @RuleTemplate = "LdapClaims"
   @RuleName = "ObjectID claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid"]
   => issue(Type = "http://schemas.microsoft.com/identity/claims/objectidentifier", Issuer = c.Issuer, OriginalIssuer = c.OriginalIssuer, Value = c.Value, ValueType = c.ValueType);

   @RuleName = "Family Name and Given claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname", "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname"), query = ";sn,givenName;{0}", param = c.Value);

   @RuleTemplate = "PassThroughClaims"
   @RuleName = "Pass through all Group SID claims"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
   => issue(claim = c);

   @RuleTemplate = "PassThroughClaims"
   @RuleName = "Pass through all windows account name claims"
   c:[Type == http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname]
   => issue(claim = c);
   ```

2. Pokud chcete povolit ověřování pomocí formulářů Windows, otevřete relaci prostředí Windows PowerShell jako uživatel s oprávněním vyšší úrovně a spusťte následující příkaz:

   ```powershell
   Set-AdfsProperties -WIASupportedUserAgents @("MSAuthHost/1.0/In-Domain","MSIPC","Windows Rights Management Client","Kloud")
   ```

3. Pokud chcete přidat vztah důvěryhodnosti předávající strany, spusťte následující příkaz prostředí Windows PowerShell na vaše instance služby AD FS nebo členem farmy. Zajistěte, aby se aktualizovat koncový bod služby AD FS a přejděte na soubor vytvořený v kroku 1.

   **Pro službu AD FS 2016**

   ```powershell
   Add-ADFSRelyingPartyTrust -Name AzureStack -MetadataUrl "https://YourAzureStackADFSEndpoint/FederationMetadata/2007-06/FederationMetadata.xml" -IssuanceTransformRulesFile "C:\ClaimIssuanceRules.txt" -AutoUpdateEnabled:$true -MonitoringEnabled:$true -enabled:$true -AccessControlPolicyName "Permit everyone"
   ```

   **Pro AD FS 2012 nebo 2012 R2**

   ```powershell
   Add-ADFSRelyingPartyTrust -Name AzureStack -MetadataUrl "https://YourAzureStackADFSEndpoint/FederationMetadata/2007-06/FederationMetadata.xml" -IssuanceTransformRulesFile "C:\ClaimIssuanceRules.txt" -AutoUpdateEnabled:$true -MonitoringEnabled:$true -enabled:$true
   ```

   > [!IMPORTANT]
   > Modul snap-in konzoly MMC AD FS musíte použít ke konfiguraci autorizačních pravidel vystavování, pokud používáte Windows Server 2012 nebo 2012 R2 AD FS.

4. Pokud používáte Internet Explorer nebo Microsoft Edge prohlížeč pro přístup k Azure zásobníku, musí ignorovat token vazby. V opačném pokusů o přihlášení nezdaří. Na vaše instance služby AD FS nebo členem farmy spusťte následující příkaz:

   > [!note]  
   > Tento krok není použitelný, pokud používáte Windows Server 2012 nebo 2012 R2 AD FS. Je bezpečné Přeskočit tento příkaz a pokračujte integrace.

   ```powershell
   Set-AdfsProperties -IgnoreTokenBinding $true
   ```

5. Pokud chcete povolit tokeny obnovení, otevřete relaci prostředí Windows PowerShell zvýšenými oprávněními a spusťte následující příkaz:

   ```powershell
   Set-ADFSRelyingPartyTrust -TargetName AzureStack -TokenLifeTime 1440
   ```

## <a name="spn-creation"></a>Vytvoření názvu SPN

Existuje mnoho scénářů, které vyžadují použití hlavní název služby (SPN) pro ověřování. Následuje několik příkladů:

- Použití rozhraní příkazového řádku se nasazení služby AD FS Azure zásobníku
- System Center Management Pack pro Azure zásobníku při nasazení se službou AD FS
- Zprostředkovatelé prostředků v Azure zásobníku při nasazení se službou AD FS
- Různé aplikace
- Vyžadujete-interaktivní přihlášení

Další informace o vytvoření názvu SPN najdete v tématu [vytvořit objekt služby pro službu AD FS](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#create-service-principal-for-ad-fs).


## <a name="troubleshooting"></a>Řešení potíží

### <a name="configuration-rollback"></a>Vrácení zpět konfigurace

Pokud dojde k chybě, který zůstane ve stavu, kde můžete už ověřovat prostředí, je k dispozici možnost vrácení zpět.

1. Otevřete relaci prostředí Windows PowerShell zvýšenými oprávněními a spusťte následující příkazy:

   ```powershell
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Spusťte následující rutinu:

   ```powershell
   Reset-DatacenterIntegationConfiguration
   ```

   Po spuštění akce vrácení zpět, budou vráceny všechny změny konfigurace. Pouze ověřování pomocí integrované **CloudAdmin** uživatele je možné.

   > [!IMPORTANT]
   > Je nutné nakonfigurovat původní vlastník předplatného výchozí zprostředkovatel

   ```powershell
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "azurestackadmin@[Internal Domain]"
   ```

### <a name="collecting-additional-logs"></a>Shromažďování dalších protokolů

Pokud žádné z rutiny selže, můžete shromažďovat další protokoly pomocí `Get-Azurestacklogs` rutiny.

1. Otevřete relaci prostředí Windows PowerShell zvýšenými oprávněními a spusťte následující příkazy:

   ```powershell
   $creds = Get-Credential
   Enter-pssession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Potom spusťte následující rutinu:

   ```powershell
   Get-AzureStackLog -OutputPath \\myworstation\AzureStackLogs -FilterByRole ECE
   ```


## <a name="next-steps"></a>Další postup

[Integrovat externí řešení monitorování](azure-stack-integrate-monitor.md)
