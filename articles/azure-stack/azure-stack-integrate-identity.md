---
title: Integrace datových center Azure Stack – Identity
description: Informace o integraci služby AD FS pro Azure Stack s vaším datovým centrem služby AD FS
services: azure-stack
author: jeffgilb
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 10/22/2018
ms.author: jeffgilb
ms.reviewer: wfayed
keywords: ''
ms.openlocfilehash: 8a33d4edb4107b936c36a744bb082c02b7830868
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50024439"
---
# <a name="azure-stack-datacenter-integration---identity"></a>Integrace datových center Azure Stack – Identity
Azure Stack pomocí Azure Active Directory (Azure AD) nebo Active Directory Federation Services (AD FS) můžete nasadit jako zprostředkovatele identity. Volba je třeba provést před nasazením služby Azure Stack. Nasazení pomocí služby AD FS se také označuje jako při nasazování služby Azure Stack v odpojeném režimu.

V následující tabulce jsou uvedeny rozdíly mezi identity dvě možnosti:

||Odpojení od Internetu|Připojení k Internetu|
|---------|---------|---------|
|Fakturace|Musí být kapacity<br> Pouze Enterprise Agreement (EA)|Kapacita nebo platbami jako využití<br>EA nebo poskytovatele Cloud Solution Provider (CSP)|
|Identita|Musí být služba AD FS|Azure AD nebo AD FS|
|Marketplace |Podporováno<br>BYOL licencování|Podporováno<br>BYOL licencování|
|Registrace|Doporučuje, vyžaduje vyměnitelné médium<br> a samostatné připojené zařízení.|Automatizované|
|Opravy a aktualizace|Povinné, vyžaduje vyměnitelné médium<br> a samostatné připojené zařízení.|Balíček aktualizace si můžete stáhnout přímo<br> z Internetu do služby Azure Stack.|

> [!IMPORTANT]
> Zprostředkovatel identity se nedá přejít bez opětovného nasazení celé řešení Azure Stack.

## <a name="active-directory-federation-services-and-graph"></a>Active Directory Federation Services a grafu

Nasazování se službou AD FS umožňuje identit v existující doménové struktury služby Active Directory k ověření pomocí prostředků ve službě Azure Stack. Tento existující doménové struktury služby Active Directory vyžaduje nasazení služby AD FS a umožňuje vytvoření důvěryhodnosti federace AD FS.

Ověřování je jednou ze součástí identity. Ke správě na základě řízení přístupu Role (RBAC) ve službě Azure Stack, musí být nakonfigurované komponenty grafu. Když se deleguje přístup k prostředku, komponenta grafu vyhledá uživatelský účet v existující doménové struktuře služby Active Directory pomocí protokolu LDAP.

![Architektura služby Azure Stack služby AD FS](media/azure-stack-integrate-identity/Azure-Stack-ADFS-architecture.png)

Existující služby AD FS je účet služby tokenů zabezpečení (STS), která odesílá deklarace identity do služby Azure Stack AD FS (zdrojem STS). Ve službě Azure Stack automatizace vytvoří vztah důvěryhodnosti zprostředkovatele deklarací se koncový bod metadat pro existující službu AD FS.

V existující služby AD FS musí být nakonfigurovaný vztah důvěryhodnosti předávající strany. Tento krok se provádí automatizace a musí být nakonfigurovaný pomocí operátoru. Koncový bod metadat služby Azure Stack je popsána v souboru AzureStackStampDeploymentInfo.JSON nebo prostřednictvím privilegovaných koncový bod pomocí příkazu `Get-AzureStackInfo`.

Konfiguraci vztahu důvěryhodnosti předávající strany také vyžaduje, abyste nakonfigurovali pravidla transformace deklarací identity, které jsou k dispozici společností Microsoft.

Pro konfiguraci grafu musí být účet služby, za předpokladu, že má oprávnění v existující služby Active Directory pro čtení. Tento účet je požadován jako vstup pro automatizaci, aby se povolily scénáře RBAC.

Poslední krok je nakonfigurován nového vlastníka pro výchozí předplatné poskytovatele. Tento účet má úplný přístup ke všem prostředkům při přihlášení k portálu Správce služby Azure Stack.

Požadavky:

|Komponenta|Požadavek|
|---------|---------|
|Graph|Microsoft Active Directory 2012/2012 R2/2016|
|AD FS|Windows Server 2012/2012 R2/2016|

## <a name="setting-up-graph-integration"></a>Nastavení integrace grafu

Graf podporuje pouze integraci s jednou doménovou strukturou Active Directory. Pokud existuje více doménových struktur, pouze struktuře zadaný v konfiguraci se použije k načtení uživatelů a skupin.

Tyto informace se vyžaduje jako vstup pro automatizaci parametry:

|Parametr|Popis|Příklad:|
|---------|---------|---------|
|CustomADGlobalCatalog|Plně kvalifikovaný název domény cílové doménové struktuře služby Active Directory<br>Chcete integrovat s|contoso.com|
|CustomADAdminCredentials|Uživatel s oprávněním ke čtení protokolu LDAP|YOURDOMAIN\graphservice|

### <a name="configure-active-directory-sites"></a>Konfigurace lokalit služby Active Directory

Pro nasazení služby Active Directory s více lokalitami nakonfigurujte nejbližší lokalitu služby Active Directory k nasazení Azure Stack. Konfigurace se vyhnete, že ve službě Azure Stack Graph vyřešit dotazy, které používají Server globálního katalogu od vzdáleného webu.

Přidání služby Azure Stack [síť veřejných virtuálních IP adres](azure-stack-network.md#public-vip-network) podsítě k webu Azure AD co nejblíže ke službě Azure Stack. Například pokud Active Directory obsahuje dvě lokality Seattle a Redmond, s využitím Azure stacku nasadit v lokalitě Seattle, by přidat podsíť sítě Azure stacku veřejnou virtuální IP adresy k webu Azure AD pro Seattle.

Další informace o serverů služby Active Directory najdete v části [navrhování topologie lokalit](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/designing-the-site-topology).

> [!Note]  
> Pokud se skládají z jedné lokality služby Active Directory můžete tento krok přeskočit. V případě, že máte podsíť pokrývající vše nakonfigurované, ověřte, že podsíť sítě Azure stacku veřejnou virtuální IP Adresou není jeho součástí.

### <a name="create-user-account-in-the-existing-active-directory-optional"></a>Vytvoření uživatelského účtu ve stávající službě Active Directory (volitelné)

Volitelně můžete vytvořit účet služby Graph v existující služby Active Directory. Tento krok proveďte, pokud ještě nemáte účet, který chcete použít.

1. V existující služby Active Directory vytvořte následující uživatelský účet (recommendation):
   - **Uživatelské jméno**: graphservice
   - **Heslo**: použijte silné heslo<br>Konfigurace hesla nikdy nevyprší.

   Je potřeba žádná zvláštní oprávnění nebo členství.

#### <a name="trigger-automation-to-configure-graph"></a>Aktivování automatizace konfigurace grafu

Pro tento postup použijte počítač v síti datového centra, který může komunikovat s koncovým bodem privilegovaných ve službě Azure Stack.

1. Otevřete relaci Windows Powershellu se zvýšenými oprávněními (Spustit jako správce) a připojit k IP adrese privileged koncového bodu. Použijte přihlašovací údaje pro **CloudAdmin** k ověření.

   ```PowerShell  
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Teď, když jste připojeni k privilegovaným koncový bod, spusťte následující příkaz: 

   ```PowerShell  
   Register-DirectoryService -CustomADGlobalCatalog contoso.com
   ```

   Po zobrazení výzvy zadejte přihlašovací údaje pro uživatelský účet, který chcete použít pro službu grafu (třeba graphservice). Vstup pro rutinu Register-DirectoryService musí odpovídat názvu doménové struktury / kořenové domény v doménové struktuře, nikoli všech ostatních domén v doménové struktuře.

   > [!IMPORTANT]
   > Počkejte místní přihlašovací údaje (Get-Credential není podporována v privilegovaných koncový bod) a zadejte přihlašovací údaje účtu služby Graph.

#### <a name="graph-protocols-and-ports"></a>Graf protokoly a porty

Služba Graph ve službě Azure Stack používá následující protokoly a porty, ke komunikaci se zapisovatelné Server globálního katalogu (GC) a distribuce softwaru KDC (Key), která dokáže zpracovávat žádosti o přihlášení v cílové doménové struktuře služby Active Directory.

Služba Graph ve službě Azure Stack používá následující protokoly a porty pro komunikaci s cílem služby Active Directory:

|Typ|Port|Protocol (Protokol)|
|---------|---------|---------|
|LDAP|389|TCP A UDP|
|LDAP SSL|636|TCP|
|GLOBÁLNÍ KATALOG LDAP|3268|TCP|
|SSL GLOBÁLNÍ KATALOG LDAP|3269|TCP|

## <a name="setting-up-ad-fs-integration-by-downloading-federation-metadata"></a>Nastavení integrace služby AD FS stažením federačních metadat

Tyto informace se vyžaduje jako vstup pro automatizaci parametry:

|Parametr|Popis|Příklad:|
|---------|---------|---------|
|CustomAdfsName|Název zprostředkovatele deklarací identity. <cr>Zobrazí se tak na cílové stránce služby AD FS.|Contoso|
|CustomAD<br>FSFederationMetadataEndpointUri|Federační metadata odkaz|https://ad01.contoso.com/federationmetadata/2007-06/federationmetadata.xml|


### <a name="trigger-automation-to-configure-claims-provider-trust-in-azure-stack"></a>Aktivování automatizace ke konfiguraci vztahu důvěryhodnosti zprostředkovatele deklarací identity ve službě Azure Stack

Pro tento postup použijte počítač, který může komunikovat s privileged koncového bodu ve službě Azure Stack. Očekává se, že tento účet používat certifikát **služby tokenů zabezpečení AD FS** je důvěryhodný pro Azure Stack.

1. Otevřete relaci Windows Powershellu se zvýšenými oprávněními a připojte se k privilegovaným koncový bod.

   ```PowerShell  
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Teď, když jste připojeni k privilegovaným koncový bod, spusťte následující příkaz pomocí parametrů, které jsou vhodné pro vaše prostředí:

   ```PowerShell  
   Register-CustomAdfs -CustomAdfsName Contoso -CustomADFSFederationMetadataEndpointUri https://win-SQOOJN70SGL.contoso.com/federationmetadata/2007-06/federationmetadata.xml
   ```

3. Spusťte následující příkaz k aktualizaci vlastníkem předplatného poskytovatele výchozí, pomocí parametrů, které jsou vhodné pro vaše prostředí:

   ```PowerShell  
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "administrator@contoso.com"
   ```

## <a name="setting-up-ad-fs-integration-by-providing-federation-metadata-file"></a>Nastavení integrace služby AD FS tím, že poskytuje soubor metadat federace

Počínaje verzí 1807, tuto metodu použijte, pokud platí některá z následujících podmínek:

- Řetěz certifikátů se liší pro službu AD FS ve srovnání s všechny ostatní koncové body ve službě Azure Stack.
- Z instance služby AD FS v Azure stacku neexistuje žádné připojení k síti na existující server služby AD FS.

Tyto informace se vyžaduje jako vstup pro automatizaci parametry:


|Parametr|Popis|Příklad:|
|---------|---------|---------|
|CustomAdfsName|Název zprostředkovatele deklarací identity. Zobrazí se tak na cílové stránce služby AD FS.|Contoso|
|CustomADFSFederationMetadataFileContent|Metadata obsahu|$using: federationMetadataFileContent|



### <a name="create-federation-metadata-file"></a>Vytvořit soubor metadat federace

Následující postup musíte použít počítač, který má síťové připojení k existující nasazení služby AD FS, který bude účet služby tokenů zabezpečení. Také musí být nainstalován potřebné certifikáty.

1. Otevřete relaci Windows Powershellu se zvýšenými oprávněními a spusťte následující příkaz, parametry, které jsou vhodné pro vaše prostředí:

   ```PowerShell  
    $metadata = (Invoke-WebRequest -URI " https://win-SQOOJN70SGL.contoso.com/federationmetadata/2007-06/federationmetadata.xml " -UseBasicParsing).Content
    Set-Content -Path c:\metadata.xml -Encoding Unicode -Value $metadata 

   ```

2. Zkopírujte soubor metadat do počítače, který může komunikovat s koncovým bodem privileged.

### <a name="trigger-automation-to-configure-claims-provider-trust-in-azure-stack"></a>Aktivování automatizace ke konfiguraci vztahu důvěryhodnosti zprostředkovatele deklarací identity ve službě Azure Stack

Tento postup použijte počítač, který může komunikovat s privileged koncového bodu ve službě Azure Stack a má přístup k souboru metadat, který jste vytvořili v předchozím kroku.

1. Otevřete relaci Windows Powershellu se zvýšenými oprávněními.

   ```PowerShell  
   $federationMetadataFileContent = get-content c:\metadata.xml
   $creds=Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   Register-CustomAdfs -CustomAdfsName Contoso -CustomADFSFederationMetadataFileContent $using:federationMetadataFileContent
   ```

2. Spusťte následující příkaz k aktualizaci vlastníkem předplatného poskytovatele výchozí, pomocí parametrů, které jsou vhodné pro vaše prostředí:

   ```PowerShell  
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "administrator@contoso.com"
   ```

   > [!Note]  
   > Při otočení certifikát na existující služby AD FS (účet služby tokenů zabezpečení) musí nastavení integrace služby AD FS znovu. Integrace musíte nastavit i v případě, že je dostupný koncový bod metadat nebo byl nakonfigurován tím, že poskytuje soubor metadat.

## <a name="configure-relying-party-on-existing-ad-fs-deployment-account-sts"></a>Nakonfigurovat předávající stranu na existující nasazení služby AD FS (účet služby tokenů zabezpečení)

Společnost Microsoft poskytuje skript, který nakonfiguruje vztah důvěryhodnosti předávající strany, včetně pravidel transformace deklarací identity. Použití skriptu není povinné, jako příkazy můžete spustit ručně.

Můžete stáhnout skript pomocné rutiny z [nástroje Azure Stack](https://github.com/Azure/AzureStack-Tools/tree/vnext/DatacenterIntegration/Identity) na Githubu.

Pokud se rozhodnete ručně spuštěním příkazů, postupujte podle těchto kroků:

1. Zkopírujte následující obsah do souboru .txt (například uložený jako c:\ClaimRules.txt) u vašeho datového centra člena instance nebo farmy služby AD FS:

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
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
   => issue(claim = c);
   ```

2. Ověřte, že ověřování pomocí formulářů Windows pro extranetu a intranetu je povolená. Nejprve ověřte, jestli jeho již povolena spuštěním následující rutiny:

   ```PowerShell  
   Get-AdfsAuthenticationProvider | where-object { $_.name -eq "FormsAuthentication" } | select Name, AllowedForPrimaryExtranet, AllowedForPrimaryIntranet
   ```

    > [!Note]  
    > Windows integrované ověřování (WIA) zastaralé podporované uživatelského agenta, řetězce se může pro vás AD FS nasazení může vyžadovat aktualizaci podporovat nejnovější klienty. Můžete si přečíst více o aktualizaci WIA nepodporuje identifikační řetězce v článku [konfigurace ověřování pomocí formulářů intranet pro zařízení, která nepodporují WIA](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-intranet-forms-based-authentication-for-devices-that-do-not-support-wia).<br>Postup povolení ověřování pomocí formuláře zásad popsané v článku [nakonfigurovat zásady ověřování](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-authentication-policies).

3. Chcete-li přidat vztah důvěryhodnosti předávající strany, spusťte následující příkaz prostředí Windows PowerShell na vaší instance služby AD FS nebo farmy člena. Ujistěte se, že aktualizujete koncový bod služby AD FS a přejděte na soubor vytvořený v kroku 1.

   **Pro službu AD FS 2016**

   ```PowerShell  
   Add-ADFSRelyingPartyTrust -Name AzureStack -MetadataUrl "https://YourAzureStackADFSEndpoint/FederationMetadata/2007-06/FederationMetadata.xml" -IssuanceTransformRulesFile "C:\ClaimIssuanceRules.txt" -AutoUpdateEnabled:$true -MonitoringEnabled:$true -enabled:$true -AccessControlPolicyName "Permit everyone" -TokenLifeTime 1440
   ```

   **Pro AD FS 2012/2012 R2**

   ```PowerShell  
   Add-ADFSRelyingPartyTrust -Name AzureStack -MetadataUrl "https://YourAzureStackADFSEndpoint/FederationMetadata/2007-06/FederationMetadata.xml" -IssuanceTransformRulesFile "C:\ClaimIssuanceRules.txt" -AutoUpdateEnabled:$true -MonitoringEnabled:$true -enabled:$true -TokenLifeTime 1440
   ```

   > [!IMPORTANT]  
   > Konfigurace autorizačních pravidel vystavování, při použití systému Windows Server 2012 nebo 2012 R2 AD FS musíte použít modul snap-in konzoly MMC AD FS.

4. Pokud používáte Internet Explorer nebo Microsoft Edge prohlížeč pro přístup k Azure zásobníku, musí ignorovat token vazby. V opačném případě se nezdaří pokusy o přihlášení. Na vaše instance služby AD FS nebo členem farmy spusťte následující příkaz:

   > [!note]  
   > Tento krok není použitelné při použití systému Windows Server 2012 nebo 2012 R2 AD FS. Je bezpečné tento příkaz přeskočit a pokračovat pomocí integrace rozhraní.

   ```PowerShell  
   Set-AdfsProperties -IgnoreTokenBinding $true
   ```

## <a name="spn-creation"></a>Vytvoření hlavního názvu služby

Existuje mnoho scénářů, které vyžadují používání hlavní název služby (SPN) pro ověřování. Následuje několik příkladů:

- Použití rozhraní příkazového řádku pomocí služby AD FS nasazení služby Azure Stack
- System Center Management Pack pro službu Azure Stack po nasazení se službou AD FS
- Poskytovatelé prostředků ve službě Azure Stack po nasazení se službou AD FS
- Různými aplikacemi
- Budete potřebovat jako neinteraktivní přihlášení

> [!Important]  
> Služba AD FS podporuje pouze interaktivní přihlašovací relace. Pokud budete potřebovat jako neinteraktivní přihlášení pro automatizované scénář, je nutné použít hlavní název služby.

Další informace o vytvoření názvu SPN najdete v tématu [vytvořit instanční objekt služby AD FS](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#create-service-principal-for-ad-fs).


## <a name="troubleshooting"></a>Řešení potíží

### <a name="configuration-rollback"></a>Vrácení změn konfigurace

Pokud dojde k chybě, která nechává prostředí ve stavu, ve kterém můžete nadále ověřovat, je k dispozici možnost vrácení zpět.

1. Otevřete relaci Windows Powershellu se zvýšenými oprávněními a spusťte následující příkazy:

   ```PowerShell  
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Potom spusťte následující rutinu:

   ```PowerShell  
   Reset-DatacenterIntegationConfiguration
   ```

   Po spuštění akce vrácení zpět, budou vráceny všechny změny konfigurace. Pouze ověřování pomocí integrovaného **CloudAdmin** uživatele je možné.

   > [!IMPORTANT]
   > Je nutné nakonfigurovat původního vlastníka výchozí předplatné poskytovatele.

   ```PowerShell  
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "azurestackadmin@[Internal Domain]"
   ```

### <a name="collecting-additional-logs"></a>Shromažďování dalších protokolů

Pokud některou z rutin selže, můžete shromažďovat další protokoly pomocí `Get-Azurestacklogs` rutiny.

1. Otevřete relaci Windows Powershellu se zvýšenými oprávněními a spusťte následující příkazy:

   ```PowerShell  
   $creds = Get-Credential
   Enter-pssession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Potom spusťte následující rutinu:

   ```PowerShell  
   Get-AzureStackLog -OutputPath \\myworstation\AzureStackLogs -FilterByRole ECE
   ```


## <a name="next-steps"></a>Další postup

[Integrace externích řešení monitorování](azure-stack-integrate-monitor.md)
