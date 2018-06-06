---
title: 'Azure AD Connect: Použijte poskytovatele SAML 2.0 Identity pro jednotné přihlašování na | Microsoft Docs'
description: Tento dokument popisuje pomocí vyhovující Idp SAML 2.0 pro jednotné přihlašování na.
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
ms.openlocfilehash: 6933d9f9951925888c92e35f6b1e2962cc29b0ce
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "34801775"
---
#  <a name="use-a-saml-20-identity-provider-idp-for-single-sign-on"></a>Použít poskytovatele 2.0 Identity SAML (IdP) pro jednotné přihlašování na

Tento dokument obsahuje informace o používání SAML 2.0 kompatibilní SP Lite na základě profilu zprostředkovatele Identity jako upřednostňovaný zabezpečení Token Service (Služba tokenů zabezpečení) nebo zprostředkovatele identity. Tento scénář je vhodný, pokud již máte adresáře uživatele a heslo ukládat místně kterým lze přistupovat pomocí SAML 2.0. Tento adresář existující uživatele lze použít pro přihlášení k Office 365 a jiným Azure AD zabezpečeným prostředkům. SAML 2.0 SP-Lite profilu je založena na často používaný standardní federovaných identit Security (Assertion Markup Language SAML) k poskytování rozhraní exchange přihlášení a atribut.

>[!NOTE]
>Seznam 3. stran Idps, které byly testovány pro použití s Azure AD najdete v článku [seznam kompatibility federace Azure AD](active-directory-aadconnect-federation-compatibility.md)

Společnost Microsoft podporuje toto přihlášení jako integrace cloudové služby Microsoftu, jako je například Office 365 s vaší správně nakonfigurované SAML 2.0 profil na základě deklarací identity. Zprostředkovatelé identity SAML 2.0 jsou produkty třetích stran, a proto společnost Microsoft neposkytuje podporu pro nasazení, konfiguraci, řešení potíží s osvědčené postupy týkající se jim. Jednou správně nakonfigurovaný, integraci s SAML 2.0 poskytovatele identit může být testována pro správnou konfiguraci pomocí nástroje Microsoft připojení analyzátor, který je podrobněji popsané v následující. Další informace o SAML 2.0 SP-Lite zprostředkovatele identity na základě profilu požádejte organizaci, která je zadána.

>[!IMPORTANT]
>Jen omezenou sadou klientů jsou k dispozici v tomto scénáři přihlášení pomocí poskytovatelů identit SAML 2.0, patří mezi ně:

>- Webových klientů, jako například Outlook Web Access a služby SharePoint Online
- Bohaté e-mailové klienty, použijte základní ověřování a podporovaná metoda přístup Exchange například IMAP, POP, Active Sync, MAPI, atd. (rozšířené klientský protokol koncového bodu je potřeba nasadit), včetně:
    - Aplikace Microsoft Outlook 2010, Outlook 2013/Outlook 2016, Apple iPhone (různé verze iOS)
    - Různá zařízení Google Android
    - Windows Phone 7, Windows Phone 7,8 a Windows Phone 8.0
    - E-mailového klienta systému Windows 8 a Windows 8.1 e-mailového klienta
    - E-mailového klienta Windows 10

Všechny ostatní klienty nejsou k dispozici v tomto scénáři přihlášení u svého poskytovatele Identity SAML 2.0. Například na klientovi plochy Lync 2010 není možné se přihlásit do služby u svého poskytovatele Identity SAML 2.0 nakonfigurována pro jednotné přihlašování.

## <a name="azure-ad-saml-20-protocol-requirements"></a>Protokol požadavky pro Azure AD SAML 2.0
Tento dokument obsahuje podrobné požadavky na protokol a zpráva formátování, že poskytovatel identity SAML 2.0 musí implementovat vytvořit federaci s Azure AD povolit přihlášení k jedné nebo více cloudovým službám Microsoftu (např. Office 365). Předávající strana SAML 2.0 (SP služby tokenů zabezpečení) pro cloudové služby Microsoftu používá v tomto scénáři je Azure AD.

Je doporučeno, ujistěte se svého poskytovatele identity SAML 2.0 výstup zprávy byl podobná trasování zadaný vzorek nejvíc. Také použít konkrétní atribut hodnoty ze zadaných metadat Azure AD kde je to možné. Jakmile vyhovují zprávy si výstup, můžete otestovat připojení analyzátorem Microsoft jak je popsáno níže.

Azure AD metadata si můžete stáhnout z této adresy URL: [ https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml ](http://https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml).
Pro zákazníkům v Číně pomocí Číně konkrétní instanci Office 365, je třeba použít následující koncový bod federation: [ https://nexus.partner.microsoftonline-p.cn/federationmetadata/saml20/federationmetadata.xml ](https://nexus.partner.microsoftonline-p.cn/federationmetadata/saml20/federationmetadata.xml).

## <a name="saml-protocol-requirements"></a>Požadavky protokolu SAML
Tato část Podrobnosti o způsobu páry zprávu žádosti a odpovědi jsou společně umístit pořadí můžete k formátování zprávy si správně.

Azure AD můžete nakonfigurovat pro práci s zprostředkovatelů identity, které používají SAML 2.0 SP Lite profil s některé specifické požadavky, jak je uvedeno dále. Použití ukázkové SAML žádosti a odpovědi zprávy společně s automatizované a ruční testování, můžete pracovat v dosažení interoperability s Azure AD.

## <a name="signature-block-requirements"></a>Požadavky na podpis bloku
V rámci zprávu odpovědi SAML uzlu podpis obsahuje informace o digitální podpis pro samotnou zprávu. Blok signatury má následující požadavky:

1. Uzel kontrolní výraz musí být podepsané.
2.  Algoritmus RSA sha1 musí použít jako DigestMethod. Jiné algoritmy digitální podpis nebyly přijaty.
   `<ds:DigestMethod Algorithm="http://www.w3.org/2000/09/xmldsig#sha1"/>`
3.  Může také podepsat dokumentu XML. 
4.  Algoritmus transformace musí shodovat s hodnotami následující ukázka:    `<ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature"/>
       <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#"/>`
9.  Algoritmus SignatureMethod musí odpovídat následující ukázka:   `<ds:SignatureMethod Algorithm="http://www.w3.org/2000/09/xmldsig#rsa-sha1"/>`

## <a name="supported-bindings"></a>Podporované vazby
Vazby jsou informace týkající se přenosu parametry, které jsou požadovány. Následující požadavky platí pro vazby

1. HTTPS je požadovaná přenosu.
2.  Azure AD bude vyžadovat HTTP POST na token odesílání během přihlášení
3.  Azure AD bude používat pro požadavek na ověření do zprostředkovatele identity a PŘESMĚROVÁNÍ pro odhlášení zprávu na zprostředkovatele identit HTTP POST.

## <a name="required-attributes"></a>Povinné atributy
Tato tabulka uvádí požadavky pro konkrétní atributy ve zprávě SAML 2.0.
 
|Atribut|Popis|
| ----- | ----- |
|NameID|Hodnota Tento kontrolní výraz součásti musí být stejný jako ImmutableID uživatele Azure AD. Může být až 64 speciálních znaků. Musí být kódovaný bezpečné znaky jiného typu než html, například je "+" znak zobrazí jako ".2B".|
|IDPEmail|Hlavní název uživatele (UPN) je uveden v odpovědi SAML jako element s názvem IDPEmail UserPrincipalName uživatele (UPN) v Azure AD nebo Office 365. Hlavní název uživatele je ve formátu e-mailovou adresu. Hodnota UPN v systému Windows Office 365 (Azure Active Directory).|
|Vystavitel|Musí být identifikátor URI zprostředkovatele identity. Nepoužívejte opakovaně vystavitele z ukázkové zprávy. Pokud máte víc domén nejvyšší úrovně ve vaší klienty Azure AD se musí shodovat vystavitele zadaný identifikátor URI nastavení nakonfigurovaná v každé doméně.|

>[!IMPORTANT]
>Azure AD aktuálně podporuje následující formát URI NameID pro SAML 2.0:urn:oasis:names:tc:SAML:2.0:nameid-formátu: trvalé.

## <a name="sample-saml-request-and-response-messages"></a>Ukázka zprávy požadavku a odpovědi SAML
Pár zprávu žádosti a odpovědi je zobrazen pro výměnu zpráv přihlášení.
Toto je ukázkovou zprávu požadavku, která se odesílá z Azure AD na poskytovatele identity SAML 2.0 ukázka. Ukázka poskytovatele identity SAML 2.0 je Active Directory Federation Services (AD FS) nakonfigurované na používání protokolu SAML-P. Vzájemná funkční spolupráce testování také bylo dokončeno s jiných zprostředkovatelů identity SAML 2.0.

    `<samlp:AuthnRequest xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol" xmlns:saml="urn:oasis:names:tc:SAML:2.0:assertion" ID="_7171b0b2-19f2-4ba2-8f94-24b5e56b7f1e" IssueInstant="2014-01-30T16:18:35Z" Version="2.0" AssertionConsumerServiceIndex="0" >
    <saml:Issuer>urn:federation:MicrosoftOnline</saml:Issuer>
    <samlp:NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
    </samlp:AuthnRequest>`

Tady je ukázkovou zprávu odpovědi, která se odesílá z zprostředkovatele ukázka SAML 2.0 kompatibilní identity do služby Azure AD nebo Office 365.

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
Tato část obsahuje pokyny ke konfiguraci svého poskytovatele identity SAML 2.0 vytvořit federaci s Azure AD pro povolení přístupu jeden přihlašování na jeden nebo více cloudovým službám Microsoftu (např. Office 365) pomocí protokolu SAML verze 2.0. SAML 2.0 předávající strany pro cloudové služby Microsoftu používá v tomto scénáři je Azure AD.

## <a name="add-azure-ad-metadata"></a>Přidat metadata Azure AD
Zprostředkovatele identity SAML 2.0 je potřeba řídit informace o službě Azure AD předávající strany. Azure AD publikuje metadata na https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml.

Doporučuje se při konfiguraci svého poskytovatele identity SAML 2.0 vždy naimportovat nejnovější metadata Azure AD.

>[!NOTE]
>Azure AD číst metadata ze zprostředkovatele identity.

## <a name="add-azure-ad-as-a-relying-party"></a>Přidat Azure AD jako předávající strany
Je nutné povolit komunikaci mezi poskytovatele identity SAML 2.0 a Azure AD. Tato konfigurace bude závisí na zprostředkovateli konkrétní identity a naleznete v dokumentaci pro ni. Identifikátor předávající strany se obvykle nastavuje ke stejné jako entityID z metadat služby Azure AD.

>[!NOTE]
>Ověřte, zda že na serveru poskytovatele identity SAML 2.0 hodiny synchronizované zdroji přesnému času. Čas nesprávné hodin může způsobit selhání federovaných přihlášení.

## <a name="install-windows-powershell-for-sign-on-with-saml-20-identity-provider"></a>Instalace prostředí Windows PowerShell pro přihlašování pomocí zprostředkovatele identity SAML 2.0
Po konfiguraci svého poskytovatele identity SAML 2.0 pro použití s přihlašováním k Azure AD, dalším krokem je ke stažení a instalaci Azure Active Directory modul pro prostředí Windows PowerShell. Po instalaci bude tyto rutiny použít ke konfiguraci vaší domény služby Azure AD jako federované domény.

Azure Active Directory modul pro prostředí Windows PowerShell je ke stažení pro správu ve službě Azure AD data vaší organizace. Tento modul nainstaluje soubor rutin prostředí Windows PowerShell; Spusťte tyto rutiny nastavit přístup jediné přihlášení k Azure AD a v zapnout na všechny cloudové služby jsou přihlášení k odběru. Pokyny o tom, jak stáhnout a nainstalovat rutiny najdete v tématu [http://technet.microsoft.com/library/jj151815.aspx](http://technet.microsoft.com/library/jj151815.aspx)

## <a name="set-up-a-trust-between-your-saml-identity-provider-and-azure-ad"></a>Nastavení vztahu důvěryhodnosti mezi poskytovatele identity SAML a Azure AD
Před provedením konfigurace federace v doméně služby Azure AD, musí mít nakonfigurované vlastní doménu. Nelze vytvořit federaci výchozí domény, od Microsoftu. Výchozí domény od Microsoftu končí textem "onmicrosoft.com".
Řadu rutiny poběží v rozhraní příkazového řádku prostředí Windows PowerShell můžete přidat nebo převést domén pro jednotné přihlašování.

Každou doménu Azure Active Directory, který chcete vytvořit federaci pomocí zprostředkovatele identity SAML 2.0 musí být přidán jako jediná doména přihlášení nebo převést jako jeden přihlášení domény ze standardní domény. Přidáním nebo převedením domény nastaví vztah důvěryhodnosti mezi poskytovatele identity SAML 2.0 a Azure AD.

Následující postup vás provede převod existující standardní domény na federovanou doménu pomocí SAML 2.0 SP-Lite. 

>[!NOTE]
>Vaše doména může dojít k výpadku, který ovlivňuje uživatele až 2 hodin po provedení tohoto kroku.

## <a name="configuring-a-domain-in-your-azure-ad-directory-for-federation"></a>Konfigurace domény v adresáři Azure AD pro federaci


1. Připojení k vašemu adresáři Azure AD jako správce klienta: připojení MsolService.
2.  Nakonfigurujte požadované Office 365 domény federace pomocí SAML 2.0: `$dom = "contoso.com" $BrandName - "Sample SAML 2.0 IDP" $LogOnUrl = "https://WS2012R2-0.contoso.com/passiveLogon" $LogOffUrl = "https://WS2012R2-0.contoso.com/passiveLogOff" $ecpUrl = "https://WS2012R2-0.contoso.com/PAOS" $MyURI = "urn:uri:MySamlp2IDP" $MySigningCert = @" MIIC7jCCAdagAwIBAgIQRrjsbFPaXIlOG3GTv50fkjANBgkqhkiG9w0BAQsFADAzMTEwLwYDVQQDEyh BREZTIFNpZ25pbmcgLSBXUzIwMTJSMi0wLnN3aW5mb3JtZXIuY29tMB4XDTE0MDEyMDE1MTY0MFoXDT E1MDEyMDE1MTY0MFowMzExMC8GA1UEAxMoQURGUyBTaWduaW5nIC0gV1MyMDEyUjItMC5zd2luZm9yb WVyLmNvbTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAKe+rLVmXy1QwCwZwqgbbp1/kupQ VcjKuKLitVDbssFyqbDTjP7WRjlVMWAHBI3kgNT7oE362Gf2WMJFf1b0HcrsgLin7daRXpq4Qi6OA57 sW1YFMj3sqyuTP0eZV3S4+ZbDVob6amsZIdIwxaLP9Zfywg2bLsGnVldB0+XKedZwDbCLCVg+3ZWxd9 T/jV0hpLIIWr+LCOHqq8n8beJvlivgLmDJo8f+EITnAxWcsJUvVai/35AhHCUq9tc9sqMp5PWtabAEM b2AU72/QlX/72D2/NbGQq1BWYbqUpgpCZ2nSgvlWDHlCiUo//UGsvfox01kjTFlmqQInsJVfRxF5AcC AwEAATANBgkqhkiG9w0BAQsFAAOCAQEAi8c6C4zaTEc7aQiUgvnGQgCbMZbhUXXLGRpjvFLKaQzkwa9 eq7WLJibcSNyGXBa/SfT5wJgsm3TPKgSehGAOTirhcqHheZyvBObAScY7GOT+u9pVYp6raFrc7ez3c+ CGHeV/tNvy1hJNs12FYH4X+ZCNFIT9tprieR25NCdi5SWUbPZL0tVzJsHc1y92b2M2FxqRDohxQgJvy JOpcg2mSBzZZIkvDg7gfPSUXHVS1MQs0RHSbwq/XdQocUUhl9/e/YWCbNNxlM84BxFsBUok1dH/gzBy Sx+Fc8zYi7cOq9yaBT3RLT6cGmFGVYZJW4FyhPZOCLVNsLlnPQcX3dDg9A==" "@ $uri = "http://WS2012R2-0.contoso.com/adfs/services/trust" $Protocol = "SAMLP" Set-MsolDomainAuthentication -DomainName $dom -FederationBrandName $dom -Authentication Federated -PassiveLogOnUri $MyURI -ActiveLogOnUri $ecpUrl -SigningCertificate $MySigningCert -IssuerUri $uri -LogOffUri $url -PreferredAuthenticationProtocol $Protocol` 

3.  Řetězec s kódováním base64 podpisový certifikát můžete získat z IDP metadata souboru. Příkladem tohoto umístění bylo zadáno, ale může mírně lišit v závislosti na vaší implementace.

    `<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol"> <KeyDescriptor use="signing"> <KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#"> <X509Data> <X509Certificate>MIIC5jCCAc6gAwIBAgIQLnaxUPzay6ZJsC8HVv/QfTANBgkqhkiG9w0BAQsFADAvMS0wKwYDVQQDEyRBREZTIFNpZ25pbmcgLSBmcy50ZWNobGFiY2VudHJhbC5vcmcwHhcNMTMxMTA0MTgxMzMyWhcNMTQxMTA0MTgxMzMyWjAvMS0wKwYDVQQDEyRBREZTIFNpZ25pbmcgLSBmcy50ZWNobGFiY2VudHJhbC5vcmcwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQCwMdVLTr5YTSRp+ccbSpuuFeXMfABD9mVCi2wtkRwC30TIyPdORz642MkurdxdPCWjwgJ0HW6TvXwcO9afH3OC5V//wEGDoNcI8PV4enCzTYFe/h//w51uqyv48Fbb3lEXs+aVl8155OAj2sO9IX64OJWKey82GQWK3g7LfhWWpp17j5bKpSd9DBH5pvrV+Q1ESU3mx71TEOvikHGCZYitEPywNeVMLRKrevdWI3FAhFjcCSO6nWDiMqCqiTDYOURXIcHVYTSof1YotkJ4tG6mP5Kpjzd4VQvnR7Pjb47nhIYG6iZ3mR1F85Ns9+hBWukQWNN2hcD/uGdPXhpdMVpBAgMBAAEwDQYJKoZIhvcNAQELBQADggEBAK7h7jF7wPzhZ1dPl4e+XMAr8I7TNbhgEU3+oxKyW/IioQbvZVw1mYVCbGq9Rsw4KE06eSMybqHln3w5EeBbLS0MEkApqHY+p68iRpguqa+W7UHKXXQVgPMCpqxMFKonX6VlSQOR64FgpBme2uG+LJ8reTgypEKspQIN0WvtPWmiq4zAwBp08hAacgv868c0MM4WbOYU0rzMIR6Q+ceGVRImlCwZ5b7XKp4mJZ9hlaRjeuyVrDuzBkzROSurX1OXoci08yJvhbtiBJLf3uPOJHrhjKRwIt2TnzS9ElgFZlJiDIA26Athe73n43CT0af2IG6yC7e6sK4L3NEXJrwwUZk=</X509Certificate> </X509Data> </KeyInfo> </KeyDescriptor>` 

Další informace o "Set-MsolDomainAuthentication" v tématu: [ http://technet.microsoft.com/library/dn194112.aspx ](http://technet.microsoft.com/library/dn194112.aspx).

>[!NOTE]
>Je nutné spustit pomocí "$ecpUrl ="https://WS2012R2-0.contoso.com/PAOS"" pouze v případě, že nastavíte ECP rozšíření pro zprostředkovatele identity. Klienti Exchange Online, s výjimkou Outlook webové aplikace (OWA), závisí na příspěvku na základě active koncového bodu. Pokud vaše SAML 2.0 služby tokenů zabezpečení implementuje aktivní koncový bod podobné implementace ECP Shibboleth na aktivní koncový bod je možné, že pro tyto bohatých klientů pro interakci s službě Exchange Online.

Jakmile byl nakonfigurován federační můžete přepnout zpět na "nefederovaných" (nebo "spravovaný"), ale této změny trvá až dvě hodiny a vyžaduje přiřazení nové náhodné hesla pro cloudové přihlašování pro každého uživatele. Přepnout zpět na "spravovaný" může být nutné v některých scénářích resetovat Chyba v nastavení. Další informace o převodu domény najdete v tématu: [ http://msdn.microsoft.com/library/windowsazure/dn194122.aspx ](http://msdn.microsoft.com/library/windowsazure/dn194122.aspx).

## <a name="provision-user-principals-to-azure-ad--office-365"></a>Zřídit uživatelské objekty do služby Azure AD nebo Office 365
Než uživatelům služeb Office 365, můžete ověřovat, je třeba zřídit Azure AD pomocí objektů uživatele, které odpovídají kontrolního výrazu SAML 2.0 deklarace. Pokud tyto objekty uživatele nejsou známá, mají předem Azure AD, nelze použít pro federované přihlašování. Azure AD Connect nebo prostředí Windows PowerShell může být použito k přidělení identity uživatelů.

Azure AD Connect může být použito k přidělení objektů do domény do adresáře Azure AD z místní služby Active Directory. Podrobnější informace najdete v části [integraci místních adresářů se službou Azure Active Directory](active-directory-aadconnect.md).

Prostředí Windows PowerShell lze také automatizovat, přidávání nových uživatelů do služby Azure AD a k synchronizaci změn z místního adresáře. Chcete-li používat rutiny prostředí Windows PowerShell, je nutné stáhnout [Azure Active Directory moduly](https://docs.microsoft.com/powershell/azure/install-adv2?view=azureadps-2.0).

Tento postup ukazuje, jak přidat jednoho uživatele do Azure AD.


1. Připojení k vašemu adresáři Azure AD jako správce klienta: připojení MsolService.
2.  Vytvořte nový hlavní název uživatele: ` New-MsolUser
        -UserPrincipalName elwoodf1@contoso.com
        -ImmutableId ABCDEFG1234567890
        -DisplayName "Elwood Folk"
        -FirstName Elwood 
        -LastName Folk 
        -AlternateEmailAddresses "Elwood.Folk@contoso.com" 
        -LicenseAssignment "samlp2test:ENTERPRISEPACK" 
        -UsageLocation "US" ` 

Další informace najdete v článku věnovaném "New-MsolUser", [http://technet.microsoft.com/library/dn194096.aspx](http://technet.microsoft.com/library/dn194096.aspx)

>[!NOTE]
>Hodnota "UserPrinciplName" musí odpovídat hodnotě, kterou odešlete "IDPEmail" vaší deklarace SAML 2.0 a hodnota "ImmutableID" musí odpovídat hodnotě odeslány ve vašem kontrolní výraz "NameID".

## <a name="verify-single-sign-on-with-your-saml-20-idp"></a>Ověřte, jednotné přihlašování s vaší IDP SAML 2.0
Jako správce a před ověření a Správa jednotného přihlašování (také federace identit názvem), zkontrolujte informace a postupujte podle kroků v následujících článcích nastavit jednotné přihlašování s poskytovatelem SAML 2.0 SP-Lite na základě identity:


1.  Jste si přečetli protokol požadavky na Azure AD SAML 2.0
2.  Nakonfigurování zprostředkovatele identity SAML 2.0
3.  Instalace prostředí Windows PowerShell pro jednotné přihlašování pomocí zprostředkovatele identity SAML 2.0
4.  Nastavení vztahu důvěryhodnosti mezi poskytovatele identity SAML 2.0 a Azure AD
5.  Buď prostřednictvím prostředí Windows PowerShell nebo Azure AD Connect, zřídí známé testovací hlavní název uživatele do Azure Active Directory (Office 365).
6.  Konfigurovat pomocí synchronizace adresáře [Azure AD Connect](active-directory-aadconnect.md).

Po nastavení jednotného přihlašování s vaší SAML 2.0 SP-Lite na základě identity poskytovatele, ověřte, zda pracuje správně.

>[!NOTE]
>Pokud jste převedli domény, nikoli přidávání jednoho, může trvat až 24 hodin nastavit jednotné přihlašování.
Před ověření jednotného přihlašování, by měl dokončit nastavení synchronizace služby Active Directory, synchronizace vašich adresářů a aktivujte synchronizované uživatele.

### <a name="use-the-tool-to-verify-that-single-sign-on-has-been-set-up-correctly"></a>Ověřte, zda že tento jednotné přihlášení byla nastavena správně pomocí nástroje
Pokud chcete ověřit, že tento jednotné přihlášení byla nastavena správně, můžete provést podle následujícího postupu ověřte, zda jste přihlášení ke cloudové službě pomocí svých podnikových přihlašovacích údajů.

Společnost Microsoft poskytuje nástroj, který můžete použít k testování zprostředkovatele na základě identity SAML 2.0. Než spustíte nástroj pro testování, musíte nakonfigurovat klienta služby Azure AD pro vytvoření federace pomocí zprostředkovatele identity.

>[!NOTE]
>Analyzátor připojení vyžaduje Internet Explorer 10 nebo novější.



1. Stáhnout analyzátor připojení, [ https://testconnectivity.microsoft.com/?tabid=Client ](https://testconnectivity.microsoft.com/?tabid=Client).
2.  Začněte kliknutím na tlačítko nainstalovat stahování a instalace nástroje.
3.  Vyberte "I nemůže vytvořit federaci se Office 365, Azure nebo jiné služby, které používají Azure Active Directory".
4.  Jakmile nástroj stažené a spuštěná, zobrazí se okno diagnostiky připojení. Testování připojení federační bude projděte nástroj.
5.  Analyzátor připojení bude otevřít vaše IDP SAML 2.0 pro vás přihlásit, zadejte přihlašovací údaje pro hlavní název uživatele testujete: ![SAML](media/active-directory-aadconnect-federation-saml-idp/saml1.png)
6.  V federační testovací přihlášení intervalu by měl zadejte název účtu a heslo pro klienta Azure AD, který je nakonfigurovaný na federovanou u svého poskytovatele identity SAML 2.0. Tento nástroj se pokusí přihlásit pomocí těchto přihlašovacích údajů a podrobné výsledky testů provést při pokusu o přihlášení, bude třeba zadat jako výstup.
![SAML](media/active-directory-aadconnect-federation-saml-idp/saml2.png)
7. Toto okno zobrazuje selhání výsledek testování. Kliknutím na zkontrolujte podrobné výsledky se zobrazí informace o výsledky pro všechny testy, které bylo provedeno. Také můžete uložit na disk, aby bylo možné sdílet jejich výsledky.
 
>[!NOTE]
>Analyzátor připojení také testy Active federace pomocí WS*-ECP/PAOS a na základě protokolů. Pokud nepoužíváte tyto můžete ignorovat k následující chybě: testování Active toku přihlášení pomocí zprostředkovatele identity Active federační koncový bod.

### <a name="manually-verify-that-single-sign-on-has-been-set-up-correctly"></a>Ručně ověřte, zda že tento jednotné přihlášení byla nastavena správně
Ruční ověření poskytuje další kroky, které můžete provést k zajištění poskytovatele identity SAML 2.0, ke které pracuje správně v řadě scénářů.
Pokud chcete ověřit, že tento jednotné přihlášení byla nastavena správně, proveďte následující kroky:


1. V počítači připojeném k doméně Přihlaste se do cloudové služby se stejným názvem přihlášení, který používáte pro svoje podnikové přihlašovací údaje.
2.  Klikněte do pole heslo. Pokud jednotné přihlašování je nastavený, do pole heslo bude nastaveno a zobrazí se následující zpráva: "teď musíte přihlásit na &lt;vaší společnosti&gt;."
3.  Klikněte na možnost přihlášení na &lt;vaší společnosti&gt; odkaz. Pokud budete moci přihlásit, pak jednotného přihlašování je nastaven.

## <a name="next-steps"></a>Další kroky


- [Přizpůsobení službou Azure AD Connect a správy služby Active Directory Federation Services](active-directory-aadconnect-federation-management.md)
- [Seznam kompatibilit pro federaci Azure AD](active-directory-aadconnect-federation-compatibility.md)
- [Vlastní instalace Azure AD Connect](active-directory-aadconnect-get-started-custom.md)
