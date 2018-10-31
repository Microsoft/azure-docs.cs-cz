---
title: 'Azure AD Connect: Použití zprostředkovatele Identity SAML 2.0 pro jednotné přihlašování na | Dokumentace Microsoftu'
description: Tento dokument popisuje na vyhovující Idp SAML 2.0 pro jednotné přihlašování.
services: active-directory
author: billmath
manager: mtillman
ms.custom: it-pro
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: a197b3c7aa423323f984a793851a458733391e33
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50243108"
---
#  <a name="use-a-saml-20-identity-provider-idp-for-single-sign-on"></a>Použití zprostředkovatele 2.0 Identity (IdP) SAML pro jednotné přihlašování na

Tento dokument obsahuje informace o použití SAML 2.0 kompatibilní SP Lite na základě profilu zprostředkovatele Identity jako upřednostňované tokenů zabezpečení služby (STS) / zprostředkovatele identity. Tento scénář je vhodný v případě, že už máte adresář uživatele a heslo ukládat místně, které lze přistupovat pomocí SAML 2.0. Tento existující adresář uživatele je možné pro přihlašování k Office 365 a další AD zabezpečené prostředky Azure. Profil SAML 2.0 SP-Lite je založena na standardu běžně používaných federovaných identit zabezpečení kontrolního výrazu SAML (Markup Language) poskytuje architekturu exchange přihlašování a atribut.

>[!NOTE]
>Seznam 3. stran zprostředkovatelů identity, které se testovaly pro použití se službou Azure AD najdete v článku [seznam kompatibilit pro federaci Azure AD](how-to-connect-fed-compatibility.md)

Společnost Microsoft podporuje tyto možnosti přihlašování jako integrace cloudové služby Microsoftu, jako je Office 365 pomocí svého správně nakonfigurovaný protokol SAML 2.0 na základě profilu zprostředkovatele identity. Zprostředkovatelé identity SAML 2.0 jsou produkty třetích stran a proto společnost Microsoft neposkytuje podporu pro nasazení, konfiguraci, řešení potíží s osvědčené postupy týkající se jejich. Jednou správně nakonfigurována integrace pomocí SAML 2.0 zprostředkovatele identity může být testovány z hlediska správnou konfiguraci s použitím nástroje Microsoft připojení analyzátor, který je podrobněji popsaný níže. Další informace o zprostředkovateli identity na základě profilu SAML 2.0 SP-Lite požádejte organizaci, který je zadán.

>[!IMPORTANT]
>Jen omezenou sadou klientů jsou k dispozici v tomto scénáři přihlašování pomocí zprostředkovatelů identity SAML 2.0, to zahrnuje:

>- Webové klienty, například Outlook Web Access a Sharepointu Online
- E-mailu ve formátu RTF, které používají základní ověřování a podporovaná metoda přístupu serveru Exchange jako je například IMAP, POP, Active Sync, MAPI, atd. (je vyžadována k nasazení rozšířeného klientský protokol koncového bodu), včetně:
    - Aplikace Microsoft Outlook 2010, Outlook 2013/Outlook 2016, iPhone Apple (různé verze iOS)
    - Různá zařízení Google Android
    - Windows Phone 8.0, Windows Phone 7 a Windows Phone 7.8
    - E-mailového klienta systému Windows 8 a Windows 8.1 e-mailového klienta
    - E-mailového klienta Windows 10

Všechny ostatní klienti nejsou k dispozici v tomto scénáři přihlášení pomocí zprostředkovatele Identity SAML 2.0. Například klient klasické pracovní plochy Lync 2010 není moct přihlásit ke službě pomocí zprostředkovatele Identity SAML 2.0 pro jednotné přihlašování nakonfigurované.

## <a name="azure-ad-saml-20-protocol-requirements"></a>Požadavky protokolu Azure AD SAML 2.0
Tento dokument obsahuje podrobné požadavky na protokol a zprávu formátování, musí implementovat vašeho zprostředkovatele identity SAML 2.0 pro vytvoření federace s Azure AD povolit přihlášení k jedné nebo více cloudovým službám Microsoftu (jako je Office 365). Přijímající strany SAML 2.0 (SP služby tokenů zabezpečení) pro cloudové služby Microsoftu používat v tomto scénáři je Azure AD.

Doporučuje se, že zajistíte zprostředkovatele identity SAML 2.0 výstupní zprávy být možno podobný připravená ukázková trasování. Navíc použít konkrétní atribut hodnoty ze zadané služby Azure AD metadat tam, kde je to možné. Jakmile budete spokojeni s výstupní zprávy, můžete otestovat s Microsoft Connectivity Analyzer jak je popsáno níže.

Metadata služby Azure AD si můžete stáhnout z této adresy URL: [ https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml ](https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml).
Pro zákazníky v Číně pomocí instance specifické pro Čínu – Office 365, je třeba použít následující koncový bod federace: [ https://nexus.partner.microsoftonline-p.cn/federationmetadata/saml20/federationmetadata.xml ](https://nexus.partner.microsoftonline-p.cn/federationmetadata/saml20/federationmetadata.xml).

## <a name="saml-protocol-requirements"></a>Požadavky protokolu SAML
Tato část Podrobnosti jak páry zprávy požadavků a odpovědí jsou umístěny společně v pořadí při formátování zprávy správně.

Azure AD může být nakonfigurovány pro práci s zprostředkovatelů identity, které používají SAML 2.0 SP Lite profil s některé konkrétní požadavky, jak je uvedeno níže. Pomocí ukázky SAML žádostí a odpovědí zprávy spolu s automatizované a manuální testování, můžete pracovat v dosažení interoperability s Azure AD.

## <a name="signature-block-requirements"></a>Signatura bloku požadavky
Ve zprávě odpověď SAML uzel podpis obsahuje informace o digitální podpis pro vlastní zprávě. Blok signatury má následující požadavky:

1. Samotný uzel výrazu musí být podepsané.
2.  Jako metoda DigestMethod ještě musí použije algoritmus RSA sha1. Jiné algoritmy digitální podpis nepřijmou.
   `<ds:DigestMethod Algorithm="http://www.w3.org/2000/09/xmldsig#sha1"/>`
3.  Může také podepsat dokument XML. 
4.  Algoritmus transformace musí odpovídat hodnotám v následující ukázce:    `<ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature"/>
       <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#"/>`
9.  Algoritmus SignatureMethod musí odpovídala následující ukázce:   `<ds:SignatureMethod Algorithm="http://www.w3.org/2000/09/xmldsig#rsa-sha1"/>`

## <a name="supported-bindings"></a>Podporované vazby
Vazby jsou parametry zpráv týkající se přenosu, které jsou požadovány. Následující požadavky platí pro vazby

1. HTTPS je požadovaná přenosu.
2.  Azure AD bude vyžadovat HTTP POST pro token odeslání během přihlášení
3.  Azure AD použije HTTP POST pro požadavek na ověření pro zprostředkovatele identity a PŘESMĚROVÁNÍ pro zprávy odhlášení zprostředkovatele identity.

## <a name="required-attributes"></a>Vyžadované atributy
Tato tabulka uvádí požadavky pro konkrétní atributy ve zprávě SAML 2.0.
 
|Atribut|Popis|
| ----- | ----- |
|NameID|Hodnota tohoto výrazu musí být stejný jako ImmutableID uživatele Azure AD. Může být až 64 alfanumerické znaky. Všechny znaky bezpečné jiného typu než html musí kódováním, například znak "+" se zobrazí jako ".2B".|
|IDPEmail|Hlavní název uživatele (UPN) je uveden v odpověď SAML jako element s názvem IDPEmail UserPrincipalName daného uživatele (UPN) v Azure AD nebo Office 365. Hlavní název uživatele je ve formátu e-mailové adresy. Hodnotu hlavního názvu uživatele v Office 365 Windows (Azure Active Directory).|
|Vystavitel|Musí být identifikátor URI zprostředkovatele identity. Nepoužívejte opakovaně vystavitele z ukázkové zprávy. Pokud máte více domén nejvyšší úrovně ve vaší tenantů Azure AD vystavitele musí odpovídat zadaný identifikátor URI nastavení nakonfigurované na doménu.|

>[!IMPORTANT]
>Azure AD aktuálně podporuje následující identifikátor URI formátu NameID SAML 2.0:urn:oasis:names:tc:SAML:2.0:nameid-formátu: trvalé.

## <a name="sample-saml-request-and-response-messages"></a>Ukázka zprávy požadavků a odpovědí SAML
K výměně zpráv přihlášení se zobrazí zpráva pár požadavků a odpovědí.
Tady je ukázková zpráva požadavku, odesílané ze služby Azure AD do ukázkového poskytovatele identity SAML 2.0. Ukázka zprostředkovatele identity SAML 2.0 je Active Directory Federation Services (ADFS) nakonfigurovaný tak, aby používat protokol SAML-P. Vzájemná funkční spolupráce testování také bylo dokončeno s ostatními zprostředkovateli identity SAML 2.0.

    `<samlp:AuthnRequest xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol" xmlns:saml="urn:oasis:names:tc:SAML:2.0:assertion" ID="_7171b0b2-19f2-4ba2-8f94-24b5e56b7f1e" IssueInstant="2014-01-30T16:18:35Z" Version="2.0" AssertionConsumerServiceIndex="0" >
    <saml:Issuer>urn:federation:MicrosoftOnline</saml:Issuer>
    <samlp:NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
    </samlp:AuthnRequest>`

Tady je ukázková zpráva odpovědi, odeslané ze zprostředkovatele kompatibilní identity SAML 2.0 ukázky do služby Azure AD nebo Office 365.

    `<samlp:Response ID="_592c022f-e85e-4d23-b55b-9141c95cd2a5" Version="2.0" IssueInstant="2014-01-31T15:36:31.357Z" Destination="https://login.microsoftonline.com/login.srf" Consent="urn:oasis:names:tc:SAML:2.0:consent:unspecified" InResponseTo="_049917a6-1183-42fd-a190-1d2cbaf9b144" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">http://WS2012R2-0.contoso.com/adfs/services/trust</Issuer>
    <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
    </samlp:Status>
    <Assertion ID="_7e3c1bcd-f180-4f78-83e1-7680920793aa" IssueInstant="2014-01-31T15:36:31.279Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">
    <Issuer>http://WS2012R2-0.contoso.com/adfs/services/trust</Issuer>
    <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
      <ds:SignedInfo>
        <ds:CanonicalizationMethod Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
        <ds:SignatureMethod Algorithm="http://www.w3.org/2000/09/xmldsig#rsa-sha1" />
        <ds:Reference URI="#_7e3c1bcd-f180-4f78-83e1-7680920793aa">
          <ds:Transforms>
            <ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature" />
            <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
          </ds:Transforms>
          <ds:DigestMethod Algorithm="http://www.w3.org/2000/09/xmldsig#sha1" />
          <ds:DigestValue>CBn/5YqbheaJP425c0pHva9PhNY=</ds:DigestValue>
        </ds:Reference>
      </ds:SignedInfo>
      <ds:SignatureValue>TciWMyHW2ZODrh/2xrvp5ggmcHBFEd9vrp6DYXp+hZWJzmXMmzwmwS8KNRJKy8H7XqBsdELA1Msqi8I3TmWdnoIRfM/ZAyUppo8suMu6Zw+boE32hoQRnX9EWN/f0vH6zA/YKTzrjca6JQ8gAV1ErwvRWDpyMcwdYCiWALv9ScbkAcebOE1s1JctZ5RBXggdZWrYi72X+I4i6WgyZcIGai/rZ4v2otoWAEHS0y1yh1qT7NDPpl/McDaTGkNU6C+8VfjD78DrUXEcAfKvPgKlKrOMZnD1lCGsViimGY+LSuIdY45MLmyaa5UT4KWph6dA==</ds:SignatureValue>
      <KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
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
    </samlp:Response>`

## <a name="configure-your-saml-20-compliant-identity-provider"></a>Konfigurace zprostředkovatele identity kompatibilní SAML 2.0
Tato část obsahuje pokyny ke konfiguraci vašeho zprostředkovatele identity SAML 2.0 pro vytvoření federace s Azure AD umožňuje přístup jednotné přihlašování na jeden nebo více cloudovým službám Microsoftu (jako je Office 365) pomocí protokolu SAML 2.0. Protokol SAML 2.0 předávající stranu pro cloudové služby Microsoftu používat v tomto scénáři je Azure AD.

## <a name="add-azure-ad-metadata"></a>Přidání metadat služby Azure AD
Zprostředkovatele identity SAML 2.0 je potřeba dodržovat informace o službě Azure AD předávající strany. Azure AD publikuje metadata na https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml.

Doporučujeme při konfiguraci vašeho zprostředkovatele identity SAML 2.0 vždy importovat metadata nejnovější služby Azure AD.

>[!NOTE]
>Azure AD nepodporuje čtení metadat ze zprostředkovatele identity.

## <a name="add-azure-ad-as-a-relying-party"></a>Přidat jako přijímající strany Azure AD
Je nutné povolit komunikaci mezi zprostředkovatele identity SAML 2.0 a Azure AD. Tato konfigurace bude závisí na zprostředkovateli konkrétní identitu a by měla odkazovat na dokumentaci pro něj. By obvykle nastavíte Identifikátor předávající strany na stejné jako entityID z metadat služby Azure AD.

>[!NOTE]
>Ověřte, že hodiny na serveru zprostředkovatele identity SAML 2.0 synchronizované se zdrojem přesnému času. Nesprávné čas může způsobit selhání federované přihlášení.

## <a name="install-windows-powershell-for-sign-on-with-saml-20-identity-provider"></a>Instalace prostředí Windows PowerShell pro přihlášení pomocí zprostředkovatele identity SAML 2.0
Po nakonfigurování zprostředkovatele identity SAML 2.0 pro použití s přihlašováním k Azure AD, dalším krokem je ke stažení a instalaci Azure Active Directory modulu pro Windows PowerShell. Po instalaci budete používat tyto rutiny ke konfiguraci domény Azure AD jako federovaných domén.

Sada Azure Active Directory modulu pro Windows PowerShell je ke stažení pro správu dat organizace ve službě Azure AD. Tento modul nainstaluje sadu rutin Windows powershellu; Spusťte tyto rutiny k nastavení přístupu jednotné přihlašování do služby Azure AD a v důsledku ke všem cloudovým službám jste se nepřihlásili k odběru. Pokyny o tom, jak stáhnout a nainstalovat nejnovější verzi rutin najdete v tématu [http://technet.microsoft.com/library/jj151815.aspx](http://technet.microsoft.com/library/jj151815.aspx)

## <a name="set-up-a-trust-between-your-saml-identity-provider-and-azure-ad"></a>Nastavení vztahu důvěryhodnosti mezi SAML zprostředkovatele identity a Azure AD
Před konfigurací federace pro doménu služby Azure AD, musí mít v konfiguraci vlastní domény. Nelze vytvořit federaci výchozí doménu, kterou zajišťuje Microsoft. Výchozí doménu od Microsoftu končí řetězcem "onmicrosoft.com".
V rozhraní příkazového řádku Windows Powershellu k přidání nebo převést domény pro jednotné přihlašování se spustit sérii rutin.

Každé doméně Azure Active Directory, kterou chcete Federovat pomocí zprostředkovatele identity SAML 2.0 musí být buď přidán jako jediná doména přihlašování nebo převést jako jednotné přihlašování domény ze standardní domény. Přidáním nebo převedením domény vytvoří vztah důvěryhodnosti mezi zprostředkovatele identity SAML 2.0 a Azure AD.

Následující postup vás provede převod existující standardní domény na federovanou doménu pomocí SAML 2.0 SP-Lite. 

>[!NOTE]
>Vaše doména může dojít k výpadku, který ovlivňuje uživatelů až do 2 hodin po provedení tohoto kroku.

## <a name="configuring-a-domain-in-your-azure-ad-directory-for-federation"></a>Konfigurace domény v adresáři Azure AD pro federaci


1. Připojení k adresáři Azure AD jako správce tenanta: Connect-MsolService.
2.  Nakonfigurujte požadované domény Office 365 do federace pomocí SAML 2.0: `$dom = "contoso.com" $BrandName - "Sample SAML 2.0 IDP" $LogOnUrl = "https://WS2012R2-0.contoso.com/passiveLogon" $LogOffUrl = "https://WS2012R2-0.contoso.com/passiveLogOff" $ecpUrl = "https://WS2012R2-0.contoso.com/PAOS" $MyURI = "urn:uri:MySamlp2IDP" $MySigningCert = @" MIIC7jCCAdagAwIBAgIQRrjsbFPaXIlOG3GTv50fkjANBgkqhkiG9w0BAQsFADAzMTEwLwYDVQQDEyh BREZTIFNpZ25pbmcgLSBXUzIwMTJSMi0wLnN3aW5mb3JtZXIuY29tMB4XDTE0MDEyMDE1MTY0MFoXDT E1MDEyMDE1MTY0MFowMzExMC8GA1UEAxMoQURGUyBTaWduaW5nIC0gV1MyMDEyUjItMC5zd2luZm9yb WVyLmNvbTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAKe+rLVmXy1QwCwZwqgbbp1/kupQ VcjKuKLitVDbssFyqbDTjP7WRjlVMWAHBI3kgNT7oE362Gf2WMJFf1b0HcrsgLin7daRXpq4Qi6OA57 sW1YFMj3sqyuTP0eZV3S4+ZbDVob6amsZIdIwxaLP9Zfywg2bLsGnVldB0+XKedZwDbCLCVg+3ZWxd9 T/jV0hpLIIWr+LCOHqq8n8beJvlivgLmDJo8f+EITnAxWcsJUvVai/35AhHCUq9tc9sqMp5PWtabAEM b2AU72/QlX/72D2/NbGQq1BWYbqUpgpCZ2nSgvlWDHlCiUo//UGsvfox01kjTFlmqQInsJVfRxF5AcC AwEAATANBgkqhkiG9w0BAQsFAAOCAQEAi8c6C4zaTEc7aQiUgvnGQgCbMZbhUXXLGRpjvFLKaQzkwa9 eq7WLJibcSNyGXBa/SfT5wJgsm3TPKgSehGAOTirhcqHheZyvBObAScY7GOT+u9pVYp6raFrc7ez3c+ CGHeV/tNvy1hJNs12FYH4X+ZCNFIT9tprieR25NCdi5SWUbPZL0tVzJsHc1y92b2M2FxqRDohxQgJvy JOpcg2mSBzZZIkvDg7gfPSUXHVS1MQs0RHSbwq/XdQocUUhl9/e/YWCbNNxlM84BxFsBUok1dH/gzBy Sx+Fc8zYi7cOq9yaBT3RLT6cGmFGVYZJW4FyhPZOCLVNsLlnPQcX3dDg9A==" "@ $uri = "http://WS2012R2-0.contoso.com/adfs/services/trust" $Protocol = "SAMLP" Set-MsolDomainAuthentication -DomainName $dom -FederationBrandName $dom -Authentication Federated -PassiveLogOnUri $MyURI -ActiveLogOnUri $ecpUrl -SigningCertificate $MySigningCert -IssuerUri $uri -LogOffUri $url -PreferredAuthenticationProtocol $Protocol` 

3.  Můžete získat řetězec pro kódování base64 podpisového certifikátu ze souboru metadat zprostředkovatele identity. Příkladem tohoto umístění neposkytl se ale může mírně lišit v závislosti na vaší implementace.

    `<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol"> <KeyDescriptor use="signing"> <KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#"> <X509Data> <X509Certificate>MIIC5jCCAc6gAwIBAgIQLnaxUPzay6ZJsC8HVv/QfTANBgkqhkiG9w0BAQsFADAvMS0wKwYDVQQDEyRBREZTIFNpZ25pbmcgLSBmcy50ZWNobGFiY2VudHJhbC5vcmcwHhcNMTMxMTA0MTgxMzMyWhcNMTQxMTA0MTgxMzMyWjAvMS0wKwYDVQQDEyRBREZTIFNpZ25pbmcgLSBmcy50ZWNobGFiY2VudHJhbC5vcmcwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQCwMdVLTr5YTSRp+ccbSpuuFeXMfABD9mVCi2wtkRwC30TIyPdORz642MkurdxdPCWjwgJ0HW6TvXwcO9afH3OC5V//wEGDoNcI8PV4enCzTYFe/h//w51uqyv48Fbb3lEXs+aVl8155OAj2sO9IX64OJWKey82GQWK3g7LfhWWpp17j5bKpSd9DBH5pvrV+Q1ESU3mx71TEOvikHGCZYitEPywNeVMLRKrevdWI3FAhFjcCSO6nWDiMqCqiTDYOURXIcHVYTSof1YotkJ4tG6mP5Kpjzd4VQvnR7Pjb47nhIYG6iZ3mR1F85Ns9+hBWukQWNN2hcD/uGdPXhpdMVpBAgMBAAEwDQYJKoZIhvcNAQELBQADggEBAK7h7jF7wPzhZ1dPl4e+XMAr8I7TNbhgEU3+oxKyW/IioQbvZVw1mYVCbGq9Rsw4KE06eSMybqHln3w5EeBbLS0MEkApqHY+p68iRpguqa+W7UHKXXQVgPMCpqxMFKonX6VlSQOR64FgpBme2uG+LJ8reTgypEKspQIN0WvtPWmiq4zAwBp08hAacgv868c0MM4WbOYU0rzMIR6Q+ceGVRImlCwZ5b7XKp4mJZ9hlaRjeuyVrDuzBkzROSurX1OXoci08yJvhbtiBJLf3uPOJHrhjKRwIt2TnzS9ElgFZlJiDIA26Athe73n43CT0af2IG6yC7e6sK4L3NEXJrwwUZk=</X509Certificate> </X509Data> </KeyInfo> </KeyDescriptor>` 

Další informace o "Set-MsolDomainAuthentication" v tématu: [ http://technet.microsoft.com/library/dn194112.aspx ](http://technet.microsoft.com/library/dn194112.aspx).

>[!NOTE]
>Je nutné spustit pomocí "$ecpUrl ="https://WS2012R2-0.contoso.com/PAOS"" pouze v případě, že nastavíte ECP rozšíření pro zprostředkovatele identity. Exchange Online, s výjimkou Outlook Web Application (OWA), klienty na základě příspěvek aktivní koncový bod. Pokud váš protokol SAML 2.0 STS implementuje active koncový bod podobná společnosti Shibboleth ECP provádění aktivní koncový bod je možné, že pro tyto bohatých klientů k interakci se službou Exchange Online.

Jakmile federation není nakonfigurovaná můžete přepnout zpět na "nefederovaných" (nebo "spravovaný"), ale tato změna trvá až dvě hodiny a vyžaduje přiřazení nové náhodná hesla pro cloudové přihlašování k jednotlivým uživatelům. Přepnutí zpět do "spravovaný" může být nutné v některých scénářích resetování chybu v nastavení. Další informace o převodu domény naleznete v tématu: [ http://msdn.microsoft.com/library/windowsazure/dn194122.aspx ](http://msdn.microsoft.com/library/windowsazure/dn194122.aspx).

## <a name="provision-user-principals-to-azure-ad--office-365"></a>Zřízení objekty zabezpečení uživatelů do služby Azure AD nebo Office 365
Předtím, než můžete ověřovat uživatele pro Office 365, je třeba zřídit služby Azure AD s objekty zabezpečení uživatelů, které odpovídají kontrolního výrazu v deklaraci identity SAML 2.0. Pokud jsou tyto objekty zabezpečení uživatelů nejsou známé do služby Azure AD předem, nelze použít pro federované přihlášení. Azure AD Connect nebo prostředí Windows PowerShell lze použít ke zřízení objekty zabezpečení uživatelů.

Azure AD Connect je možné zřídit objekty zabezpečení do domén v adresáři Azure AD z místní služby Active Directory. Další informace najdete v tématu [integrace místních adresářů se službou Azure Active Directory](whatis-hybrid-identity.md).

Prostředí Windows PowerShell můžete použít také k automatizaci přidání nových uživatelů do služby Azure AD a synchronizace změn z místního adresáře. Pokud chcete použít rutiny Windows Powershellu, musíte stáhnout [Azure Active Directory moduly](https://docs.microsoft.com/powershell/azure/install-adv2?view=azureadps-2.0).

Tento postup ukazuje, jak přidat do služby Azure AD jednoho uživatele.


1. Připojení k adresáři Azure AD jako správce tenanta: Connect-MsolService.
2.  Vytvořte nový instanční objekt uživatele: ` New-MsolUser
        -UserPrincipalName elwoodf1@contoso.com
        -ImmutableId ABCDEFG1234567890
        -DisplayName "Elwood Folk"
        -FirstName Elwood 
        -LastName Folk 
        -AlternateEmailAddresses "Elwood.Folk@contoso.com" 
        -LicenseAssignment "samlp2test:ENTERPRISEPACK" 
        -UsageLocation "US" ` 

Další informace o rezervaci "New-MsolUser" [http://technet.microsoft.com/library/dn194096.aspx](http://technet.microsoft.com/library/dn194096.aspx)

>[!NOTE]
>Hodnota "UserPrinciplName" musí odpovídat hodnotě, kterou odešlete "IDPEmail" ve vaší deklarace identity SAML 2.0 a hodnota "ImmutableID" musí odpovídat hodnotě poslaná vaše kontrolní výraz "NameID".

## <a name="verify-single-sign-on-with-your-saml-20-idp"></a>Ověření jednotného přihlašování pomocí svého zprostředkovatele identity SAML 2.0
Jako správce než ověřit a Správa jednotného přihlašování (také nazývané federaci), přečtěte si informace a proveďte kroky v následujících článcích nastavit jednotné přihlašování pomocí SAML 2.0 SP-Lite zprostředkovatele identity na základě:


1.  Jste si prohlédli požadavky protokolu Azure AD SAML 2.0
2.  Nakonfigurovali jste zprostředkovatele identity SAML 2.0
3.  Instalace Windows Powershellu pro jednotné přihlašování pomocí zprostředkovatele identity SAML 2.0
4.  Nastavení vztahu důvěryhodnosti mezi zprostředkovatele identity SAML 2.0 a Azure AD
5.  Zřízení známý testovací hlavní název uživatele ke službě Azure Active Directory (Office 365) buď prostřednictvím Windows Powershellu nebo Azure AD Connect.
6.  Konfigurace synchronizace adresáře pomocí [Azure AD Connect](whatis-hybrid-identity.md).

Po nastavení jednotného přihlašování s vaší identitou SAML 2.0 SP-Lite podle poskytovatele, ověřte, že funguje správně.

>[!NOTE]
>Pokud jste převedli domény, nikoli přidáním jedné, může trvat až 24 hodin k nastavení jednotného přihlašování.
Než potvrdíte jednotného přihlašování, by měla dokončit nastavení synchronizace služby Active Directory, synchronizace vašich adresářů a aktivace synchronizovaných uživatelů.

### <a name="use-the-tool-to-verify-that-single-sign-on-has-been-set-up-correctly"></a>Pomocí nástroje pro ověření, že tento jednotného přihlašování se nastavila správně
Pokud chcete ověřit, že tento jednotného přihlašování je nastavený správně, můžete provést podle následujícího postupu potvrďte, že se můžete přihlásit ke cloudové službě pomocí svých firemních přihlašovacích údajů.

Microsoft poskytuje nástroje, který můžete použít k otestování vašeho zprostředkovatele identity založené na SAML 2.0. Před spuštěním nástroje test, musíte nakonfigurovat tenanta služby Azure AD, kterou chcete Federovat s zprostředkovatele identity.

>[!NOTE]
>Analyzátor připojení vyžaduje aplikaci Internet Explorer 10 nebo novější.



1. Stáhněte si Connectivity Analyzer, [ https://testconnectivity.microsoft.com/?tabid=Client ](https://testconnectivity.microsoft.com/?tabid=Client).
2.  Začněte kliknutím na tlačítko nainstalovat stahování a instalace nástroje.
3.  Vyberte "Nejde nastavit federace s Office 365, Azure nebo jiné služby, které používají Azure Active Directory".
4.  Jakmile nástroj se stáhne a spuštěná, zobrazí se okno diagnostiku připojení. Nástroj vás postupně testování připojení federace.
5.  Analyzátor připojení se otevře svého zprostředkovatele identity SAML 2.0 pro vás přihlásit, zadejte přihlašovací údaje pro testování hlavní název uživatele: ![SAML](./media/how-to-connect-fed-saml-idp/saml1.png)
6.  Federace test přihlášení okna měli byste zadat název účtu a heslo pro tenanta Azure AD, který je konfigurován pro federované pomocí zprostředkovatele identity SAML 2.0. Nástroj se pokusí přihlásit pomocí těchto přihlašovacích údajů a poskytneme vám podrobné výsledky testů, které provádí během pokusu o přihlášení jako výstup.
![SAML](./media/how-to-connect-fed-saml-idp/saml2.png)
7. Toto okno zobrazuje neúspěšných výsledků testů. Kliknutím na podrobné výsledky se zobrazí informace o výsledky pro každý test, který se provedla kontrola. Můžete také uložit výsledky na disk, aby bylo možné sdílet.
 
>[!NOTE]
>Analyzátor připojení také testy Active federace pomocí WS*-ECP/PAOS a na základě protokolů. Pokud nepoužíváte, toto můžete ignorovat následující chybu: testování toku aktivní přihlášení pomocí zprostředkovatele identity aktivní federaci koncového bodu.

### <a name="manually-verify-that-single-sign-on-has-been-set-up-correctly"></a>Ručně ověřte, že tento jednotného přihlašování se nastavila správně
Ruční ověření poskytuje další kroky, které můžete provést k zajištění, že poskytovatele identity SAML 2.0 je funguje správně v mnoha scénářích.
Pokud chcete ověřit, že tento jednotného přihlašování má správně nastavené, proveďte následující kroky:


1. Na počítači připojeném k doméně přihlášení ke cloudové službě pomocí stejné přihlašovací jméno, který používáte pro své podnikové přihlašovací údaje.
2.  Klikněte do pole heslo. Pokud se jednotné přihlašování je nastavený heslo, bude pole označeno šedou barvou a zobrazí se následující zpráva: "teď jsou nutné k přihlášení na &lt;vaší společnosti&gt;."
3.  Klikněte na tlačítko přihlášení na &lt;vaší společnosti&gt; odkaz. Pokud budete moct přihlásit, pak jednotné přihlašování je nastavený.

## <a name="next-steps"></a>Další kroky


- [Active Directory Federation Services management a přizpůsobení službou Azure AD Connect](how-to-connect-fed-management.md)
- [Seznam kompatibilit pro federaci Azure AD](how-to-connect-fed-compatibility.md)
- [Vlastní instalace služby Azure AD Connect](how-to-connect-install-custom.md)
