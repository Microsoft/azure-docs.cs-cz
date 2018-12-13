---
title: Konfigurace zásad doručení assetu pomocí REST API služby Media Services | Dokumentace Microsoftu
description: Toto téma ukazuje, jak konfigurace zásad doručování různých prostředků pomocí REST API služby Media Services.
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
ms.date: 12/07/2017
ms.author: juliako
ms.openlocfilehash: dea12d7188b716b4a832a33bb173201e68dbe20f
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53189740"
---
# <a name="configuring-asset-delivery-policies"></a>Konfigurace zásad doručení assetu
[!INCLUDE [media-services-selector-asset-delivery-policy](../../../includes/media-services-selector-asset-delivery-policy.md)]

Pokud budete chtít doručování dynamicky šifrovaných prostředků, jeden z kroků v pracovním postupu doručování obsahu v Media Services je konfigurace zásad doručování prostředků. Zásady doručení assetu říká Media Services způsob pro váš prostředek, který bude doručen: do datových proudů protokol, který by měl váš asset dynamicky balí (pro příklad, MPEG DASH, HLS, technologie Smooth Streaming nebo všechny), jestli chcete šifrovat dynamicky váš asset a jak (Obálka nebo používat standard common encryption).

Toto téma popisuje, proč a jak vytvořit a nakonfigurovat zásady doručení assetu.

> [!NOTE]
> Po vytvoření účtu AMS se do vašeho účtu přidá **výchozí** koncový bod streamování ve stavu **Zastaveno**. Pokud chcete spustit streamování vašeho obsahu a využít výhod dynamického balení a dynamického šifrování, musí koncový bod streamování, ze kterého chcete streamovat obsah, být ve stavu **Spuštěno**. 
>
> Abyste mohli používat dynamické balení a dynamického šifrování také, váš asset musí obsahovat sadu s adaptivní přenosovou rychlostí soubory MP4 rychlostmi nebo soubory technologie Smooth Streaming s adaptivní přenosovou rychlostí.

Do stejného assetu můžete použít různé zásady. Může například použít šifrování PlayReady na technologie Smooth Streaming a standardu AES Envelope šifrování a MPEG DASH, HLS. Veškeré protokoly, které nejsou v zásadách doručení definovány (například když přidáte jedinou zásadu, která jako protokol určuje pouze HLS), budou při streamování blokovány. Výjimkou je, pokud nemáte definovány vůbec žádné zásady doručení assetu. Pak budou všechny protokoly povolené v nešifrované podobě.

Pokud chcete dodávat šifrované prostředků úložiště, musíte nakonfigurovat zásady doručení assetu. Předtím, než můžete Streamovat prostředek, server streamování zruší šifrování úložiště a streamuje obsah pomocí zadaného doručování zásad. Například k poskytování asset zašifrovaná pomocí šifrování AES (Advanced Standard) obálky šifrovací klíč, nastavte typ zásad na **DynamicEnvelopeEncryption**. Pokud chcete odstranit úložiště šifrování a streamování majetku v nezašifrované podobě, nastavte typ zásad na **NoDynamicEncryption**. Postupujte podle příkladů, které ukazují, jak nakonfigurovat tyto typy zásad.

V závislosti na tom, jak nakonfigurovat zásady doručení assetu by budete moci dynamicky balíček, dynamické šifrování a streamování následující streamovacích protokolů: Funkce Smooth Streaming, HLS, MPEG DASH datové proudy.

Následující seznam obsahuje formát, který používáte k datový proud Smooth, HLS, DASH.

Technologie Smooth Streaming:

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

HLS:

{streamování koncový bod služby media název účtu name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG DASH

{streamování koncový bod služby media název účtu name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)


Pokyny k publikování assetu a vytvoření adresy URL streamování najdete v článku o [vytvoření adresy URL streamování](media-services-deliver-streaming-content.md).

## <a name="considerations"></a>Požadavky
* Nelze odstranit AssetDeliveryPolicy přidružené k prostředku, zatímco Lokátor OnDemand (streamování) existuje pro tohoto prostředku. Doporučujeme odebrat zásadu z prostředku před odstraněním zásady.
* Šifrované prostředků úložiště nelze vytvořit lokátor streamování, když nejsou nastavené žádné zásady doručení assetu.  Pokud prostředek není šifrování úložiště, systém vám umožní vytvořit Lokátor a streamování prostředků v nezašifrované podobě bez zásad doručení prostředku.
* Můžete mít více zásad doručování prostředků přidružené k jedné assetu, ale můžete zadat pouze jeden způsob, jak zpracovat daný AssetDeliveryProtocol.  To znamená, pokud se pokusíte k propojení dvou zásady doručování, které určují AssetDeliveryProtocol.SmoothStreaming protokol, který způsobí chybu, protože systém neví, což je ta, ve které chcete použít, když klient odešle požadavek technologie Smooth Streaming.
* Pokud máte prostředek s stávající Lokátor streamování, nelze propojit novou zásadu k assetu, odpojit existující zásady z prostředku nebo aktualizujete zásady pro doručení přidružené k assetu.  Nejprve musíte odebrat Lokátor streamování, upravte zásady a pak znovu vytvořte Lokátor streamování.  Když znovu vytvořit lokátor streamování, ale měli byste zajistit, že, který nebude způsobovat problémy pro klienty od mezipaměti obsahu počátek nebo příjem dat CDN, můžete použít stejné locatorId.

> [!NOTE]
> 
> Při přístupu k entity ve službě Media Services, musíte nastavit specifická pole hlaviček a hodnoty v požadavcích HTTP. Další informace najdete v tématu [instalace pro vývoj pro Media Services REST API](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Připojení ke službě Media Services

Informace o tom, jak se připojit k rozhraní API pro AMS, naleznete v tématu [přístup k rozhraní API Azure Media Services pomocí ověřování Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="clear-asset-delivery-policy"></a>Zásady doručení assetu vymazat
### <a id="create_asset_delivery_policy"></a>Vytvoření zásady doručení assetu
Následující požadavek HTTP vytvoří zásad doručení prostředku, který určuje k dynamické šifrování se nedá použít a k poskytování datový proud v některém z těchto protokolů:  Protokoly, MPEG DASH, HLS a Smooth Streaming. 

Informace o hodnoty, můžete zadat při vytváření AssetDeliveryPolicy, najdete v článku [typy používané při definování AssetDeliveryPolicy](#types) oddílu.   

Požadavek:

    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
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

### <a id="link_asset_with_asset_delivery_policy"></a>Propojení prostředku s zásady doručení assetu
Následující požadavek HTTP odkazy na zásady doručení assetu pro zadaný prostředek.

Požadavek:

    POST https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A86933344-9539-4d0c-be7d-f842458693e0')/$links/DeliveryPolicies HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Content-Type: application/json
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: 56d2763f-6e72-419d-ba3c-685f6db97e81
    Host: media.windows.net

    {"uri":"https://media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3A92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd')"}

Odpověď:

    HTTP/1.1 204 No Content


## <a name="dynamicenvelopeencryption-asset-delivery-policy"></a>DynamicEnvelopeEncryption zásady doručení assetu
### <a name="create-content-key-of-the-envelopeencryption-type-and-link-it-to-the-asset"></a>Vytvoření klíče k obsahu typu EnvelopeEncryption a připojit ho k assetu
Při zadávání DynamicEnvelopeEncryption zásady pro doručení, budete muset Ujistěte se, že odkaz na klíč k obsahu typu EnvelopeEncryption asset. Další informace naleznete v tématu: [Vytvoření klíče k obsahu](media-services-rest-create-contentkey.md)).

### <a id="get_delivery_url"></a>Získat adresu URL doručování
Získáte adresu URL doručování pro metodu zadané doručování obsahu klíč vytvořený v předchozím kroku. Klient používá vrácené adresu URL k vyžádání klíčem standardu AES nebo PlayReady licence v pořadí pro přehrávání chráněného obsahu.

Zadejte adresu URL získat v textu požadavku HTTP. Když chcete chránit obsah pomocí technologie PlayReady, žádost o adresu URL získání licencí Media Services PlayReady pomocí 1 pro keyDeliveryType: {"keyDeliveryType": 1}. Pokud chráníte obsahu pomocí šifrování obálky, adresa URL klíče pořízení požadavku tak, že zadáte 2 pro keyDeliveryType: {"keyDeliveryType": 2}.

Požadavek:

    POST https://media.windows.net/api/ContentKeys('nb:kid:UUID:dc88f996-2859-4cf7-a279-c52a9d6b2f04')/GetKeyDeliveryUrl HTTP/1.1
    Content-Type: application/json
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
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


### <a name="create-asset-delivery-policy"></a>Vytvoření zásady doručení assetu
Vytvoří následující požadavek HTTP **AssetDeliveryPolicy** , který je nakonfigurovaný na použití obálky dynamického šifrování (**DynamicEnvelopeEncryption**) k **HLS** protokol (v tomto příkladu jiné protokoly budou při streamování blokovány). 

Informace o hodnoty, můžete zadat při vytváření AssetDeliveryPolicy, najdete v článku [typy používané při definování AssetDeliveryPolicy](#types) oddílu.   

Požadavek:

    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
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


### <a name="link-asset-with-asset-delivery-policy"></a>Propojení prostředku s zásady doručení assetu
Zobrazit [propojení prostředku s zásady doručení assetu](#link_asset_with_asset_delivery_policy)

## <a name="dynamiccommonencryption-asset-delivery-policy"></a>DynamicCommonEncryption zásady doručení assetu
### <a name="create-content-key-of-the-commonencryption-type-and-link-it-to-the-asset"></a>Vytvoření klíče k obsahu typu CommonEncryption a připojit ho k assetu
Při zadávání DynamicCommonEncryption zásady pro doručení, budete muset Ujistěte se, že odkaz na klíč k obsahu typu CommonEncryption asset. Další informace naleznete v tématu: [Vytvoření klíče k obsahu](media-services-rest-create-contentkey.md)).

### <a name="get-delivery-url"></a>Získat adresu URL doručování
Získáte adresu URL doručování pro metodu doručení PlayReady obsahu klíče vytvořené v předchozím kroku. Klient použije adresu URL vrácené žádat o licenci PlayReady v pořadí pro přehrávání chráněného obsahu. Další informace najdete v tématu [získat adresu URL doručování](#get_delivery_url).

### <a name="create-asset-delivery-policy"></a>Vytvoření zásady doručení assetu
Vytvoří následující požadavek HTTP **AssetDeliveryPolicy** , který je nakonfigurovaný na použití běžného dynamického šifrování (**DynamicCommonEncryption**) k **technologie Smooth Streaming**protokolu (v tomto příkladu jiné protokoly budou při streamování blokovány). 

Informace o hodnoty, můžete zadat při vytváření AssetDeliveryPolicy, najdete v článku [typy používané při definování AssetDeliveryPolicy](#types) oddílu.   

Požadavek:

    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
    Host: media.windows.net

    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":1,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\/PlayReady\/"}]"}


Pokud chcete chránit obsah pomocí Widevine DRM, aktualizujte hodnoty AssetDeliveryConfiguration používat WidevineLicenseAcquisitionUrl (který má hodnotu 7) a zadejte adresu URL službu doručování licencí. Při doručování licencí Widevine můžete použít následující partneři AMS: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/), [castLabs](http://castlabs.com/company/partners/azure/).

Příklad: 

    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":2,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":7,\"Value\":\"https:\\/\\/example.net\/WidevineLicenseAcquisition\/"}]"}

> [!NOTE]
> Při šifrování pomocí Widevine, by pouze možné doručit pomocí DASH. Nezapomeňte zadat POMLČKU (2) v doručovací protokol assetu.
> 
> 

### <a name="link-asset-with-asset-delivery-policy"></a>Propojení prostředku s zásady doručení assetu
Zobrazit [propojení prostředku s zásady doručení assetu](#link_asset_with_asset_delivery_policy)

## <a id="types"></a>Typy používané při definování AssetDeliveryPolicy

### <a name="assetdeliveryprotocol"></a>AssetDeliveryProtocol

Následující výčet popisuje hodnoty, které můžete nastavit pro doručovací protokol assetu.

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

Následující výčet popisuje hodnoty, které lze zadat pro typ zásady doručení assetu.  

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

Následující výčet popisuje hodnoty, které vám umožní nakonfigurovat metodu doručení klíče k obsahu do klienta.
    
    public enum ContentKeyDeliveryType
    {
        /// <summary>
        /// None.
        ///
        </summary>
        None = 0,

        /// <summary>
        /// Use PlayReady License acquistion protocol
        ///
        </summary>
        PlayReadyLicense = 1,

        /// <summary>
        /// Use MPEG Baseline HTTP key protocol.
        ///
        </summary>
        BaselineHttp = 2,

        /// <summary>
        /// Use Widevine License acquistion protocol
        ///
        </summary>
        Widevine = 3

    }


### <a name="assetdeliverypolicyconfigurationkey"></a>AssetDeliveryPolicyConfigurationKey

Následující výčet popisuje hodnoty, které lze nastavit na konfiguraci klíče, použít k získání konkrétní konfigurace zásad doručení prostředku.

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

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

