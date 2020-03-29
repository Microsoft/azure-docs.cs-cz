---
title: 'Azure AD Connect: Použijte poskytovatele identity SAML 2.0 pro jednotné přihlašování – Azure'
description: Tento dokument popisuje použití idp kompatibilní se standardem SAML 2.0 pro jednotné přihlašování.
services: active-directory
author: billmath
manager: daveba
ms.custom: it-pro
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff4f1b81f476159c13d3725cd3cb1766aec7dc87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70305103"
---
#  <a name="use-a-saml-20-identity-provider-idp-for-single-sign-on"></a>Použití zprostředkovatele identity (IdP) SAML 2.0 pro Jednotné přihlašování

Tento dokument obsahuje informace o použití poskytovatele identity založeného na profilech SP-Lite kompatibilním se standardem SAML 2.0 jako upřednostňovaného zprostředkovatele tokenů zabezpečení (STS) / identit. Tento scénář je užitečný, pokud již máte místní úložiště uživatelských adresářů a hesel, ke kterému lze přistupovat pomocí saml 2.0. Tento existující uživatelský adresář se dá použít pro přihlášení k Office 365 a dalším prostředkům zabezpečeným službou Azure AD. Profil SP-Lite SAML 2.0 je založen na široce používaném standardu federované identity jazyka rozpoznávání zabezpečení (SAML), který poskytuje rámec pro přihlašování a výměnu atributů.

>[!NOTE]
>Seznam idps třetích stran, které byly testovány pro použití s Azure AD naleznete v [seznamu kompatibility federace Azure AD](how-to-connect-fed-compatibility.md)

Společnost Microsoft podporuje toto přihlašovací prostředí jako integraci cloudové služby Microsoftu, jako je například Office 365, se správně nakonfigurovaným idp založeným na profilech SAML 2.0. Poskytovatelé identit SAML 2.0 jsou produkty třetích stran, a proto společnost Microsoft neposkytuje podporu pro nasazení, konfiguraci a řešení potíží s doporučenými postupy týkajícími se nich. Po správné konfiguraci lze integraci s poskytovatelem identity SAML 2.0 otestovat na správnou konfiguraci pomocí nástroje Microsoft Connectivity Analyzer Tool, který je podrobněji popsán níže. Další informace o poskytovateli identity založeném na profilech SAML 2.0 SP-Lite získáte v organizaci, která jej dodala.

> [!IMPORTANT]
> V tomto scénáři přihlašování s poskytovateli identit saml 2.0 je k dispozici pouze omezená sada klientů, což zahrnuje:
> 
> - Weboví klienti, jako je Outlook Web Access a SharePoint Online
> - Klienti s bohatými e-maily, kteří používají základní ověřování a podporovanou metodu přístupu k serveru Exchange, jako je IMAP, POP, Active Sync, MAPI atd., (koncový bod rozšířeného klientského protokolu je nutné nasadit), včetně:
>     - Microsoft Outlook 2010/Outlook 2013/Outlook 2016, Apple iPhone (různé verze iOS)
>     - Různá zařízení Google Android
>     - Windows Phone 7, Windows Phone 7.8 a Windows Phone 8.0
>     - Poštovní klient Windows 8 a poštovní klient Windows 8.1
>     - Poštovní klient windows 10

Všichni ostatní klienti nejsou k dispozici v tomto scénáři přihlášení s poskytovatelem identity SAML 2.0. Například desktopový klient Lyncu 2010 se nemůže přihlásit ke službě pomocí poskytovatele identity SAML 2.0 nakonfigurovaného pro jednotné přihlašování.

## <a name="azure-ad-saml-20-protocol-requirements"></a>Požadavky na protokol Azure AD SAML 2.0
Tento dokument obsahuje podrobné požadavky na protokol a formátování zpráv, které musí váš poskytovatel identity SAML 2.0 implementovat, aby mohl fedovat pomocí Azure AD, aby bylo možné se přihlásit k jedné nebo více cloudovým službám Microsoftu (například KSČM). Předávající strana SAML 2.0 (SP-STS) pro cloudovou službu Microsoftu používanou v tomto scénáři je Azure AD.

Doporučujeme zajistit, aby výstupní zprávy poskytovatele identity SAML 2.0 byly co nejvíce podobné zadaným ukázkovým trasováním. Také použijte hodnoty konkrétníatribut z dodaných metadat Azure AD, kde je to možné. Jakmile budete spokojeni s výstupními zprávami, můžete otestovat pomocí analyzátoru připojení společnosti Microsoft, jak je popsáno níže.

Metadata Azure AD lze stáhnout z [https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml](https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml)této adresy URL: .
Pro zákazníky v Číně, kteří používají instanci Office 365 specifickou pro Čínu, by se měl použít následující koncový bod federace: [https://nexus.partner.microsoftonline-p.cn/federationmetadata/saml20/federationmetadata.xml](https://nexus.partner.microsoftonline-p.cn/federationmetadata/saml20/federationmetadata.xml).

## <a name="saml-protocol-requirements"></a>Požadavky na protokol SAML
V této části je podrobně uvedeno, jak jsou dvojice zpráv požadavků a odpovědí sestaveny, aby vám pomohly správně formátovat zprávy.

Azure AD lze nakonfigurovat pro práci s poskytovateli identit, které používají SAML 2.0 SP Lite profil s některými konkrétními požadavky, jak je uvedeno níže. Pomocí ukázkových zpráv požadavků saml a odpovědí spolu s automatizovaným a ručním testováním můžete pracovat na dosažení interoperability s Azure AD.

## <a name="signature-block-requirements"></a>Požadavky na blok podpisu
Ve zprávě odpovědi SAML obsahuje uzel Podpis informace o digitálním podpisu pro samotnou zprávu. Blok podpisu má následující požadavky:

1. Samotný uzel kontrolního výrazu musí být podepsán.
2.  RsA-sha1 algoritmus musí být použit jako DigestMethod. Jiné algoritmy digitálního podpisu nejsou akceptovány.
   `<ds:DigestMethod Algorithm="https://www.w3.org/2000/09/xmldsig#sha1"/>`
3.  Můžete také podepsat dokument XML. 
4.  Transformační algoritmus musí odpovídat hodnotám v následujícím vzorku:`<ds:Transform Algorithm="https://www.w3.org/2000/09/xmldsig#enveloped-signature"/>
       <ds:Transform Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#"/>`
9.  Algoritmus SignatureMethod musí odpovídat následující ukázce:`<ds:SignatureMethod Algorithm="https://www.w3.org/2000/09/xmldsig#rsa-sha1"/>`

## <a name="supported-bindings"></a>Podporovaná vazby
Vazby jsou komunikační parametry související s přenosem, které jsou požadovány. Následující požadavky se vztahují na vazby

1. HTTPS je požadovaný přenos.
2.  Azure AD bude vyžadovat HTTP POST pro odeslání tokenu během přihlášení.
3.  Azure AD bude používat HTTP POST pro požadavek na ověření na zprostředkovatele identity a přesměrování pro odhlásit zprávu k poskytovateli identity.

## <a name="required-attributes"></a>Povinné atributy
Tato tabulka ukazuje požadavky na konkrétní atributy ve zprávě SAML 2.0.
 
|Atribut|Popis|
| ----- | ----- |
|NameID|Hodnota tohoto kontrolního výrazu musí být stejná jako imemitaci id uživatele služby Azure AD. Může to být až 64 alfanumerických znaků. Všechny bezpečné znaky než html musí být kódovány, například znak "+" je zobrazen jako ".2B".|
|IDPEmail|Hlavní jméno uživatele (UPN) je uveden v odpovědi SAML jako prvek s názvem IDPEmail UserPrincipalName uživatele (UPN) v Azure AD/Office 365. Hlavní název společnosti je ve formátu e-mailové adresy. hodnota UPN ve Windows Office 365 (Azure Active Directory).|
|Vystavitel|Musí být identifikátor URI zprostředkovatele identity. Nepoužívejte vydavatele z ukázkové zprávy. Pokud máte ve svých klientech Azure AD více domén nejvyšší úrovně, musí vystavitel odpovídat zadanému nastavení URI nakonfigurovanému pro doménu.|

>[!IMPORTANT]
>Služba Azure AD aktuálně podporuje následující identifikátor URI formátu NameID pro SAML 2.0:urn:oasis:names:tc:SAML:2.0:nameid-format:persistent.

## <a name="sample-saml-request-and-response-messages"></a>Ukázkové zprávy požadavků a odpovědí SAML
Pro výměnu přihlašovacích zpráv se zobrazí dvojice zpráv požadavku a odpovědi.
Následuje ukázková zpráva požadavku, která se odesílá z Azure AD na ukázku SAML 2.0 zprostředkovatele identity. Ukázkovým poskytovatelem identity SAML 2.0 je služba AD FS (Active Directory Federation Services) nakonfigurovaná pro použití protokolu SAML-P. Testování interoperability bylo také dokončeno s dalšími poskytovateli identit SAML 2.0.

    `<samlp:AuthnRequest xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol" xmlns:saml="urn:oasis:names:tc:SAML:2.0:assertion" ID="_7171b0b2-19f2-4ba2-8f94-24b5e56b7f1e" IssueInstant="2014-01-30T16:18:35Z" Version="2.0" AssertionConsumerServiceIndex="0" >
    <saml:Issuer>urn:federation:MicrosoftOnline</saml:Issuer>
    <samlp:NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
    </samlp:AuthnRequest>`

Následuje ukázková zpráva odpovědi, která se odesílá z ukázkového poskytovatele identity kompatibilního se standardem SAML 2.0 do Azure AD / Office 365.

    `<samlp:Response ID="_592c022f-e85e-4d23-b55b-9141c95cd2a5" Version="2.0" IssueInstant="2014-01-31T15:36:31.357Z" Destination="https://login.microsoftonline.com/login.srf" Consent="urn:oasis:names:tc:SAML:2.0:consent:unspecified" InResponseTo="_049917a6-1183-42fd-a190-1d2cbaf9b144" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
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
    </samlp:Response>`

## <a name="configure-your-saml-20-compliant-identity-provider"></a>Konfigurace poskytovatele identity kompatibilního se standardem SAML 2.0
Tato část obsahuje pokyny, jak nakonfigurovat poskytovatele identity SAML 2.0 tak, aby federate s Azure AD povolit přístup k jednotnému přihlášení k jedné nebo více cloudových služeb Microsoftu (například Office 365) pomocí protokolu SAML 2.0. Předávající stranou SAML 2.0 pro cloudovou službu Microsoftu používanou v tomto scénáři je Azure AD.

## <a name="add-azure-ad-metadata"></a>Přidání metadat Azure AD
Váš poskytovatel identity SAML 2.0 musí dodržovat informace o předávající straně Azure AD. Azure AD publikuje https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xmlmetadata na .

Při konfiguraci zprostředkovatele identity SAML 2.0 doporučujeme vždy importovat nejnovější metadata Azure AD.

>[!NOTE]
>Azure AD nečte metadata od poskytovatele identity.

## <a name="add-azure-ad-as-a-relying-party"></a>Přidání Azure AD jako předávající strany
Je nutné povolit komunikaci mezi poskytovatelem identity SAML 2.0 a Azure AD. Tato konfigurace bude záviset na konkrétním poskytovateli identity a měli byste se obrátit na dokumentaci. Obvykle byste nastavili ID předávající strany na stejné jako id entity z metadat Azure AD.

>[!NOTE]
>Ověřte, zda jsou hodiny na serveru poskytovatele identity SAML 2.0 synchronizovány s přesným zdrojem času. Nepřesné hodiny čas může způsobit federované přihlášení nezdaří.

## <a name="install-windows-powershell-for-sign-on-with-saml-20-identity-provider"></a>Instalace prostředí Windows PowerShell pro přihlášení pomocí zprostředkovatele identity SAML 2.0
Po nakonfigurování poskytovatele identity SAML 2.0 pro použití s přihlášením služby Azure AD je dalším krokem stažení a instalace modulu Azure Active Directory pro prostředí Windows PowerShell. Po instalaci použijete tyto rutiny ke konfiguraci domén Azure AD jako federovaných domén.

Modul Azure Active Directory pro Windows PowerShell je soubor ke stažení pro správu dat vašich organizací ve službě Azure AD. Tento modul nainstaluje sadu rutin do prostředí Windows PowerShell; spustíte tyto rutiny nastavit přístup jednotného přihlášení k Azure AD a zase na všechny cloudové služby, které jste přihlášeni k odběru. Pokyny ke stažení a instalaci rutin naleznete v tématu[https://technet.microsoft.com/library/jj151815.aspx](https://technet.microsoft.com/library/jj151815.aspx)

## <a name="set-up-a-trust-between-your-saml-identity-provider-and-azure-ad"></a>Nastavení vztahu důvěryhodnosti mezi poskytovatelem identity SAML a službou Azure AD
Před konfigurací federace v doméně Azure AD musí mít nakonfigurovanou vlastní doménu. Výchozí doménu poskytovanou společností Microsoft nelze federate. Výchozí doména od společnosti Microsoft končí "onmicrosoft.com".
V rozhraní příkazového řádku prostředí Windows PowerShell spustíte řadu rutin pro přidání nebo převod domén pro jednotné přihlašování.

Každá doména služby Azure Active Directory, kterou chcete federate pomocí vašeho poskytovatele identity SAML 2.0, musí být přidána jako doména jednotného přihlášení nebo převedena na doménu jednotného přihlášení ze standardní domény. Přidání nebo převod domény nastaví vztah důvěryhodnosti mezi poskytovatelem identity SAML 2.0 a Azure AD.

Následující postup vás provede převodem existující standardní domény na federovovovnou doménu pomocí aktualizace SAML 2.0 SP-Lite. 

>[!NOTE]
>Do vaší domény může dojít k výpadku, který má vliv na uživatele až 2 hodiny po provedení tohoto kroku.

## <a name="configuring-a-domain-in-your-azure-ad-directory-for-federation"></a>Konfigurace domény v adresáři Azure AD pro federaci


1. Připojte se k adresáři Azure AD jako správce klienta: Connect-MsolService .
2.  Nakonfigurujte požadovanou doménu Office 365 tak, aby používala federaci se saml 2.0:`$dom = "contoso.com" $BrandName - "Sample SAML 2.0 IDP" $LogOnUrl = "https://WS2012R2-0.contoso.com/passiveLogon" $LogOffUrl = "https://WS2012R2-0.contoso.com/passiveLogOff" $ecpUrl = "https://WS2012R2-0.contoso.com/PAOS" $MyURI = "urn:uri:MySamlp2IDP" $MySigningCert = @" MIIC7jCCAdagAwIBAgIQRrjsbFPaXIlOG3GTv50fkjANBgkqhkiG9w0BAQsFADAzMTEwLwYDVQQDEyh BREZTIFNpZ25pbmcgLSBXUzIwMTJSMi0wLnN3aW5mb3JtZXIuY29tMB4XDTE0MDEyMDE1MTY0MFoXDT E1MDEyMDE1MTY0MFowMzExMC8GA1UEAxMoQURGUyBTaWduaW5nIC0gV1MyMDEyUjItMC5zd2luZm9yb WVyLmNvbTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAKe+rLVmXy1QwCwZwqgbbp1/kupQ VcjKuKLitVDbssFyqbDTjP7WRjlVMWAHBI3kgNT7oE362Gf2WMJFf1b0HcrsgLin7daRXpq4Qi6OA57 sW1YFMj3sqyuTP0eZV3S4+ZbDVob6amsZIdIwxaLP9Zfywg2bLsGnVldB0+XKedZwDbCLCVg+3ZWxd9 T/jV0hpLIIWr+LCOHqq8n8beJvlivgLmDJo8f+EITnAxWcsJUvVai/35AhHCUq9tc9sqMp5PWtabAEM b2AU72/QlX/72D2/NbGQq1BWYbqUpgpCZ2nSgvlWDHlCiUo//UGsvfox01kjTFlmqQInsJVfRxF5AcC AwEAATANBgkqhkiG9w0BAQsFAAOCAQEAi8c6C4zaTEc7aQiUgvnGQgCbMZbhUXXLGRpjvFLKaQzkwa9 eq7WLJibcSNyGXBa/SfT5wJgsm3TPKgSehGAOTirhcqHheZyvBObAScY7GOT+u9pVYp6raFrc7ez3c+ CGHeV/tNvy1hJNs12FYH4X+ZCNFIT9tprieR25NCdi5SWUbPZL0tVzJsHc1y92b2M2FxqRDohxQgJvy JOpcg2mSBzZZIkvDg7gfPSUXHVS1MQs0RHSbwq/XdQocUUhl9/e/YWCbNNxlM84BxFsBUok1dH/gzBy Sx+Fc8zYi7cOq9yaBT3RLT6cGmFGVYZJW4FyhPZOCLVNsLlnPQcX3dDg9A==" "@ $uri = "http://WS2012R2-0.contoso.com/adfs/services/trust" $Protocol = "SAMLP" Set-MsolDomainAuthentication -DomainName $dom -FederationBrandName $BrandName -Authentication Federated -PassiveLogOnUri $LogOnUrl -ActiveLogOnUri $ecpUrl -SigningCertificate $MySigningCert -IssuerUri $MyURI -LogOffUri $LogOffUrl -PreferredAuthenticationProtocol $Protocol` 

3.  Řetězec zakódovaný podpisovým certifikátem base64 můžete získat ze souboru metadat IDP. Příklad tohoto umístění byla poskytnuta, ale může se mírně lišit v závislosti na implementaci.

    `<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol"> <KeyDescriptor use="signing"> <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#"> <X509Data> <X509Certificate>MIIC5jCCAc6gAwIBAgIQLnaxUPzay6ZJsC8HVv/QfTANBgkqhkiG9w0BAQsFADAvMS0wKwYDVQQDEyRBREZTIFNpZ25pbmcgLSBmcy50ZWNobGFiY2VudHJhbC5vcmcwHhcNMTMxMTA0MTgxMzMyWhcNMTQxMTA0MTgxMzMyWjAvMS0wKwYDVQQDEyRBREZTIFNpZ25pbmcgLSBmcy50ZWNobGFiY2VudHJhbC5vcmcwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQCwMdVLTr5YTSRp+ccbSpuuFeXMfABD9mVCi2wtkRwC30TIyPdORz642MkurdxdPCWjwgJ0HW6TvXwcO9afH3OC5V//wEGDoNcI8PV4enCzTYFe/h//w51uqyv48Fbb3lEXs+aVl8155OAj2sO9IX64OJWKey82GQWK3g7LfhWWpp17j5bKpSd9DBH5pvrV+Q1ESU3mx71TEOvikHGCZYitEPywNeVMLRKrevdWI3FAhFjcCSO6nWDiMqCqiTDYOURXIcHVYTSof1YotkJ4tG6mP5Kpjzd4VQvnR7Pjb47nhIYG6iZ3mR1F85Ns9+hBWukQWNN2hcD/uGdPXhpdMVpBAgMBAAEwDQYJKoZIhvcNAQELBQADggEBAK7h7jF7wPzhZ1dPl4e+XMAr8I7TNbhgEU3+oxKyW/IioQbvZVw1mYVCbGq9Rsw4KE06eSMybqHln3w5EeBbLS0MEkApqHY+p68iRpguqa+W7UHKXXQVgPMCpqxMFKonX6VlSQOR64FgpBme2uG+LJ8reTgypEKspQIN0WvtPWmiq4zAwBp08hAacgv868c0MM4WbOYU0rzMIR6Q+ceGVRImlCwZ5b7XKp4mJZ9hlaRjeuyVrDuzBkzROSurX1OXoci08yJvhbtiBJLf3uPOJHrhjKRwIt2TnzS9ElgFZlJiDIA26Athe73n43CT0af2IG6yC7e6sK4L3NEXJrwwUZk=</X509Certificate> </X509Data> </KeyInfo> </KeyDescriptor>` 

Další informace o "Set-MsolDomainAuthentication" [https://technet.microsoft.com/library/dn194112.aspx](https://technet.microsoft.com/library/dn194112.aspx)naleznete v této oblasti: .

>[!NOTE]
>Použití je `$ecpUrl = "https://WS2012R2-0.contoso.com/PAOS"` nutné spustit pouze v případě, že jste nastavili rozšíření ECP pro vašeho poskytovatele identity. Klienti Exchange Online, s výjimkou Outlook Web Application (OWA), spoléhají na aktivní koncový bod založený na post. Pokud váš SAML 2.0 STS implementuje aktivní koncový bod podobný Implementaci ECP Shibboleth aktivní koncový bod může být možné pro tyto bohaté klienty k interakci se službou Exchange Online.

Po konfiguraci federace můžete přepnout zpět na "nefederované" (nebo "spravované"), ale tato změna trvá až dvě hodiny a vyžaduje přiřazení nových náhodných hesel pro přihlášení na základě cloudu každému uživateli. Přepnutí zpět na "spravované" může být vyžadováno v některých scénářích obnovit chybu v nastavení. Další informace o převodu [https://msdn.microsoft.com/library/windowsazure/dn194122.aspx](https://msdn.microsoft.com/library/windowsazure/dn194122.aspx)domény naleznete v těchto tématech: .

## <a name="provision-user-principals-to-azure-ad--office-365"></a>Zřízení uživatelských objektů pro Azure AD / Office 365
Než budete moct ověřit uživatele office 365, musíte zřídit Azure AD s uživatelskými objekty, které odpovídají kontrolní výraz v deklaraci SAML 2.0. Pokud tyto objekty uživatelů nejsou známy Azure AD předem, pak je nelze použít pro federované přihlášení. Azure AD Connect nebo Windows PowerShell lze použít ke zřízení uživatelských objektů.

Azure AD Connect se dá zřídit objekty zabezpečení vašich domén ve vašem adresáři Azure AD z místní služby Active Directory. Podrobnější informace najdete [v tématu Integrace místních adresářů pomocí služby Azure Active Directory](whatis-hybrid-identity.md).

Prostředí Windows PowerShell lze také použít k automatizaci přidávání nových uživatelů do služby Azure AD a k synchronizaci změn z místního adresáře. Chcete-li použít rutiny prostředí Windows PowerShell, je nutné stáhnout [moduly Azure Active Directory](https://docs.microsoft.com/powershell/azure/install-adv2?view=azureadps-2.0).

Tento postup ukazuje, jak přidat jednoho uživatele do služby Azure AD.


1. Připojte se k adresáři Azure AD jako správce klienta: Connect-MsolService.
2.  Vytvořte nový uživatelský objekt zabezpečení:
    ```powershell
    New-MsolUser
      -UserPrincipalName elwoodf1@contoso.com
      -ImmutableId ABCDEFG1234567890
      -DisplayName "Elwood Folk"
      -FirstName Elwood 
      -LastName Folk 
      -AlternateEmailAddresses "Elwood.Folk@contoso.com" 
      -LicenseAssignment "samlp2test:ENTERPRISEPACK" 
      -UsageLocation "US" 
    ```

Další informace o pokladně "New-MsolUser" získáte[https://technet.microsoft.com/library/dn194096.aspx](https://technet.microsoft.com/library/dn194096.aspx)

>[!NOTE]
>Hodnota "UserPrinciplName" se musí shodovat s hodnotou, kterou odešlete pro "IDPEmail" v deklaraci SAML 2.0 a hodnota "ImmutableID" se musí shodovat s hodnotou odeslanou ve výrazu "NameID".

## <a name="verify-single-sign-on-with-your-saml-20-idp"></a>Ověření jednotného přihlášení pomocí zařízení IDP SAML 2.0
Jako správce před ověřením a správou jednotného přihlašování (označované také jako federace identit) zkontrolujte informace a proveďte kroky v následujících článcích a nastavte jednotné přihlašování u poskytovatele identity založeného na rozhraní SAML 2.0 SP-Lite:


1.  Zkontrolovali jste požadavky protokolu Azure AD SAML 2.0
2.  Nakonfigurovali jste svého zprostředkovatele identity SAML 2.0
3.  Instalace prostředí Windows PowerShell pro jednotné přihlašování s poskytovatelem identity SAML 2.0
4.  Nastavení vztahu důvěryhodnosti mezi poskytovatelem identity SAML 2.0 a azure ad
5.  Zřízené známé testovací uživatele objektu pro Azure Active Directory (Office 365) buď prostřednictvím Windows PowerShell nebo Azure AD Connect.
6.  Konfigurace synchronizace adresářů pomocí [služby Azure AD Connect](whatis-hybrid-identity.md).

Po nastavení jednotného přihlášení u poskytovatele identity založeného na saml 2.0 SP-Lite byste měli ověřit, zda funguje správně.

>[!NOTE]
>Pokud jste doménu převedli, místo aby ji přidali, může nastavení jednotného přihlášení trvat až 24 hodin.
Před ověřením jednotného přihlašování byste měli dokončit nastavení synchronizace služby Active Directory, synchronizovat adresáře a aktivovat synchronizované uživatele.

### <a name="use-the-tool-to-verify-that-single-sign-on-has-been-set-up-correctly"></a>Pomocí nástroje ověřte, zda bylo jednotné přihlašování nastaveno správně.
Chcete-li ověřit, že jednotné přihlášení bylo nastaveno správně, můžete provést následující postup k potvrzení, že jste schopni přihlásit ke cloudové službě s firemní pověření.

Společnost Microsoft poskytla nástroj, který můžete použít k testování poskytovatele identity založeného na saml 2.0. Před spuštěním testovacího nástroje musíte nakonfigurovat klienta Azure AD tak, aby federate s poskytovatelem identity.

>[!NOTE]
>Nástroj Connectivity Analyzer vyžaduje aplikaci Internet Explorer 10 nebo novější.



1. Stáhněte si nástroj [https://testconnectivity.microsoft.com/?tabid=Client](https://testconnectivity.microsoft.com/?tabid=Client)Connectivity Analyzer z . .
2.  Klepnutím na tlačítko Nainstalovat a začněte nástroj stahovat a instalovat.
3.  Vyberte "Nemůžu nastavit federaci s Office 365, Azure nebo jinými službami, které používají Azure Active Directory".
4.  Po stažení a spuštění nástroje se zobrazí okno Diagnostika připojení. Nástroj vás provede testováním vašeho federačního připojení.
5.  Nástroj Connectivity Analyzer otevře protokol IDP saml 2.0 pro přihlášení, zadejte pověření pro ![uživatelský objekt, který testujete: SAML](./media/how-to-connect-fed-saml-idp/saml1.png)
6.  V okně přihlášení k testu federace byste měli zadat název účtu a heslo pro klienta Azure AD, který je nakonfigurovaný tak, aby byl federován s poskytovatelem identity SAML 2.0. Nástroj se pokusí přihlásit pomocí těchto pověření a podrobné výsledky testů provedených během pokusu o přihlášení budou poskytnuty jako výstup.
![SAML](./media/how-to-connect-fed-saml-idp/saml2.png)
7. Toto okno zobrazuje neúspěšný výsledek testování. Kliknutím na podrobné výsledky kontroly se zobrazí informace o výsledcích každého provedeného testu. Výsledky můžete také uložit na disk, abyste je mohli sdílet.
 
>[!NOTE]
>Analyzátor připojení také testuje aktivní federaci pomocí protokolů Založených na WS* a ECP/PAOS. Pokud je nepoužíváte, můžete ignorovat následující chybu: Testování aktivního přihlašování pomocí koncového bodu aktivní federace poskytovatele identity.

### <a name="manually-verify-that-single-sign-on-has-been-set-up-correctly"></a>Ruční ověření správného nastavení jednotného přihlášení
Ruční ověření poskytuje další kroky, které můžete provést, abyste zajistili, že váš poskytovatel identity SAML 2.0 pracuje správně v mnoha scénářích.
Chcete-li ověřit, zda bylo jednotné přihlašování nastaveno správně, proveďte následující kroky:


1. V počítači přilehlém k doméně se přihlaste ke cloudové službě pomocí stejného přihlašovacího jména, které používáte pro vaše podniková pověření.
2.  Klikněte do pole s heslem. Pokud je nastaveno jednotné přihlašování, bude pole s heslem stínováno a zobrazí se &lt;následující&gt;zpráva: "Nyní se musíte přihlásit ve vaší společnosti ."
3.  Klikněte na odkaz &lt;Přihlásit se u vaší společnosti.&gt; Pokud se můžete přihlásit, bylo nastaveno jednotné přihlašování.

## <a name="next-steps"></a>Další kroky


- [Správa a přizpůsobení služby AD Federování služby AD pomocí služby Azure AD Connect](how-to-connect-fed-management.md)
- [Seznam kompatibilit pro federaci Azure AD](how-to-connect-fed-compatibility.md)
- [Vlastní instalace služby Azure AD Connect](how-to-connect-install-custom.md)
