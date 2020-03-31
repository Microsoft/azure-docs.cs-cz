---
title: Konfigurace zásad doručování datových zdrojů pomocí rozhraní REST API služby Media Services | Dokumenty společnosti Microsoft
description: Toto téma ukazuje, jak nakonfigurovat různé zásady doručování prostředků pomocí rozhraní API REST služby Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 5cb9d32a-e68b-4585-aa82-58dded0691d0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 09f0371bc189fcf7b25ec3261e2e1f5eaf1892ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78194472"
---
# <a name="configuring-asset-delivery-policies"></a>Konfigurace zásad poskytování majetku
[!INCLUDE [media-services-selector-asset-delivery-policy](../../../includes/media-services-selector-asset-delivery-policy.md)]

Pokud plánujete dodávat dynamicky šifrované datové zdroje, jedním z kroků v pracovním postupu doručování obsahu služby Media Services je konfigurace zásad doručování datových zdrojů. Zásady doručování datových zdrojů sdělují službě Media Services, jak chcete, aby byl váš datový zdroj dodán: do kterého streamovacího protokolu by měl být dynamicky zabalen (například MPEG DASH, HLS, Smooth Streaming nebo všechny), bez ohledu na to, zda chcete dynamicky šifrovat vašeho majetku a jak (obálka nebo společné šifrování).

Toto téma popisuje, proč a jak vytvořit a nakonfigurovat zásady poskytování majetku.

> [!NOTE]
> Po vytvoření účtu AMS se do vašeho účtu přidá **výchozí** koncový bod streamování ve stavu **Zastaveno**. Pokud chcete spustit streamování vašeho obsahu a využít výhod dynamického balení a dynamického šifrování, musí koncový bod streamování, ze kterého chcete streamovat obsah, být ve stavu **Spuštěno**. 
>
> Aby bylo možné používat dynamické balení a dynamické šifrování, musí váš datový zdroj obsahovat sadu souborů MP4 s adaptivním datovým tokem nebo adaptivní datové toky.

U stejného datového zdroje můžete použít různé zásady. Můžete například použít šifrování PlayReady pro plynulé streamování a šifrování obálky AES na MPEG DASH a HLS. Veškeré protokoly, které nejsou v zásadách doručení definovány (například když přidáte jedinou zásadu, která jako protokol určuje pouze HLS), budou při streamování blokovány. Výjimkou je, pokud nemáte definovány vůbec žádné zásady doručení assetu. Pak budou všechny protokoly povolené v nešifrované podobě.

Pokud chcete dodat úložiště šifrovaný prostředek, musíte nakonfigurovat zásady doručování datového zdroje. Před datovým proudem datový chod server datových proudů odebere šifrování úložiště a streamuje váš obsah pomocí zadaných zásad doručení. Chcete-li například doručit datový zdroj zašifrovaný pomocí šifrovacího klíče obálky Advanced Encryption Standard (Advanced Encryption Standard) , nastavte typ zásady na **DynamicEnvelopeEncryption**. Chcete-li odebrat šifrování úložiště a streamovat datový zdroj v přehledné, nastavte typ zásady na **NoDynamicEncryption**. Příklady, které ukazují, jak nakonfigurovat tyto typy zásad následovat.

V závislosti na konfiguraci zásad poskytování datových zdrojů byste mohli dynamicky zakódovat, dynamicky šifrovat a streamovat následující streamovací protokoly streamování: Plynulé streamování, HLS, MPEG DASH datové proudy.

V následujícím seznamu jsou uvedeny formáty, které používáte k streamování položek Smooth, HLS, DASH.

Plynulé streamování:

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Hls:

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG DASH

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)


Pokyny k publikování assetu a vytvoření adresy URL streamování najdete v článku o [vytvoření adresy URL streamování](media-services-deliver-streaming-content.md).

## <a name="considerations"></a>Požadavky
* AssetDeliveryPolicy přidružené k prostředku nelze odstranit, pokud pro tento prostředek existuje lokátor OnDemand (streaming). Doporučujeme odebrat zásadu z majetku před odstraněním zásady.
* Lokátor streamování nelze vytvořit na šifrovaném datovém zdroji úložiště, pokud není nastavena žádná zásada doručování datových zdrojů.  Pokud prostředek není zašifrovaný úložištěm, systém vám umožní vytvořit lokátor a streamovat datový zdroj v přehledné bez zásad poskytování majetku.
* K jednomu majetku můžete mít přidruženo více zásad pro doručování majetku, ale můžete určit pouze jeden způsob zpracování daného protokolu AssetDeliveryProtocol.  To znamená, že pokud se pokusíte propojit dvě zásady doručení, které určují protokol AssetDeliveryProtocol.SmoothStreaming, což bude mít za následek chybu, protože systém neví, který z nich má použít, když klient vytvoří požadavek na plynulé streamování.
* Pokud máte datový zdroj s existujícím lokátorem streamování, nemůžete s ním propojit novou zásadu, odpojit existující zásady od datového zdroje nebo aktualizovat zásady doručování přidružené k datovému zdroji.  Nejprve je nutné odebrat lokátor streamování, upravit zásady a potom znovu vytvořit lokátor streamování.  Můžete použít stejné locatorId při vytváření lokátoru streamování, ale měli byste zajistit, že nebude způsobovat problémy pro klienty, protože obsah může být uložen do mezipaměti podle původu nebo následné cdn.

> [!NOTE]
> 
> Při přístupu k entitám ve službě Media Services je nutné nastavit konkrétní pole záhlaví a hodnoty v požadavcích HTTP. Další informace naleznete [v tématu Setup for Media Services REST API Development](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Připojení ke službě Media Services

Informace o tom, jak se připojit k rozhraní AMS API, najdete [v tématu Přístup k rozhraní API Azure Media Services pomocí ověřování Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="clear-asset-delivery-policy"></a>Vymazat zásady doručování majetku
### <a name="create-asset-delivery-policy"></a><a id="create_asset_delivery_policy"></a>Vytvořit zásady doručování majetku
Následující požadavek HTTP vytvoří zásady doručování datových zdrojů, které určují nepoužití dynamického šifrování a doručení datového proudu v některém z následujících protokolů: MPEG DASH, HLS a Smooth Streaming protokoly. 

Informace o tom, jaké hodnoty můžete zadat při vytváření AssetDeliveryPolicy, naleznete [v části Typy použité při definování AssetDeliveryPolicy.](#types)   

Požadavek:

    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19
    x-ms-client-request-id: 4651882c-d7ad-4d5e-86ab-f07f47dcb41e
    Host: media.windows.net

    {"Name":"Clear Policy",
    "AssetDeliveryProtocol":7,
    "AssetDeliveryPolicyType":2,
    "AssetDeliveryConfiguration":null}

Odpověď:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 363
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3A92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 4651882c-d7ad-4d5e-86ab-f07f47dcb41e
    request-id: 6aedbf93-4bc2-4586-8845-fd45590136af
    x-ms-request-id: 6aedbf93-4bc2-4586-8845-fd45590136af
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 08 Feb 2015 06:21:27 GMT

    {"odata.metadata":"https://media.windows.net/api/$metadata#AssetDeliveryPolicies/@Element",
    "Id":"nb:adpid:UUID:92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd",
    "Name":"Clear Policy",
    "AssetDeliveryProtocol":7,
    "AssetDeliveryPolicyType":2,
    "AssetDeliveryConfiguration":null,
    "Created":"2015-02-08T06:21:27.6908329Z",
    "LastModified":"2015-02-08T06:21:27.6908329Z"}

### <a name="link-asset-with-asset-delivery-policy"></a><a id="link_asset_with_asset_delivery_policy"></a>Propojení majetku se zásadami poskytování majetku
Následující požadavek HTTP propojuje zadaný majetek se zásadami doručování majetku.

Požadavek:

    POST https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A86933344-9539-4d0c-be7d-f842458693e0')/$links/DeliveryPolicies HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Content-Type: application/json
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19
    x-ms-client-request-id: 56d2763f-6e72-419d-ba3c-685f6db97e81
    Host: media.windows.net

    {"uri":"https://media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3A92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd')"}

Odpověď:

    HTTP/1.1 204 No Content


## <a name="dynamicenvelopeencryption-asset-delivery-policy"></a>Zásady doručování datových zdrojů DynamicEnvelopeEncryption
### <a name="create-content-key-of-the-envelopeencryption-type-and-link-it-to-the-asset"></a>Vytvořit klíč obsahu typu EnvelopeEncryption a propojit ho s datovým zdrojem
Při zadávání zásad doručování DynamicEnvelopeEncryption je třeba zajistit propojení datového zdroje s klíčem obsahu typu EnvelopeEncryption. Další informace naleznete v [tématu: Vytvoření klíče obsahu](media-services-rest-create-contentkey.md).

### <a name="get-delivery-url"></a><a id="get_delivery_url"></a>Získat adresu URL doručení
Získejte adresu URL doručení pro zadanou metodu doručení klíče obsahu vytvořeného v předchozím kroku. Klient používá vrácenou adresu URL k vyžádání klíče AES nebo licence PlayReady za účelem přehrávání chráněného obsahu.

Zadejte typ adresy URL, která se má získat v textu požadavku HTTP. Pokud chráníte svůj obsah pomocí služby PlayReady, požádejte o adresu URL pro získání licence PlayReady služby Media Services, která používá 1 pro keyDeliveryType: {"keyDeliveryType":1}. Pokud chráníte svůj obsah šifrováním obálky, požádejte o adresu URL pro získání klíče zadáním adresy 2 pro keyDeliveryType: {"keyDeliveryType":2}.

Požadavek:

    POST https://media.windows.net/api/ContentKeys('nb:kid:UUID:dc88f996-2859-4cf7-a279-c52a9d6b2f04')/GetKeyDeliveryUrl HTTP/1.1
    Content-Type: application/json
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19
    x-ms-client-request-id: 569d4b7c-a446-4edc-b77c-9fb686083dd8
    Host: media.windows.net
    Content-Length: 21

    {"keyDeliveryType":2}

Odpověď:

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 198
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 569d4b7c-a446-4edc-b77c-9fb686083dd8
    request-id: d26f65d2-fe65-4136-8fcf-31545be68377
    x-ms-request-id: d26f65d2-fe65-4136-8fcf-31545be68377
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 08 Feb 2015 21:42:30 GMT

    {"odata.metadata":"media.windows.net/api/$metadata#Edm.String","value":"https://amsaccount1.keydelivery.mediaservices.windows.net/?KID=dc88f996-2859-4cf7-a279-c52a9d6b2f04"}


### <a name="create-asset-delivery-policy"></a>Vytvořit zásady doručování majetku
Následující požadavek HTTP vytvoří **AssetDeliveryPolicy,** který je nakonfigurován pro použití dynamického šifrování obálky **(DynamicEnvelopeEncryption**) na protokol **HLS** (v tomto příkladu budou ostatní protokoly blokovány streamováním). 

Informace o tom, jaké hodnoty můžete zadat při vytváření AssetDeliveryPolicy, naleznete [v části Typy použité při definování AssetDeliveryPolicy.](#types)   

Požadavek:

    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19
    x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
    Host: media.windows.net

    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":4,"AssetDeliveryPolicyType":3,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\\/\"}]"}


Odpověď:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 460
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3Aec9b994e-672c-4a5b-8490-a464eeb7964b')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
    request-id: c2a1ac0e-9644-474f-b38f-b9541c3a7c5f
    x-ms-request-id: c2a1ac0e-9644-474f-b38f-b9541c3a7c5f
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 09 Feb 2015 05:24:38 GMT

    {"odata.metadata":"media.windows.net/api/$metadata#AssetDeliveryPolicies/@Element","Id":"nb:adpid:UUID:ec9b994e-672c-4a5b-8490-a464eeb7964b","Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":4,"AssetDeliveryPolicyType":3,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\\/\"}]","Created":"2015-02-09T05:24:38.9167436Z","LastModified":"2015-02-09T05:24:38.9167436Z"}


### <a name="link-asset-with-asset-delivery-policy"></a>Propojení majetku se zásadami poskytování majetku
Viz [Propojení majetku se zásadami poskytování majetku](#link_asset_with_asset_delivery_policy)

## <a name="dynamiccommonencryption-asset-delivery-policy"></a>Zásady doručování datových zdrojů DynamicCommonEncryption
### <a name="create-content-key-of-the-commonencryption-type-and-link-it-to-the-asset"></a>Vytvořit klíč obsahu typu CommonEncryption a propojit ho s datovým zdrojem
Při zadávání zásad doručování DynamicCommonEncryption je třeba zajistit propojení datového zdroje s klíčem obsahu typu CommonEncryption. Další informace naleznete v [tématu: Vytvoření klíče obsahu](media-services-rest-create-contentkey.md).

### <a name="get-delivery-url"></a>Získat adresu URL pro doručování
Získejte adresu URL doručení pro metodu doručení PlayReady klíče obsahu vytvořeného v předchozím kroku. Klient používá vrácenou adresu URL k vyžádání licence PlayReady za účelem přehrávání chráněného obsahu. Další informace naleznete v tématu [Získání adresy URL doručení](#get_delivery_url).

### <a name="create-asset-delivery-policy"></a>Vytvořit zásady doručování majetku
Následující požadavek HTTP vytvoří **AssetDeliveryPolicy,** který je nakonfigurován tak, aby použít dynamické společné šifrování (**DynamicCommonEncryption**) na protokol **Hladký datový proud** (v tomto příkladu budou ostatní protokoly blokovány streamování). 

Informace o tom, jaké hodnoty můžete zadat při vytváření AssetDeliveryPolicy, naleznete [v části Typy použité při definování AssetDeliveryPolicy.](#types)   

Požadavek:

    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19
    x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
    Host: media.windows.net

    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":1,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\/PlayReady\/"}]"}


Pokud chcete chránit svůj obsah pomocí Widevine DRM, aktualizujte AssetDeliveryConfiguration hodnoty použít WidevineLicenseAcquisitionUrl (který má hodnotu 7) a zadejte adresu URL služby doručování licencí. Můžete použít následující partnery AMS, kteří vám pomohou dodat licence Widevine: [Axinom](https://www.axinom.com), [EZDRM](https://ezdrm.com/), [castLabs](https://castlabs.com/company/partners/azure/).

Například: 

    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":2,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":7,\"Value\":\"https:\\/\\/example.net\/WidevineLicenseAcquisition\/"}]"}

> [!NOTE]
> Při šifrování pomocí Widevine, budete moci dodat pouze pomocí DASH. Ujistěte se, že jste v protokolu o dodání majetku zadali dash (2).
> 
> 

### <a name="link-asset-with-asset-delivery-policy"></a>Propojení majetku se zásadami poskytování majetku
Viz [Propojení majetku se zásadami poskytování majetku](#link_asset_with_asset_delivery_policy)

## <a name="types-used-when-defining-assetdeliverypolicy"></a><a id="types"></a>Typy používané při definování AssetDeliveryPolicy

### <a name="assetdeliveryprotocol"></a>AssetDeliveryProtocol

Následující výčet popisuje hodnoty, které můžete nastavit pro protokol dodání majetku.

    [Flags]
    public enum AssetDeliveryProtocol
    {
        /// <summary>
        /// No protocols.
        /// </summary>
        None = 0x0,

        /// <summary>
        /// Smooth streaming protocol.
        /// </summary>
        SmoothStreaming = 0x1,

        /// <summary>
        /// MPEG Dynamic Adaptive Streaming over HTTP (DASH)
        /// </summary>
        Dash = 0x2,

        /// <summary>
        /// Apple HTTP Live Streaming protocol.
        /// </summary>
        HLS = 0x4,

        ProgressiveDownload = 0x10, 
 
        /// <summary>
        /// Include all protocols.
        /// </summary>
        All = 0xFFFF
    }

### <a name="assetdeliverypolicytype"></a>AssetDeliveryPolicyType

Následující výčet popisuje hodnoty, které můžete nastavit pro typ zásad doručování majetku.  

    public enum AssetDeliveryPolicyType
    {
        /// <summary>
        /// Delivery Policy Type not set.  An invalid value.
        /// </summary>
        None,

        /// <summary>
        /// The Asset should not be delivered via this AssetDeliveryProtocol. 
        /// </summary>
        Blocked, 

        /// <summary>
        /// Do not apply dynamic encryption to the asset.
        /// </summary>
        /// 
        NoDynamicEncryption,  

        /// <summary>
        /// Apply Dynamic Envelope encryption.
        /// </summary>
        DynamicEnvelopeEncryption,

        /// <summary>
        /// Apply Dynamic Common encryption.
        /// </summary>
        DynamicCommonEncryption
        }

### <a name="contentkeydeliverytype"></a>ContentKeyDeliveryType

Následující výčet popisuje hodnoty, které můžete použít ke konfiguraci metody doručení klíče obsahu klientovi.
    
    public enum ContentKeyDeliveryType
    {
        /// <summary>
        /// None.
        ///
        </summary>
        None = 0,

        /// <summary>
        /// Use PlayReady License acquisition protocol
        ///
        </summary>
        PlayReadyLicense = 1,

        /// <summary>
        /// Use MPEG Baseline HTTP key protocol.
        ///
        </summary>
        BaselineHttp = 2,

        /// <summary>
        /// Use Widevine License acquisition protocol
        ///
        </summary>
        Widevine = 3

    }


### <a name="assetdeliverypolicyconfigurationkey"></a>AssetDeliveryPolicyConfigurationKey

Následující výčet popisuje hodnoty, které můžete nastavit pro konfiguraci klíčů používaných k získání konkrétní konfigurace pro zásady doručování majetku.

    public enum AssetDeliveryPolicyConfigurationKey
    {
        /// <summary>
        /// No policies.
        /// </summary>
        None,

        /// <summary>
        /// Exact Envelope key URL.
        /// </summary>
        EnvelopeKeyAcquisitionUrl,

        /// <summary>
        /// Base key url that will have KID=<Guid> appended for Envelope.
        /// </summary>
        EnvelopeBaseKeyAcquisitionUrl,

        /// <summary>
        /// The initialization vector to use for envelope encryption in Base64 format.
        /// </summary>
        EnvelopeEncryptionIVAsBase64,

        /// <summary>
        /// The PlayReady License Acquisition Url to use for common encryption.
        /// </summary>
        PlayReadyLicenseAcquisitionUrl,

        /// <summary>
        /// The PlayReady Custom Attributes to add to the PlayReady Content Header
        /// </summary>
        PlayReadyCustomAttributes,

        /// <summary>
        /// The initialization vector to use for envelope encryption.
        /// </summary>
        EnvelopeEncryptionIV,

        /// <summary>
        /// Widevine DRM acquisition url
        /// </summary>
        WidevineLicenseAcquisitionUrl
    }

## <a name="additional-notes"></a>Další poznámky

* Widevine je služba poskytovaná společností Google Inc. a podléhá podmínkám služeb a zásadám ochrany osobních údajů společnosti Google, Inc.

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

