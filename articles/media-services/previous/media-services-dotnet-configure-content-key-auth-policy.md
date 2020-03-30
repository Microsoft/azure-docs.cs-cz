---
title: Konfigurace zásad autorizace klíče obsahu pomocí sady Media Services .NET SDK | Dokumenty společnosti Microsoft
description: Přečtěte si, jak nakonfigurovat zásady autorizace pro klíč obsahu pomocí sady Media Services .NET SDK.
services: media-services
documentationcenter: ''
author: mingfeiy
manager: femila
editor: ''
ms.assetid: 1a0aedda-5b87-4436-8193-09fc2f14310c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 58d52cd194ca4391c61f2477189984273df1198a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251203"
---
# <a name="configure-a-content-key-authorization-policy"></a>Konfigurace zásad autorizace klíče obsahu

[!INCLUDE [media-services-selector-content-key-auth-policy](../../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Přehled
 Pomocí mediálních služeb Azure můžete doručovat datové proudy MPEG-DASH, Smooth Streaming a HTTP Live Streaming (HLS) chráněné standardem Advanced Encryption Standard (AES) pomocí 128bitových šifrovacích klíčů nebo [správy digitálních práv PlayReady (DRM).](https://www.microsoft.com/playready/overview/) Pomocí služby Media Services můžete také dodávat datové proudy DASH šifrované pomocí widevine DRM. Technologie PlayReady i Widevine jsou šifrované podle specifikace Common Encryption (ISO/IEC CENC 23001-7).

Služba Media Services také poskytuje službu doručování klíčů a licencí, ze které mohou klienti získat klíče AES nebo licence PlayReady/Widevine k přehrávání šifrovaného obsahu.

Pokud chcete, aby media services šifrovat prostředek, je třeba přidružit šifrovací klíč (CommonEncryption nebo EnvelopeEncryption) s datovým zdrojem. Další informace naleznete v [tématu Create ContentKeys with .NET](media-services-dotnet-create-contentkey.md). Je také nutné nakonfigurovat zásady autorizace pro klíč (jak je popsáno v tomto článku).

Pokud je datový proud požadován přehrávačem, služba Media Services používá zadaný klíč k dynamickému šifrování obsahu pomocí šifrování AES nebo DRM. K dešifrování streamu si přehrávač vyžádá klíč ze služby doručování klíčů. Chcete-li zjistit, zda je uživatel oprávněn získat klíč, služba vyhodnotí zásady autorizace, které jste zadali pro klíč.

Služba Media Services podporuje více způsobů ověřování uživatelů, kteří žádají o klíč. Zásady autorizace klíče obsahu mohou mít jedno nebo více omezení autorizace. Možnosti jsou otevřené nebo omezení tokenu. Zásady omezení tokenem musí být doplněny tokenem vydaným službou tokenů zabezpečení (STS). Media Services podporuje tokeny ve formátu jednoduchého webového tokenu[(SWT)](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)a ve formátu JSON Web Token ([JWT).](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3)

Mediální služby neposkytují STS. Můžete vytvořit vlastní STS nebo použít službu Řízení přístupu Azure k vydávání tokenů. Služba STS musí být nakonfigurována tak, aby vytvořila token podepsaný zadaným klíčem a vydala deklarace identity, které jste zadali v konfiguraci omezení tokenu (jak je popsáno v tomto článku). Pokud je token platný a deklarace identity v tokenu odpovídají deklaracím nakonfigurovaným pro klíč obsahu, vrátí služba doručování klíčů Mediální služby šifrovací klíč klientovi.

Další informace najdete v těchto článcích:

- [Ověřování tokenů JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
- [Integrace aplikace založená na Azure Media Services OWIN MVC s Azure Active Directory a omezení doručování klíčů obsahu na základě deklarací JWT](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

### <a name="some-considerations-apply"></a>Platí některé aspekty
* Po vytvoření účtu Media Services se do vašeho účtu přidá výchozí koncový bod streamování ve stavu Zastaveno. Chcete-li spustit streamování obsahu a využít výhod dynamického balení a dynamického šifrování, musí být koncový bod streamování ve stavu Spuštěno. 
* Datový zdroj musí obsahovat sadu souborů MP4 s adaptivním datovým tokem nebo adaptivní datové rychlosti Plynulé streamování. Další informace naleznete [v tématu Kódování datového zdroje](media-services-encode-asset.md).
* Nahrajte a zakódujte datové zdroje pomocí možnosti AssetCreationOptions.StorageEncrypted.
* Pokud plánujete mít více klíčů obsahu, které vyžadují stejnou konfiguraci zásad, doporučujeme vytvořit jednu zásadu autorizace a znovu ji použít s více klíči obsahu.
* Služba doručování klíčů ukládá do mezipaměti ContentKeyAuthorizationPolicy a jeho související objekty (možnosti zásad a omezení) po dobu 15 minut. Můžete vytvořit ContentKeyAuthorizationPolicy a určit použití omezení tokenu, otestovat a potom aktualizovat zásady na otevřené omezení. Tento proces trvá přibližně 15 minut, než se zásada přepne na otevřenou verzi zásady.
* Pokud přidáte nebo aktualizujete zásady pro doručení prostředku, musíte odstranit stávající lokátor a vytvořit nový.
* V současné době nelze šifrovat progresivní stahování.
* Koncový bod streamování služby Media Services nastaví hodnotu hlavičky CORS "Access-Control-Allow-Origin" v odpovědi kontroly před výstupem jako zástupný znak .\* Tato hodnota funguje dobře s většinou hráčů, včetně Azure Media Player, Roku a JWPlayer a další. Někteří hráči, kteří používají dashjs však nefungují, protože s režimpověření nastavena na "zahrnout", XMLHttpRequest\*v jejich dashjs neumožňuje zástupný znak " " jako hodnotu 'Access-Control-Allow-Origin'. Jako řešení tohoto omezení v dashjs, pokud hostujete klienta z jedné domény, media services můžete určit, že doména v záhlaví odpovědi kontroly před výstupem. Pro pomoc otevřete lístek podpory prostřednictvím portálu Azure.

## <a name="aes-128-dynamic-encryption"></a>Dynamické šifrování AES-128
### <a name="open-restriction"></a>Otevřené omezení
Otevřené omezení znamená, že systém doručí klíč každému, kdo požádá o klíč. Toto omezení může být užitečné pro účely testování.

Následující příklad vytvoří otevřenou zásadu autorizace a přidá ji do klíče obsahu:
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

        // Add ContentKeyAuthorizationPolicy to ContentKey
        contentKey.AuthorizationPolicyId = policy.Id;
        IContentKey updatedKey = contentKey.UpdateAsync().Result;
        Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);
    }
```

### <a name="token-restriction"></a>Omezení tokenu
Tato část popisuje, jak vytvořit zásadu autorizace klíče obsahu a přidružit ji ke klíči obsahu. Zásady autorizace popisují, jaké požadavky na autorizaci musí být splněny, aby bylo možné určit, zda je uživatel oprávněn přijímat klíč. Obsahuje například seznam ověřovacích klíčů klíč, se kterým byl token podepsán?

Chcete-li nakonfigurovat možnost omezení tokenu, musíte použít XML k popisu požadavků na autorizaci tokenu. Konfigurace xml omezení tokenu musí odpovídat následujícímu schématu XML:
```csharp
#### Token restriction schema
    <?xml version="1.0" encoding="utf-8"?>
    <xs:schema xmlns:tns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" elementFormDefault="qualified" targetNamespace="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" xmlns:xs="https://www.w3.org/2001/XMLSchema">
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
Při konfiguraci zásad s omezeným přístupem tokenu je nutné zadat primární ověřovací klíč, vystavitel a parametry publika. Primární ověřovací klíč obsahuje klíč, se kterým byl token podepsán. Vystavitel je STS, který vydává token. Cílová skupina (někdy označovaná jako obor) popisuje záměr tokenu nebo prostředku, ke kterému token autorizuje přístup. Služba doručování klíčů Mediální služby ověří, zda tyto hodnoty v tokenu odpovídají hodnotám v šabloně.

Při použití sady Media Services SDK pro rozhraní .NET můžete použít třídu TokenRestrictionTemplate ke generování tokenu omezení.
Následující příklad vytvoří zásadu autorizace s omezením tokenu. V tomto příkladu musí klient předložit token, který obsahuje podpisový klíč (VerificationKey), vystavitele tokenu a požadované deklarace identity.
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

        // Add ContentKeyAuthorizationPolicy to ContentKey
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
#### <a name="test-token"></a>Testovací token
Chcete-li získat testovací token založený na omezení tokenu, který byl použit pro zásady autorizace klíče, postupujte takto:
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

## <a name="playready-dynamic-encryption"></a>Dynamické šifrování PlayReady
Pomocí služby Media Services můžete nakonfigurovat práva a omezení, která má zaběhu PlayReady DRM vynutit, když se uživatel pokusí přehrát chráněný obsah. 

Při ochraně obsahu pomocí služby PlayReady je jednou z věcí, které je třeba zadat v zásadách autorizace, řetězec XML, který definuje [licenční šablonu PlayReady](media-services-playready-license-template-overview.md). V sadě Media Services SDK pro rozhraní .NET vám třídy PlayReadyLicenseResponseTemplate a PlayReadyLicenseTemplate pomohou definovat šablonu licence PlayReady.

Informace o šifrování obsahu pomocí funkcí PlayReady a Widevine naleznete [v tématech Použití dynamického běžného šifrování PlayReady a/nebo Widevine](media-services-protect-with-playready-widevine.md).

### <a name="open-restriction"></a>Otevřené omezení
Otevřené omezení znamená, že systém doručí klíč každému, kdo požádá o klíč. Toto omezení může být užitečné pro účely testování.

Následující příklad vytvoří otevřenou zásadu autorizace a přidá ji do klíče obsahu:

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

### <a name="token-restriction"></a>Omezení tokenu
Chcete-li nakonfigurovat možnost omezení tokenu, musíte použít XML k popisu požadavků na autorizaci tokenu. Konfigurace xml omezení tokenu musí odpovídat schématu XML zobrazenému v části Schéma omezení tokenů.

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

        // Add ContentKeyAuthorizationPolicy to ContentKey
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

Chcete-li získat testovací token založený na omezení tokenu, který byl použit pro zásady autorizace klíče, naleznete v části[" Test token](#test-token)". 

## <a name="types-used-when-you-define-contentkeyauthorizationpolicy"></a><a id="types"></a>Typy používané při definování zásad ContentKeyAuthorizationPolicy
### <a name="contentkeyrestrictiontype"></a><a id="ContentKeyRestrictionType"></a>ContentKeyRestrictionType

```csharp
    public enum ContentKeyRestrictionType
    {
        Open = 0,
        TokenRestricted = 1,
        IPRestricted = 2,
    }
```

### <a name="contentkeydeliverytype"></a><a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

```csharp 
    public enum ContentKeyDeliveryType
    {
      None = 0,
      PlayReadyLicense = 1,
      BaselineHttp = 2,
      Widevine = 3
    }
```

### <a name="tokentype"></a><a id="TokenType"></a>Typ tokenu

```csharp
    public enum TokenType
    {
        Undefined = 0,
        SWT = 1,
        JWT = 2,
    }
```

## <a name="additional-notes"></a>Další poznámky

* Widevine je služba poskytovaná společností Google Inc. a podléhá podmínkám služeb a zásadám ochrany osobních údajů společnosti Google, Inc.

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Další kroky
Teď, když jste nakonfigurovali zásady autorizace klíče obsahu, najdete [v tématu Konfigurace zásad pro doručování majetku](media-services-dotnet-configure-asset-delivery-policy.md).

