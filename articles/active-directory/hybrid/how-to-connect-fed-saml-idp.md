---
title: 'Azure AD Connect: použití zprostředkovatele identity SAML 2,0 pro jednotné přihlašování – Azure'
description: Tento dokument popisuje použití IDP kompatibilního s SAML 2,0 pro jednotné přihlašování.
services: active-directory
author: billmath
manager: daveba
ms.custom: it-pro
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b26c24149d422021dcb86f75c915ade89cbccdec
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97589871"
---
#  <a name="use-a-saml-20-identity-provider-idp-for-single-sign-on"></a>Použití zprostředkovatele identity (IdP) SAML 2.0 pro Jednotné přihlašování

Tento dokument obsahuje informace o použití zprostředkovatele identity na základě profilu SP-Lite kompatibilního s SAML 2,0 jako preferovaného poskytovatele služby tokenů zabezpečení (STS) nebo zprostředkovatele identity. Tento scénář je užitečný, když už máte adresář uživatele a úložiště hesel místně, ke kterým se dá dostat pomocí SAML 2,0. Tento existující adresář uživatele se dá použít k přihlášení k Microsoft 365 a dalším prostředkům zabezpečeným službou Azure AD. Profil SP-Lite SAML 2,0 vychází z široce používaného standardu federovaného identity Security Assertion Markup Language (SAML), který poskytuje rozhraní pro přihlašování a výměnu atributů.

>[!NOTE]
>Seznam zprostředkovatelů identity třetích stran, které byly testovány pro použití se službou Azure AD, najdete v [seznamu kompatibility federace Azure AD](how-to-connect-fed-compatibility.md) .

Microsoft podporuje toto přihlašování jako integraci cloudové služby Microsoftu, jako je například Microsoft 365, s správně nakonfigurovaným IdPem založeným na profilech SAML 2,0. Zprostředkovatelé identit SAML 2,0 jsou produkty třetích stran, a proto společnost Microsoft neposkytuje podporu pro nasazení, konfiguraci a řešení potíží s osvědčenými postupy, které se týkají. Po správném nakonfigurování se integrace se zprostředkovatelem identity SAML 2,0 může testovat ke správné konfiguraci pomocí nástroje Microsoft Connectivity Analyzer, který je podrobněji popsaný níže. Další informace o poskytovateli identity založeném na profilu SAML 2,0 SP-Lite, požádejte o organizaci, která ji dodala.

> [!IMPORTANT]
> V tomto scénáři přihlášení s poskytovateli identity SAML 2,0 jsou k dispozici pouze omezená sada klientů, mezi které patří:
> 
> - Webové klienty, jako je Outlook Web Access a SharePoint Online
> - Klienti s bohatou e-mailem, kteří používají základní ověřování a podporovanou přístupovou metodu pro Exchange, jako je IMAP, POP, Active Sync, MAPI atd. (vyžaduje se nasazení rozšířeného koncového bodu klientského protokolu), včetně:
>     - Microsoft Outlook 2010/Outlook 2013/Outlook 2016, Apple iPhone (různé verze iOS)
>     - Různá zařízení Google Android
>     - Windows Phone 7, Windows Phone 7,8 a Windows Phone 8,0
>     - Klient pro poštovní klient Windows 8 a Windows 8.1 pošty
>     - Klient Windows 10 mail

Všichni ostatní klienti nejsou k dispozici v tomto scénáři přihlášení s vaším poskytovatelem identity SAML 2,0. Například klient pro stolní počítače Lync 2010 se nemůže přihlásit ke službě s vaším poskytovatelem identity SAML 2,0 nakonfigurovaným pro jednotné přihlašování.

## <a name="azure-ad-saml-20-protocol-requirements"></a>Požadavky protokolu SAML 2,0 pro Azure AD
Tento dokument obsahuje podrobné požadavky na formátování protokolu a zprávy, které musí zprostředkovatel identity SAML 2,0 implementovat, aby se federovat s Azure AD, aby bylo možné přihlašovat se k jedné nebo více cloudovým službám Microsoftu (například Microsoft 365). Předávající strana SAML 2,0 (SP-STS) pro cloudovou službu Microsoftu, která se používá v tomto scénáři, je Azure AD.

Doporučuje se zajistit, aby výstupní zprávy zprostředkovatele identity SAML 2,0 byly co nejblíže zadaným ukázkovým trasováním. Použijte taky konkrétní hodnoty atributů z poskytnutých metadat Azure AD, kde je to možné. Až budete s výstupními zprávami spokojeni, můžete otestovat pomocí analyzátoru připojení Microsoft, jak je popsáno níže.

Metadata Azure AD je možné stáhnout z této adresy URL: [https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml](https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml) .
Pro zákazníky v Číně, kteří používají instanci Microsoft 365 specifickou pro Čínu, by se měl použít následující koncový bod federace: [https://nexus.partner.microsoftonline-p.cn/federationmetadata/saml20/federationmetadata.xml](https://nexus.partner.microsoftonline-p.cn/federationmetadata/saml20/federationmetadata.xml) .

## <a name="saml-protocol-requirements"></a>Požadavky protokolu SAML
V této části se dozvíte, jak se páry zpráv požadavků a odpovědí ukládají dohromady, aby vám pomohly správně naformátovat zprávy.

Azure AD se dá nakonfigurovat tak, aby fungoval s poskytovateli identity, kteří používají profil SAML 2,0 SP Lite s některými konkrétními požadavky, jak je uvedeno níže. Pomocí ukázkových zpráv požadavku a odpovědí SAML společně s automatizovaným a ručním testováním můžete pracovat na zajištění interoperability se službou Azure AD.

## <a name="signature-block-requirements"></a>Požadavky na blok signatur
V rámci zprávy s odpovědí SAML obsahuje uzel podpisu informace o digitální signatuře samotné zprávy. Blok signatury má následující požadavky:

1. Samotný uzel kontrolního výrazu musí být podepsán.
2. Algoritmus RSA-SHA1 se musí použít jako DigestMethod. Jiné algoritmy digitálního podpisu nejsou přijímány.
   `<ds:DigestMethod Algorithm="https://www.w3.org/2000/09/xmldsig#sha1"/>`
3. Dokument XML můžete také podepsat. 
4. Transformační algoritmus musí odpovídat hodnotám v následující ukázce:     `<ds:Transform Algorithm="https://www.w3.org/2000/09/xmldsig#enveloped-signature"/>
       <ds:Transform Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#"/>`
9. Algoritmus metodě SignatureMethod musí odpovídat následující ukázce:    `<ds:SignatureMethod Algorithm="https://www.w3.org/2000/09/xmldsig#rsa-sha1"/>`

## <a name="supported-bindings"></a>Podporované vazby
Vazby jsou povinné parametry komunikace související s přenosem. Následující požadavky platí pro vazby.

1. Požadovaná přeprava je HTTPS.
2. Služba Azure AD bude během přihlašování vyžadovat pro odeslání tokenu HTTP POST.
3. Azure AD použije HTTP POST pro požadavek na ověření pro zprostředkovatele identity a přesměruje zprávy pro odhlášení do zprostředkovatele identity.

## <a name="required-attributes"></a>Požadované atributy
Tato tabulka zobrazuje požadavky na konkrétní atributy ve zprávě SAML 2,0.
 
|Atribut|Popis|
| ----- | ----- |
|NameID|Hodnota tohoto kontrolního výrazu musí být stejná jako ImmutableID uživatele Azure AD. Může to být až 64 alfanumerických znaků. Všechny bezpečné znaky, které nejsou ve formátu HTML, musí být kódované, například znak "+" je zobrazen jako ". 2B".|
|IDPEmail|Hlavní název uživatele (UPN) je uvedený v odpovědi SAML jako element s názvem IDPEmail uživatele UserPrincipalName (UPN) v Azure AD/Microsoft 365. Hlavní název uživatele (UPN) je ve formátu e-mailové adresy. Hodnota hlavního názvu uživatele (UPN) ve Windows Microsoft 365 (Azure Active Directory).|
|Vystavitel|Musí se jednat o identifikátor URI poskytovatele identity. Neprovádějte opakované použití vystavitele z ukázkových zpráv. Pokud máte ve svých klientech Azure AD víc domén nejvyšší úrovně, Vystavitel se musí shodovat se zadaným nastavením identifikátoru URI nakonfigurovaným pro jednotlivé domény.|

>[!IMPORTANT]
>Azure AD aktuálně podporuje následující NameID formát URI pro SAML 2.0: urn: Oasis: names: TC: SAML: 2.0: NameId-Format: persistent.

## <a name="sample-saml-request-and-response-messages"></a>Ukázka zpráv požadavku a odpovědí SAML
Pro výměnu přihlašovacích zpráv se zobrazí dvojice zpráv požadavek a odpověď.
Následuje ukázka zprávy požadavku, která je odeslána z Azure AD do ukázkového poskytovatele identity SAML 2,0. Vzorový zprostředkovatel identity SAML 2,0 je Active Directory Federation Services (AD FS) (AD FS) nakonfigurovaným pro použití protokolu SAML-P. Testování interoperability bylo také dokončeno s jinými zprostředkovateli identity SAML 2,0.

```xml
  <samlp:AuthnRequest 
    xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol" 
    xmlns:saml="urn:oasis:names:tc:SAML:2.0:assertion" 
    ID="_7171b0b2-19f2-4ba2-8f94-24b5e56b7f1e" 
    IssueInstant="2014-01-30T16:18:35Z" 
    Version="2.0" 
    AssertionConsumerServiceIndex="0" >
        <saml:Issuer>urn:federation:MicrosoftOnline</saml:Issuer>
        <samlp:NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
  </samlp:AuthnRequest>
```

Následuje ukázka zprávy s odpovědí, která je odeslána z ukázkového poskytovatele identity kompatibilního s SAML 2,0 do Azure AD/Microsoft 365.

```xml
    <samlp:Response ID="_592c022f-e85e-4d23-b55b-9141c95cd2a5" Version="2.0" IssueInstant="2014-01-31T15:36:31.357Z" Destination="https://login.microsoftonline.com/login.srf" Consent="urn:oasis:names:tc:SAML:2.0:consent:unspecified" InResponseTo="_049917a6-1183-42fd-a190-1d2cbaf9b144" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">http://WS2012R2-0.contoso.com/adfs/services/trust</Issuer>
    <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
    </samlp:Status>
    <Assertion ID="_7e3c1bcd-f180-4f78-83e1-7680920793aa" IssueInstant="2014-01-31T15:36:31.279Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">
    <Issuer>http://WS2012R2-0.contoso.com/adfs/services/trust</Issuer>
    <ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
      <ds:SignedInfo>
        <ds:CanonicalizationMethod Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#" />
        <ds:SignatureMethod Algorithm="https://www.w3.org/2000/09/xmldsig#rsa-sha1" />
        <ds:Reference URI="#_7e3c1bcd-f180-4f78-83e1-7680920793aa">
          <ds:Transforms>
            <ds:Transform Algorithm="https://www.w3.org/2000/09/xmldsig#enveloped-signature" />
            <ds:Transform Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#" />
          </ds:Transforms>
          <ds:DigestMethod Algorithm="https://www.w3.org/2000/09/xmldsig#sha1" />
          <ds:DigestValue>CBn/5YqbheaJP425c0pHva9PhNY=</ds:DigestValue>
        </ds:Reference>
      </ds:SignedInfo>
      <ds:SignatureValue>TciWMyHW2ZODrh/2xrvp5ggmcHBFEd9vrp6DYXp+hZWJzmXMmzwmwS8KNRJKy8H7XqBsdELA1Msqi8I3TmWdnoIRfM/ZAyUppo8suMu6Zw+boE32hoQRnX9EWN/f0vH6zA/YKTzrjca6JQ8gAV1ErwvRWDpyMcwdYCiWALv9ScbkAcebOE1s1JctZ5RBXggdZWrYi72X+I4i6WgyZcIGai/rZ4v2otoWAEHS0y1yh1qT7NDPpl/McDaTGkNU6C+8VfjD78DrUXEcAfKvPgKlKrOMZnD1lCGsViimGY+LSuIdY45MLmyaa5UT4KWph6dA==</ds:SignatureValue>
      <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
        <ds:X509Data>
          <ds:X509Certificate>MIIC7jCCAdagAwIBAgIQRrjsbFPaXIlOG3GTv50fkjANBgkqhkiG9w0BAQsFADAzMTEwLwYDVQQDEyhBREZTIFNpZ25pbmcgLSBXUzIwMTJSMi0wLnN3aW5mb3JtZXIuY29tMB4XDTE0MDEyMDE1MTY0MFoXDTE1MDEyMDE1MTY0MFowMzExMC8GA1UEAxMoQURGUyBTaWduaW5nIC0gV1MyMDEyUjItMC5zd2luZm9ybWVyLmNvbTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAKe+rLVmXy1QwCwZwqgbbp1/+3ZWxd9T/jV0hpLIIWr+LCOHqq8n8beJvlivgLmDJo8f+EITnAxWcsJUvVai/35AhHCUq9tc9sqMp5PWtabAEMb2AU72/QlX/72D2/NbGQq1BWYbqUpgpCZ2nSgvlWDHlCiUo//UGsvfox01kjTFlmqQInsJVfRxF5AcCAwEAATANBgkqhkiG9w0BAQsFAAOCAQEAi8c6C4zaTEc7aQiUgvnGQgCbMZbhUXXLGRpjvFLKaQzkwa9eq7WLJibcSNyGXBa/SfT5wJgsm3TPKgSehGAOTirhcqHheZyvBObAScY7GOT+u9pVYp6raFrc7ez3c+CGHeV/tNvy1hJNs12FYH4X+ZCNFIT9tprieR25NCdi5SWUbPZL0tVzJsHc1y92b2M2FxqRDohxQgJvyJOpcg2mSBzZZIkvDg7gfPSUXHVS1MQs0RHSbwq/XdQocUUhl9/e/YWCbNNxlM84BxFsBUok1dH/gzBySx+Fc8zYi7cOq9yaBT3RLT6cGmFGVYZJW4FyhPZOCLVNsLlnPQcX3dDg9A==</ds:X509Certificate>
        </ds:X509Data>
      </KeyInfo>
    </ds:Signature>
    <Subject>
      <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">ABCDEG1234567890</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="_049917a6-1183-42fd-a190-1d2cbaf9b144" NotOnOrAfter="2014-01-31T15:41:31.357Z" Recipient="https://login.microsoftonline.com/login.srf" />
      </SubjectConfirmation>
    </Subject>
    <Conditions NotBefore="2014-01-31T15:36:31.263Z" NotOnOrAfter="2014-01-31T16:36:31.263Z">
      <AudienceRestriction>
        <Audience>urn:federation:MicrosoftOnline</Audience>
      </AudienceRestriction>
    </Conditions>
    <AttributeStatement>
      <Attribute Name="IDPEmail">
        <AttributeValue>administrator@contoso.com</AttributeValue>
      </Attribute>
    </AttributeStatement>
    <AuthnStatement AuthnInstant="2014-01-31T15:36:30.200Z" SessionIndex="_7e3c1bcd-f180-4f78-83e1-7680920793aa">
      <AuthnContext>
        <AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport</AuthnContextClassRef>
      </AuthnContext>
    </AuthnStatement>
    </Assertion>
    </samlp:Response>
```

## <a name="configure-your-saml-20-compliant-identity-provider"></a>Konfigurace poskytovatele identity kompatibilního s SAML 2,0
Tato část obsahuje pokyny, jak nakonfigurovat poskytovatele identity SAML 2,0 tak, aby se federovat s Azure AD a povolili přístup k jednotnému přihlašování k jedné nebo více cloudovým službám Microsoftu (například Microsoft 365) pomocí protokolu SAML 2,0. Předávající strana SAML 2,0 pro cloudovou službu Microsoftu, která se používá v tomto scénáři, je Azure AD.

## <a name="add-azure-ad-metadata"></a>Přidat metadata Azure AD
Poskytovatel identity SAML 2,0 musí dodržovat informace o předávající straně služby Azure AD. Azure AD publikuje metadata na adrese https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml .

Při konfiguraci poskytovatele identity SAML 2,0 doporučujeme vždy importovat nejnovější metadata služby Azure AD.

>[!NOTE]
>Azure AD nečte metadata od poskytovatele identity.

## <a name="add-azure-ad-as-a-relying-party"></a>Přidat Azure AD jako předávající stranu
Musíte povolit komunikaci mezi vaším poskytovatelem identity SAML 2,0 a službou Azure AD. Tato konfigurace bude závislá na konkrétním zprostředkovateli identity a měli byste si pro něj vykázat dokumentaci. Obvykle byste nastavili ID předávající strany na stejné jako entityID z metadat Azure AD.

>[!NOTE]
>Ověřte, že hodiny na serveru zprostředkovatele identity SAML 2,0 jsou synchronizované s přesným zdrojem času. Nepřesné časové časy můžou způsobit selhání federovaných přihlášení.

## <a name="install-windows-powershell-for-sign-on-with-saml-20-identity-provider"></a>Instalace prostředí Windows PowerShell pro přihlášení pomocí zprostředkovatele identity SAML 2,0
Po nakonfigurování zprostředkovatele identity SAML 2,0 pro použití s přihlášením pomocí Azure AD je dalším krokem stažení a instalace modulu Azure Active Directory pro prostředí Windows PowerShell. Po instalaci budete tyto rutiny používat ke konfiguraci domén Azure AD jako federovaných domén.

Modul Azure Active Directory pro prostředí Windows PowerShell je stažením pro správu dat organizací ve službě Azure AD. Tento modul nainstaluje sadu rutin do prostředí Windows PowerShell. Tyto rutiny můžete použít k nastavení přístupu jednotného přihlašování ke službě Azure AD a ke všem cloudovým službám, ke kterým jste se přihlásili. Pokyny ke stažení a instalaci rutin najdete v tématu [/Previous-Versions/Azure/jj151815 (v = Azure. 100)](/previous-versions/azure/jj151815(v=azure.100)) .

## <a name="set-up-a-trust-between-your-saml-identity-provider-and-azure-ad"></a>Nastavení vztahu důvěryhodnosti mezi poskytovatelem identity SAML a Azure AD
Před konfigurací federace v doméně Azure AD musí mít nakonfigurovanou vlastní doménu. Nemůžete federovat výchozí doménu, kterou poskytuje Microsoft. Výchozí doména od Microsoftu končí na "onmicrosoft.com".
V rozhraní příkazového řádku Windows PowerShellu budete spouštět řadu rutin pro přidání nebo převod domén pro jednotné přihlašování.

Každá Azure Active Directory doména, kterou chcete federovat pomocí poskytovatele identity SAML 2,0, musí být buď přidána jako doména s jednotným přihlašováním, nebo převedená na doménu s jednotným přihlašováním ze standardní domény. Přidání nebo převedení domény nastaví vztah důvěryhodnosti mezi poskytovatelem identity SAML 2,0 a službou Azure AD.

Následující postup vás provede převodem existující standardní domény na federované domény pomocí SAML 2,0 SP-Lite. 

>[!NOTE]
>Vaše doména může nastat při výpadku, který má vliv na uživatele až na 2 hodiny po provedení tohoto kroku.

## <a name="configuring-a-domain-in-your-azure-ad-directory-for-federation"></a>Konfigurace domény v adresáři Azure AD pro federaci


1. Připojte se k adresáři služby Azure AD jako správce klienta:

  ```powershell
  Connect-MsolService
  ```
  
2. Nakonfigurujte požadovanou doménu Microsoft 365 pro použití federace s SAML 2,0:

  ```powershell
  $dom = "contoso.com" 
  $BrandName - "Sample SAML 2.0 IDP" 
  $LogOnUrl = "https://WS2012R2-0.contoso.com/passiveLogon" 
  $LogOffUrl = "https://WS2012R2-0.contoso.com/passiveLogOff" 
  $ecpUrl = "https://WS2012R2-0.contoso.com/PAOS" 
  $MyURI = "urn:uri:MySamlp2IDP" 
  $MySigningCert = "MIIC7jCCAdagAwIBAgIQRrjsbFPaXIlOG3GTv50fkjANBgkqhkiG9w0BAQsFADAzMTEwLwYDVQQDEyh BREZTIFNpZ25pbmcgLSBXUzIwMTJSMi0wLnN3aW5mb3JtZXIuY29tMB4XDTE0MDEyMDE1MTY0MFoXDT E1MDEyMDE1MTY0MFowMzExMC8GA1UEAxMoQURGUyBTaWduaW5nIC0gV1MyMDEyUjItMC5zd2luZm9yb WVyLmNvbTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAKe+rLVmXy1QwCwZwqgbbp1/kupQ VcjKuKLitVDbssFyqbDTjP7WRjlVMWAHBI3kgNT7oE362Gf2WMJFf1b0HcrsgLin7daRXpq4Qi6OA57 sW1YFMj3sqyuTP0eZV3S4+ZbDVob6amsZIdIwxaLP9Zfywg2bLsGnVldB0+XKedZwDbCLCVg+3ZWxd9 T/jV0hpLIIWr+LCOHqq8n8beJvlivgLmDJo8f+EITnAxWcsJUvVai/35AhHCUq9tc9sqMp5PWtabAEM b2AU72/QlX/72D2/NbGQq1BWYbqUpgpCZ2nSgvlWDHlCiUo//UGsvfox01kjTFlmqQInsJVfRxF5AcC AwEAATANBgkqhkiG9w0BAQsFAAOCAQEAi8c6C4zaTEc7aQiUgvnGQgCbMZbhUXXLGRpjvFLKaQzkwa9 eq7WLJibcSNyGXBa/SfT5wJgsm3TPKgSehGAOTirhcqHheZyvBObAScY7GOT+u9pVYp6raFrc7ez3c+ CGHeV/tNvy1hJNs12FYH4X+ZCNFIT9tprieR25NCdi5SWUbPZL0tVzJsHc1y92b2M2FxqRDohxQgJvy JOpcg2mSBzZZIkvDg7gfPSUXHVS1MQs0RHSbwq/XdQocUUhl9/e/YWCbNNxlM84BxFsBUok1dH/gzBy Sx+Fc8zYi7cOq9yaBT3RLT6cGmFGVYZJW4FyhPZOCLVNsLlnPQcX3dDg9A==" 
  $uri = "http://WS2012R2-0.contoso.com/adfs/services/trust" 
  $Protocol = "SAMLP" 
  Set-MsolDomainAuthentication `
    -DomainName $dom `
    -FederationBrandName $BrandName `
    -Authentication Federated `
    -PassiveLogOnUri $LogOnUrl `
    -ActiveLogOnUri $ecpUrl `
    -SigningCertificate $MySigningCert `
    -IssuerUri $MyURI `
    -LogOffUri $LogOffUrl `
    -PreferredAuthenticationProtocol $Protocol
  ``` 

3.  V souboru metadat IDP můžete získat řetězec kódovaný pomocí kódování Base64 pro podpisový certifikát. Příklad tohoto umístění byl poskytnut, ale může se mírně lišit podle vaší implementace.

  ```xml
  <IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
    <KeyDescriptor use="signing">
      <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
       <X509Data>
         <X509Certificate> MIIC5jCCAc6gAwIBAgIQLnaxUPzay6ZJsC8HVv/QfTANBgkqhkiG9w0BAQsFADAvMS0wKwYDVQQDEyRBREZTIFNpZ25pbmcgLSBmcy50ZWNobGFiY2VudHJhbC5vcmcwHhcNMTMxMTA0MTgxMzMyWhcNMTQxMTA0MTgxMzMyWjAvMS0wKwYDVQQDEyRBREZTIFNpZ25pbmcgLSBmcy50ZWNobGFiY2VudHJhbC5vcmcwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQCwMdVLTr5YTSRp+ccbSpuuFeXMfABD9mVCi2wtkRwC30TIyPdORz642MkurdxdPCWjwgJ0HW6TvXwcO9afH3OC5V//wEGDoNcI8PV4enCzTYFe/h//w51uqyv48Fbb3lEXs+aVl8155OAj2sO9IX64OJWKey82GQWK3g7LfhWWpp17j5bKpSd9DBH5pvrV+Q1ESU3mx71TEOvikHGCZYitEPywNeVMLRKrevdWI3FAhFjcCSO6nWDiMqCqiTDYOURXIcHVYTSof1YotkJ4tG6mP5Kpjzd4VQvnR7Pjb47nhIYG6iZ3mR1F85Ns9+hBWukQWNN2hcD/uGdPXhpdMVpBAgMBAAEwDQYJKoZIhvcNAQELBQADggEBAK7h7jF7wPzhZ1dPl4e+XMAr8I7TNbhgEU3+oxKyW/IioQbvZVw1mYVCbGq9Rsw4KE06eSMybqHln3w5EeBbLS0MEkApqHY+p68iRpguqa+W7UHKXXQVgPMCpqxMFKonX6VlSQOR64FgpBme2uG+LJ8reTgypEKspQIN0WvtPWmiq4zAwBp08hAacgv868c0MM4WbOYU0rzMIR6Q+ceGVRImlCwZ5b7XKp4mJZ9hlaRjeuyVrDuzBkzROSurX1OXoci08yJvhbtiBJLf3uPOJHrhjKRwIt2TnzS9ElgFZlJiDIA26Athe73n43CT0af2IG6yC7e6sK4L3NEXJrwwUZk=</X509Certificate>
        </X509Data>
      </KeyInfo>
    </KeyDescriptor>
  </IDPSSODescriptor>
  ``` 

Další informace o příkazu set-MsolDomainAuthentication najdete v tématu: [/Previous-Versions/Azure/dn194112 (v = Azure. 100)](/previous-versions/azure/dn194112(v=azure.100)).

>[!NOTE]
>Je nutné použít `$ecpUrl = "https://WS2012R2-0.contoso.com/PAOS"` pouze v případě, že jste pro poskytovatele identity nastavili rozšíření ECP. Klienti Exchange Online, kromě webové aplikace Outlook (OWA), spoléhají na POST aktivní koncový bod založený na příspěvku. Pokud vaše služba SAML 2,0 STS implementuje aktivní koncový bod podobný implementaci Shibboleth ECP aktivního koncového bodu, může být pro tyto bohatých klientů možné pracovat se službou Exchange Online.

Po nakonfigurování federace můžete přejít zpátky na "nefederované" (nebo "spravované"), ale tato změna může trvat až dvě hodiny a vyžaduje přiřazení nových náhodných hesel pro cloudové přihlašování každému uživateli. V některých scénářích může být pro resetování chyby v nastavení vyžadováno přepnutí zpět na spravovanou. Další informace o převodu domény najdete v tématu: [/Previous-Versions/Azure/dn194122 (v = Azure. 100)](/previous-versions/azure/dn194122(v=azure.100)).

## <a name="provision-user-principals-to-azure-ad--microsoft-365"></a>Zřizování objektů zabezpečení uživatele pro Azure AD/Microsoft 365
Než budete moct ověřit uživatele a Microsoft 365, musíte zřídit Azure AD s uživatelskými objekty zabezpečení, které odpovídají kontrolnímu výrazu v deklaraci SAML 2,0. Pokud tyto hlavní uživatelské objekty nejsou v Azure AD předem známé, nelze je použít pro federované přihlašování. K zajištění objektů zabezpečení uživatele lze použít buď Azure AD Connect, nebo prostředí Windows PowerShell.

Azure AD Connect můžete použít ke zřízení objektů zabezpečení domén v adresáři služby Azure AD z místní služby Active Directory. Podrobnější informace najdete v tématu [Integrace místních adresářů s Azure Active Directory](whatis-hybrid-identity.md).

Prostředí Windows PowerShell můžete také použít k automatizaci přidání nových uživatelů do služby Azure AD a synchronizaci změn z místního adresáře. Pokud chcete používat rutiny Windows PowerShellu, musíte si stáhnout [Azure Active Directory moduly](/powershell/azure/active-directory/install-adv2).

Tento postup ukazuje, jak přidat jednoho uživatele do služby Azure AD.


1. Připojte se k adresáři Azure AD jako správce tenanta: Connect-MsolService.
2. Vytvořit nový objekt zabezpečení uživatele:

    ```powershell
    New-MsolUser `
      -UserPrincipalName elwoodf1@contoso.com `
      -ImmutableId ABCDEFG1234567890 `
      -DisplayName "Elwood Folk" `
      -FirstName Elwood `
      -LastName Folk `
      -AlternateEmailAddresses "Elwood.Folk@contoso.com" `
      -LicenseAssignment "samlp2test:ENTERPRISEPACK" `
      -UsageLocation "US" 
    ```

Další informace o registraci New-MsolUser, [/Previous-Versions/Azure/dn194096 (v = Azure. 100)](/previous-versions/azure/dn194096(v=azure.100))

>[!NOTE]
>Hodnota "UserPrincipalName" se musí shodovat s hodnotou, kterou odešlete za "IDPEmail" v deklaraci SAML 2,0 a hodnota "ImmutableID" se musí shodovat s hodnotou odeslanou v kontrolním výrazu "NameID".

## <a name="verify-single-sign-on-with-your-saml-20-idp"></a>Ověření jednotného přihlašování s využitím SAML 2,0 IDP
Než ověříte a spravujete jednotné přihlašování (označované také jako federace identit), zkontrolujte informace a proveďte kroky v následujících článcích a nastavte jednotné přihlašování s vaším poskytovatelem identity založeném na SP-Lite SAML 2,0:

1. Zkontrolovali jste požadavky na protokol SAML 2,0 pro Azure AD.
2. Nakonfigurovali jste poskytovatele identity SAML 2,0.
3. Instalace prostředí Windows PowerShell pro jednotné přihlašování pomocí zprostředkovatele identity SAML 2,0
4. Nastavení vztahu důvěryhodnosti mezi poskytovatelem služeb SAML 2,0 a službou Azure AD
5. Byl zřízen známý objekt zabezpečení testovacího uživatele pro Azure Active Directory (Microsoft 365) buď pomocí prostředí Windows PowerShell nebo Azure AD Connect.
6. Nakonfigurujte synchronizaci adresářů pomocí [Azure AD Connect](whatis-hybrid-identity.md).

Po nastavení jednotného přihlašování pomocí poskytovatele identity založeného na SP-Lite SAML 2,0 byste měli ověřit, že funguje správně.

>[!NOTE]
>Pokud jste převedli doménu místo přidání, může to trvat až 24 hodin, než se nastaví jednotné přihlašování.
Před ověřením jednotného přihlašování byste měli dokončit nastavení synchronizace služby Active Directory, synchronizovat adresáře a aktivovat synchronizované uživatele.

### <a name="use-the-tool-to-verify-that-single-sign-on-has-been-set-up-correctly"></a>Použijte nástroj k ověření, že jednotné přihlašování bylo nastaveno správně.
Pokud chcete ověřit, že se jednotné přihlašování správně nastavilo, můžete pomocí následujícího postupu ověřit, jestli se můžete přihlásit ke cloudové službě pomocí podnikových přihlašovacích údajů.

Společnost Microsoft poskytuje nástroj, který můžete použít k otestování poskytovatele identity založeného na SAML 2,0. Před spuštěním testovacího nástroje musíte nakonfigurovat tenanta Azure AD tak, aby federovat s vaším poskytovatelem identity.

>[!NOTE]
>Analyzátor připojení vyžaduje Internet Explorer 10 nebo novější.



1. Stáhněte si [analyzátor připojení](https://testconnectivity.microsoft.com/?tabid=Client).
2. Kliknutím na Instalovat nyní začněte stahovat a instalovat nástroj.
3. Vyberte nemůžu nastavit federaci s Office 365, Azure nebo jinými službami, které používají Azure Active Directory.
4. Po stažení a spuštění nástroje se zobrazí okno Diagnostika připojení. Nástroj vás provede testováním federačního připojení.
5. Analyzátor připojení otevře IDP SAML 2,0, abyste se mohli přihlásit, zadejte přihlašovací údaje pro objekt zabezpečení uživatele, který testujete:

    ![Snímek obrazovky zobrazující okno pro přihlášení pro váš IDP SAML 2,0.](./media/how-to-connect-fed-saml-idp/saml1.png)

6.  V okně přihlášení k testu federace byste měli zadat název účtu a heslo pro tenanta Azure AD, které je nakonfigurované tak, aby byly federované s vaším poskytovatelem identity SAML 2,0. Nástroj se pokusí přihlásit pomocí těchto přihlašovacích údajů a podrobné výsledky testů provedených během pokusu o přihlášení budou poskytnuty jako výstup.

    ![SAML](./media/how-to-connect-fed-saml-idp/saml2.png)

7. V tomto okně se zobrazuje neúspěšný výsledek testování. Kliknutím na zkontrolovat podrobné výsledky zobrazíte informace o výsledcích pro každý provedený test. Výsledky můžete také uložit na disk, aby je bylo možné sdílet.
 
> [!NOTE]
> Analyzátor připojení také testuje aktivní federaci pomocí protokolů WS-based a ECP/PAOS. Pokud tyto chyby nepoužíváte, můžete ignorovat následující chybu: testování aktivního procesu přihlašování pomocí koncového bodu aktivní federace poskytovatele identity.

### <a name="manually-verify-that-single-sign-on-has-been-set-up-correctly"></a>Ruční ověření, jestli se jednotné přihlašování správně nastavilo

Ruční ověření poskytuje další kroky, které můžete provést, abyste zajistili, že váš poskytovatel identity SAML 2,0 funguje správně v mnoha scénářích.
Chcete-li ověřit, zda bylo jednotné přihlašování nastaveno správně, proveďte následující kroky:

1. Na počítači připojeném k doméně se přihlaste ke cloudové službě pomocí stejného přihlašovacího jména, které používáte pro vaše podnikové přihlašovací údaje.
2. Klikněte do pole heslo. Pokud je nastaveno jednotné přihlašování, bude pole heslo ve stínovém zobrazení šedé a zobrazí se následující zpráva: "nyní se budete muset přihlásit ve &lt; vaší společnosti &gt; ."
3. Klikněte na přihlášení na &lt; svém firemním &gt; odkazu. Pokud se můžete přihlásit, pak bylo nastaveno jednotné přihlašování.

## <a name="next-steps"></a>Další kroky

- [Active Directory Federation Services (AD FS) Správa a přizpůsobení pomocí Azure AD Connect](how-to-connect-fed-management.md)
- [Seznam kompatibilit pro federaci Azure AD](how-to-connect-fed-compatibility.md)
- [Azure AD Connect vlastní instalaci](how-to-connect-install-custom.md)
