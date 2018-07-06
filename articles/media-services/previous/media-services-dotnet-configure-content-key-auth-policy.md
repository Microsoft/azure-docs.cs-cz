---
title: Konfigurace zásad autorizace klíče obsahu s využitím Media Services .NET SDK | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat zásadu autorizace pro klíč k obsahu pomocí Media Services .NET SDK.
services: media-services
documentationcenter: ''
author: mingfeiy
manager: cfowler
editor: ''
ms.assetid: 1a0aedda-5b87-4436-8193-09fc2f14310c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: juliako;mingfeiy
ms.openlocfilehash: 531b90b905df8549846c6027fe547521d16cf082
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/06/2018
ms.locfileid: "37868496"
---
# <a name="dynamic-encryption-configure-a-content-key-authorization-policy"></a>Dynamické šifrování: Nakonfigurujte zásady autorizace klíče obsahu
[!INCLUDE [media-services-selector-content-key-auth-policy](../../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Přehled
 Azure Media Services můžete doručovat datové proudy MPEG-DASH, Smooth Streaming a HTTP Live Streaming (HLS) chráněné pomocí na Standard AES (Advanced Encryption) pomocí 128bitového šifrování klíče nebo [PlayReady digital rights management (DRM) ](https://www.microsoft.com/playready/overview/). Díky Media Services můžete také doručovat datové proudy DASH šifrované pomocí Widevine DRM. Technologie PlayReady i Widevine jsou šifrované podle specifikace Common Encryption (ISO/IEC CENC 23001-7).

Služba Media Services také poskytuje služba doručování klíčů/licencí ze kterých mohou klienti získat klíče AES nebo PlayReady/Widevine licence k přehrávání šifrovaného obsahu.

Pokud chcete Media Services k šifrování assetu, musíte přidružit šifrovacího klíče (CommonEncryption nebo EnvelopeEncryption) assetu. Další informace najdete v tématu [vytvoření klíčů ContentKeys s využitím .NET](media-services-dotnet-create-contentkey.md). Musíte také nakonfigurovat zásady autorizace pro klíč (jak je popsáno v tomto článku).

Datový proud je žádost přehrávač, využívá Media Services se zadaným klíčem k dynamické šifrování obsahu pomocí šifrování AES a DRM. K dešifrování datového proudu, přehrávače požádá službu doručování klíčů klíč. Pokud chcete zjistit, zda je uživatel oprávnění k získání klíče, služba vyhodnocuje zásady autorizace, které jste zadali pro klíč.

Služba Media Services podporuje více způsobů ověřování uživatelů, kteří žádají o klíč. Zásady autorizace klíče obsahu může mít jednu nebo více omezení autorizace. Možnosti jsou otevřené nebo s tokenem omezení. Zásady omezení tokenem musí být doplněny tokenem vydaným službou tokenů zabezpečení (STS). Služba Media Services podporuje tokeny ve jednoduchý webový token ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) formát a webových tokenů JSON ([JWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3)) formát.

Služba Media Services neposkytuje službu tokenů zabezpečení. Můžete vytvořit vlastní službu STS, nebo použít Azure Access Control Service pro vydávání tokenů. Služba tokenů zabezpečení musí být nakonfigurovaný k vytvoření tokenu podepsán zadaný klíč a vydávání deklarací identity, které jste zadali v konfiguraci omezení s tokenem (jak je popsáno v tomto článku). Pokud je token platný a deklarace identity v tokenu odpovídají těm nakonfigurované pro symetrický klíč, doručení klíče služby Media Services vrátí klientovi šifrovací klíč.

Další informace najdete v následujících článcích:

- [Ověření tokenu JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
- [Integrace aplikace využívající architekturu MVC OWIN Azure Media Services se službou Azure Active Directory a omezit klíče doručování obsahu na základě deklarací identity tokenů JWT](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

### <a name="some-considerations-apply"></a>Určité předpoklady
* Po vytvoření účtu Media Services se do vašeho účtu přidá výchozí koncový bod streamování ve stavu Zastaveno. Pokud chcete spustit streamování vašeho obsahu a využít výhod dynamického balení a dynamického šifrování, musí být koncový bod streamování ve stavu "Spuštěno". 
* Váš asset musí obsahovat sadu s adaptivní přenosovou rychlostí soubory MP4 rychlostmi nebo soubory technologie Smooth Streaming s adaptivní přenosovou rychlostí. Další informace najdete v tématu [kódování prostředku](media-services-encode-asset.md).
* Nahrání a kódovat vaše prostředky pomocí možnosti AssetCreationOptions.StorageEncrypted.
* Pokud chcete mít více klíčů obsahu, které vyžadují stejnou konfiguraci zásad, doporučujeme vytvořit jeden autorizační zásady a znovu použít s více klíčů obsahu.
* Služba doručování klíčů ukládá do mezipaměti ContentKeyAuthorizationPolicy a její související objekty (Možnosti zásad a omezení) na 15 minut. Můžete vytvořit ContentKeyAuthorizationPolicy a zadat omezení s tokenem, otestovat a pak aktualizujte zásady na otevřené omezení. Tento proces trvá přibližně 15 minut před zásad přepínače otevřít verzi zásad.
* Pokud přidáte nebo aktualizujete zásady pro doručení prostředku, musíte odstranit stávající lokátor a vytvořit nový.
* V současné době nelze zašifrovat progresivní stahování.
* Media Services koncový bod streamování nastaví hodnotu hlavičky CORS "Přístup-Control-Allow-Origin" v předběžné odpověď jako zástupný znak "\*". Tato hodnota se pracuje s většina přehrávače, včetně Azure Media Player, Roku a JWPlayer a dalších. Však některé přehrávačích dashjs nefungují, protože v režimu pověření nastavte na "zahrnutí" XMLHttpRequest v jejich dashjs nepovoluje zástupný znak "\*" jako hodnota "Přístup-Control-Allow-Origin". Jako alternativní řešení pro toto omezení v dashjs pokud hostujete vašeho klienta z jedné domény, Media Services můžete zadat tuto doménu v hlavičce odpovědi výstupem. Potřebujete pomoc otevřete lístek podpory prostřednictvím webu Azure portal.

## <a name="aes-128-dynamic-encryption"></a>Dynamické šifrování AES-128
### <a name="open-restriction"></a>Otevřít omezení
Otevřít omezení znamená, že systém poskytuje všem uživatelům, kteří vytvoří klíče požadavek klíč. Toto omezení může být užitečná pro účely testování.

Následující příklad vytvoří zásadu autorizace otevřít a přidá jej do klíče k obsahu:
```csharp
    static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
    {
        // Create ContentKeyAuthorizationPolicy with Open restrictions
        // and create authorization policy
        IContentKeyAuthorizationPolicy policy = _context.
        ContentKeyAuthorizationPolicies.
        CreateAsync("Open Authorization Policy").Result;
        
        List<ContentKeyAuthorizationPolicyRestriction> restrictions =
            new List<ContentKeyAuthorizationPolicyRestriction>();

        ContentKeyAuthorizationPolicyRestriction restriction =
            new ContentKeyAuthorizationPolicyRestriction
            {
                Name = "HLS Open Authorization Policy",
                KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
                Requirements = null // no requirements needed for HLS
            };

        restrictions.Add(restriction);

        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create(
            "policy", 
            ContentKeyDeliveryType.BaselineHttp, 
            restrictions, 
            "");

        policy.Options.Add(policyOption);

        // Add ContentKeyAutorizationPolicy to ContentKey
        contentKey.AuthorizationPolicyId = policy.Id;
        IContentKey updatedKey = contentKey.UpdateAsync().Result;
        Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);
    }
```

### <a name="token-restriction"></a>Omezení s tokenem
Tato část popisuje, jak vytvořit zásadu autorizace klíče obsahu a přidružte jej k klíče k obsahu. Zásady autorizace popisuje, jaké požadavky na ověření musí být splněny k určení, pokud je uživatel oprávnění pro přijetí klíč. Například obsahuje seznam klíčů ověřovací klíč, který byl token podepsán pomocí?

Konfigurace omezení s tokenem možnost, budete muset použít XML pro popis tokenu autorizace požadavky. Konfigurace omezení s tokenem XML musí odpovídat následujícím schématu XML:
```csharp
#### Token restriction schema
    <?xml version="1.0" encoding="utf-8"?>
    <xs:schema xmlns:tns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" elementFormDefault="qualified" targetNamespace="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" xmlns:xs="http://www.w3.org/2001/XMLSchema">
      <xs:complexType name="TokenClaim">
        <xs:sequence>
          <xs:element name="ClaimType" nillable="true" type="xs:string" />
          <xs:element minOccurs="0" name="ClaimValue" nillable="true" type="xs:string" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="TokenClaim" nillable="true" type="tns:TokenClaim" />
      <xs:complexType name="TokenRestrictionTemplate">
        <xs:sequence>
          <xs:element minOccurs="0" name="AlternateVerificationKeys" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
          <xs:element name="Audience" nillable="true" type="xs:anyURI" />
          <xs:element name="Issuer" nillable="true" type="xs:anyURI" />
          <xs:element name="PrimaryVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
          <xs:element minOccurs="0" name="RequiredClaims" nillable="true" type="tns:ArrayOfTokenClaim" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="TokenRestrictionTemplate" nillable="true" type="tns:TokenRestrictionTemplate" />
      <xs:complexType name="ArrayOfTokenVerificationKey">
        <xs:sequence>
          <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ArrayOfTokenVerificationKey" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
      <xs:complexType name="TokenVerificationKey">
        <xs:sequence />
      </xs:complexType>
      <xs:element name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
      <xs:complexType name="ArrayOfTokenClaim">
        <xs:sequence>
          <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenClaim" nillable="true" type="tns:TokenClaim" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ArrayOfTokenClaim" nillable="true" type="tns:ArrayOfTokenClaim" />
      <xs:complexType name="SymmetricVerificationKey">
        <xs:complexContent mixed="false">
          <xs:extension base="tns:TokenVerificationKey">
            <xs:sequence>
              <xs:element name="KeyValue" nillable="true" type="xs:base64Binary" />
            </xs:sequence>
          </xs:extension>
        </xs:complexContent>
      </xs:complexType>
      <xs:element name="SymmetricVerificationKey" nillable="true" type="tns:SymmetricVerificationKey" />
    </xs:schema>
```
Když konfigurujete zásady omezení tokenem, musíte zadat primární ověřovací klíč, vydavatele a parametry cílovou skupinu. Primární ověřovací klíč obsahuje klíč, který byl token podepsán pomocí. Vystavitel je služba tokenů zabezpečení, které vydá token. Cílová skupina (říká se jim oboru) by měl popisovat záměr tokenu nebo prostředek token, který autorizuje přístup k. Služba Media Services doručení klíče ověří, že tyto hodnoty v tokenu odpovídají hodnotám v šabloně.

Při použití Media Services SDK pro .NET, můžete použít třídu TokenRestrictionTemplate k vygenerování tokenu omezení.
Následující příklad vytvoří zásadu autorizace se omezení s tokenem. V tomto příkladu klient musí poskytnout token, který obsahuje požadované deklarace identit, podpisový klíč (VerificationKey) a vydavatel tokenu.
```csharp
    public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
    {
        string tokenTemplateString = GenerateTokenRequirements();

        IContentKeyAuthorizationPolicy policy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("HLS token restricted authorization policy").Result;

        List<ContentKeyAuthorizationPolicyRestriction> restrictions =
                new List<ContentKeyAuthorizationPolicyRestriction>();

        ContentKeyAuthorizationPolicyRestriction restriction =
                new ContentKeyAuthorizationPolicyRestriction
                {
                    Name = "Token Authorization Policy",
                    KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                    Requirements = tokenTemplateString
                };

        restrictions.Add(restriction);

        //You could have multiple options 
        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create(
                "Token option for HLS",
                ContentKeyDeliveryType.BaselineHttp,
                restrictions,
                null  // no key delivery data is needed for HLS
                );

        policy.Options.Add(policyOption);

        // Add ContentKeyAutorizationPolicy to ContentKey
        contentKey.AuthorizationPolicyId = policy.Id;
        IContentKey updatedKey = contentKey.UpdateAsync().Result;
        Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);

        return tokenTemplateString;
    }

    static private string GenerateTokenRequirements()
    {
        TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);

        template.PrimaryVerificationKey = new SymmetricVerificationKey();
        template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
            template.Audience = _sampleAudience.ToString();
            template.Issuer = _sampleIssuer.ToString();

        template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);

        return TokenRestrictionTemplateSerializer.Serialize(template);
    }
```
#### <a name="test-token"></a>Token testu
Chcete-li získat token testů podle omezení s tokenem, který se použil pro zásady autorizace pro klíč, postupujte takto:
```csharp
    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate =
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

    // Generate a test token based on the data in the given TokenRestrictionTemplate.
    // Note, you need to pass the key id Guid because we specified 
    // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
    Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);

    //The GenerateTestToken method returns the token without the word “Bearer” in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
    Console.WriteLine();
```

## <a name="playready-dynamic-encryption"></a>Dynamického šifrování PlayReady
Služba Media Services můžete použít ke konfiguraci práv a omezení, které chcete, aby modul runtime PlayReady DRM vynucovat, když se uživatel pokusí o přehrávání chráněného obsahu. 

Pokud budete chránit obsah pomocí technologie PlayReady, jednou z věcí, je třeba zadat v zásadách autorizace je řetězec XML, který definuje [šablona licencování PlayReady](media-services-playready-license-template-overview.md). V Media Services SDK pro .NET PlayReadyLicenseResponseTemplate a PlayReadyLicenseTemplate tříd umožňují definovat šablonu licence PlayReady.

Zjistěte, jak šifrování obsahu pomocí technologie PlayReady a Widevine, najdete v článku [běžného dynamického šifrování pomocí PlayReady nebo Widevine](media-services-protect-with-playready-widevine.md).

### <a name="open-restriction"></a>Otevřít omezení
Otevřít omezení znamená, že systém poskytuje všem uživatelům, kteří vytvoří klíče požadavek klíč. Toto omezení může být užitečná pro účely testování.

Následující příklad vytvoří zásadu autorizace otevřít a přidá jej do klíče k obsahu:

```csharp
    static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
    {

        // Create ContentKeyAuthorizationPolicy with Open restrictions 
        // and create authorization policy          

        List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
        {
            new ContentKeyAuthorizationPolicyRestriction 
            { 
                Name = "Open", 
                KeyRestrictionType = (int)ContentKeyRestrictionType.Open, 
                Requirements = null
            }
        };

        // Configure PlayReady license template.
        string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create("",
                ContentKeyDeliveryType.PlayReadyLicense,
                    restrictions, newLicenseTemplate);

        IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                    ContentKeyAuthorizationPolicies.
                    CreateAsync("Deliver Common Content Key with no restrictions").
                    Result;


        contentKeyAuthorizationPolicy.Options.Add(policyOption);

        // Associate the content key authorization policy with the content key.
        contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
        contentKey = contentKey.UpdateAsync().Result;
    }
```

### <a name="token-restriction"></a>Omezení s tokenem
Konfigurace omezení s tokenem možnost, budete muset použít XML pro popis tokenu autorizace požadavky. Konfigurace omezení s tokenem XML musí odpovídat schématu XML ukazuje "[Token omezení schématu](#token-restriction-schema)" oddílu.

```csharp
    public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
    {
        string tokenTemplateString = GenerateTokenRequirements();

        IContentKeyAuthorizationPolicy policy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("HLS token restricted authorization policy").Result;

        List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
        {
            new ContentKeyAuthorizationPolicyRestriction 
            { 
                Name = "Token Authorization Policy", 
                KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                Requirements = tokenTemplateString, 
            }
        };

        // Configure PlayReady license template.
        string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                ContentKeyDeliveryType.PlayReadyLicense,
                    restrictions, newLicenseTemplate);

        IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                    ContentKeyAuthorizationPolicies.
                    CreateAsync("Deliver Common Content Key with no restrictions").
                    Result;

        policy.Options.Add(policyOption);

        // Add ContentKeyAutorizationPolicy to ContentKey
        contentKeyAuthorizationPolicy.Options.Add(policyOption);

        // Associate the content key authorization policy with the content key
        contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
        contentKey = contentKey.UpdateAsync().Result;

        return tokenTemplateString;
    }

    static private string GenerateTokenRequirements()
    {

        TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);

        template.PrimaryVerificationKey = new SymmetricVerificationKey();
        template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
            template.Audience = _sampleAudience.ToString();
            template.Issuer = _sampleIssuer.ToString();


        template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);

        return TokenRestrictionTemplateSerializer.Serialize(template);
    } 

    static private string ConfigurePlayReadyLicenseTemplate()
    {
        // The following code configures PlayReady License Template using .NET classes
        // and returns the XML string.

        //The PlayReadyLicenseResponseTemplate class represents the template for the response sent back to the end user. 
        //It contains a field for a custom data string between the license server and the application 
        //(may be useful for custom app logic) as well as a list of one or more license templates.
        PlayReadyLicenseResponseTemplate responseTemplate = new PlayReadyLicenseResponseTemplate();

        // The PlayReadyLicenseTemplate class represents a license template for creating PlayReady licenses
        // to be returned to the end users. 
        //It contains the data on the content key in the license and any rights or restrictions to be 
        //enforced by the PlayReady DRM runtime when using the content key.
        PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();
        //Configure whether the license is persistent (saved in persistent storage on the client) 
        //or non-persistent (only held in memory while the player is using the license).  
        licenseTemplate.LicenseType = PlayReadyLicenseType.Nonpersistent;

        // AllowTestDevices controls whether test devices can use the license or not.  
        // If true, the MinimumSecurityLevel property of the license
        // is set to 150.  If false (the default), the MinimumSecurityLevel property of the license is set to 2000.
        licenseTemplate.AllowTestDevices = true;


        // You can also configure the Play Right in the PlayReady license by using the PlayReadyPlayRight class. 
        // It grants the user the ability to play back the content subject to the zero or more restrictions 
        // configured in the license and on the PlayRight itself (for playback specific policy). 
        // Much of the policy on the PlayRight has to do with output restrictions 
        // which control the types of outputs that the content can be played over and 
        // any restrictions that must be put in place when using a given output.
        // For example, if the DigitalVideoOnlyContentRestriction is enabled, 
        //then the DRM runtime will only allow the video to be displayed over digital outputs 
        //(analog video outputs won’t be allowed to pass the content).

        //IMPORTANT: These types of restrictions can be very powerful but can also affect the consumer experience. 
        // If the output protections are configured too restrictive, 
        // the content might be unplayable on some clients. For more information, see the PlayReady Compliance Rules document.

        // For example:
        //licenseTemplate.PlayRight.AgcAndColorStripeRestriction = new AgcAndColorStripeRestriction(1);

        responseTemplate.LicenseTemplates.Add(licenseTemplate);

        return MediaServicesLicenseTemplateSerializer.Serialize(responseTemplate);
    }
```

Získání testovacího tokenu podle omezení s tokenem, který se použil pro zásady autorizace pro klíč, najdete v článku "[testovací token](#test-token)" oddílu. 

## <a id="types"></a>Typy používané při definování ContentKeyAuthorizationPolicy
### <a id="ContentKeyRestrictionType"></a>ContentKeyRestrictionType

```csharp
    public enum ContentKeyRestrictionType
    {
        Open = 0,
        TokenRestricted = 1,
        IPRestricted = 2,
    }
```

### <a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

```csharp 
    public enum ContentKeyDeliveryType
    {
      None = 0,
      PlayReadyLicense = 1,
      BaselineHttp = 2,
      Widevine = 3
    }
```

### <a id="TokenType"></a>Typ TokenType

```csharp
    public enum TokenType
    {
        Undefined = 0,
        SWT = 1,
        JWT = 2,
    }
```

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Další postup
Teď, když jste nakonfigurovali zásady autorizace klíče k obsahu, najdete v článku [konfigurace zásad doručení assetu](media-services-dotnet-configure-asset-delivery-policy.md).

