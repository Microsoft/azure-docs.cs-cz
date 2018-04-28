---
title: Konfigurace zásad doručení assetu pomocí Media Services REST API | Microsoft Docs
description: Toto téma ukazuje, jak nakonfigurovat zásady doručení jiný prostředek pomocí Media Services REST API.
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
ms.openlocfilehash: 17846da7534856198e62ddeadf47180fe5b102f6
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="configuring-asset-delivery-policies"></a>Konfigurace zásad doručení assetu
[!INCLUDE [media-services-selector-asset-delivery-policy](../../includes/media-services-selector-asset-delivery-policy.md)]

Pokud máte v plánu pro doručování dynamicky šifrovaných prostředky, jeden z kroků v pracovním postupu doručování obsahu Media Services je konfigurace zásad doručení pro prostředky. Zásady doručení assetu informuje Media Services, jak chcete použít pro váš asset, který bude doručen: do které protokol pro streamování by měl váš asset dynamicky zabalené (pro příklad, MPEG DASH, HLS, technologie Smooth Streaming nebo všechny), zda chcete dynamicky šifrovat. váš asset a jak (obálky nebo common encryption).

Toto téma popisuje, proč a jak vytvořit a nakonfigurovat zásady doručení assetu.

>[!NOTE]
>Po vytvoření účtu AMS se do vašeho účtu přidá **výchozí** koncový bod streamování ve stavu **Zastaveno**. Pokud chcete spustit streamování vašeho obsahu a využít výhod dynamického balení a dynamického šifrování, musí koncový bod streamování, ze kterého chcete streamovat obsah, být ve stavu **Spuštěno**. 
>
>Abyste mohli používat dynamické balením a dynamickým šifrováním také váš asset musí obsahovat sadu s adaptivní přenosovou rychlostí soubory MP4 s rychlostmi nebo soubory technologie Smooth Streaming s adaptivní přenosovou rychlostí.

Různé zásady je možné aplikovat na stejný asset. Můžete například použít šifrování PlayReady na technologie Smooth Streaming a pomocí standardu AES Envelope šifrování a MPEG DASH, HLS. Veškeré protokoly, které nejsou v zásadách doručení definovány (například když přidáte jedinou zásadu, která jako protokol určuje pouze HLS), budou při streamování blokovány. Výjimkou je, pokud nemáte definovány vůbec žádné zásady doručení assetu. Pak budou všechny protokoly povolené v nešifrované podobě.

Pokud chcete doručovat šifrované asset úložiště, musíte nakonfigurovat zásady doručení assetu. Před asset Streamovat, server datových proudů odebere šifrování úložiště a datové proudy svůj obsah pomocí zadaného doručování zásad. Například k poskytování asset šifrován Advanced Encryption (Standard AES) obálky šifrovací klíč, nastavte typ zásad na **DynamicEnvelopeEncryption**. Pokud chcete odebrat šifrování úložiště a Streamovat prostředek v nešifrované podobě, nastavte typ zásad na **NoDynamicEncryption**. Postupujte podle příklady, které ukazují, jak konfigurovat tyto typy zásad.

V závislosti na tom, jak nakonfigurovat zásady doručení assetu by nebudete moct dynamicky balíčku, dynamicky šifrovat a stream u následujících protokolů streamování: technologie Smooth Streaming, HLS, datové proudy MPEG DASH.

V následujícím seznamu jsou formáty, který používáte pro datový proud Smooth, HLS, DASH.

Technologie Smooth Streaming:

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

HLS:

{streamování koncový bod služby media název účtu name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG DASH

{streamování koncový bod služby media název účtu name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)


Pokyny k publikování assetu a vytvoření adresy URL streamování najdete v článku o [vytvoření adresy URL streamování](media-services-deliver-streaming-content.md).

## <a name="considerations"></a>Požadavky
* Nelze odstranit AssetDeliveryPolicy přidružený prostředek při Lokátor OnDemand (streaming) existuje pro tento prostředek. Doporučuje se před odstraněním zásady odeberte zásady z prostředku.
* Šifrované majetku úložiště nelze vytvořit lokátor streamování, nastavena žádné zásady doručení assetu.  Není-li Asset šifrování úložiště, systém vám umožní vytvořit Lokátor a Streamovat prostředek v nešifrované podobě bez zásady pro doručení assetu.
* Můžete mít více zásady doručení mediálního přidružené jednoho datového zdroje, ale můžete určit pouze jeden způsob, jak zpracovávat dané AssetDeliveryProtocol.  Znamená, pokud se pokusíte propojit dvě zásady doručení, které zadat AssetDeliveryProtocol.SmoothStreaming protokol, který bude výsledkem chyba, protože systém nebude vědět, který jeden se má použít, když klient odešle požadavek technologie Smooth Streaming.
* Pokud máte prostředek s stávající Lokátor streamování, nelze propojit nové zásady pro daný prostředek, odpojit existující zásady z prostředku nebo aktualizujete zásady pro doručení přidružený asset.  Nejdřív musíte odstraňte Lokátor streamování, upravit zásady a potom je znovu vytvořit lokátor streamování.  Stejné locatorId můžete použít, když je znovu vytvořit lokátor streamování, ale měli byste zajistit, že vzhledem k tomu, že do mezipaměti obsah počátek nebo podřízené CDN, který nebude způsobovat problémy pro klienty.

>[!NOTE]

>Při přístupu k entity ve službě Media Services, musíte nastavit specifická pole hlaviček a hodnoty ve své žádosti HTTP. Další informace najdete v tématu [instalační program pro Media Services REST API vývoj](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Připojení ke službě Media Services

Informace o tom, jak připojit k rozhraní API pro AMS najdete v tématu [přístup k Azure Media Services API pomocí ověřování Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="clear-asset-delivery-policy"></a>Zásady doručení assetu vymazat
### <a id="create_asset_delivery_policy"></a>Vytvoření zásady doručení assetu
Následující požadavek HTTP vytvoří zásady doručení assetu, který určuje nelze použít dynamické šifrování a poskytovat datový proud v některém z těchto protokolů: protokoly MPEG DASH, HLS nebo technologie Smooth Streaming. 

Informace na hodnotách, které můžete zadat při vytváření AssetDeliveryPolicy najdete v tématu [typy používané při definování AssetDeliveryPolicy](#types) části.   

Žádost:

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

### <a id="link_asset_with_asset_delivery_policy"></a>Odkaz asset zásady doručení assetu
Následující požadavek HTTP odkazuje na zásady doručení assetu pro daný prostředek.

Žádost:

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


## <a name="dynamicenvelopeencryption-asset-delivery-policy"></a>Zásady doručení assetu DynamicEnvelopeEncryption
### <a name="create-content-key-of-the-envelopeencryption-type-and-link-it-to-the-asset"></a>Vytvořte klíč obsahu typu EnvelopeEncryption a tu propojit na prostředku
Při zadávání zásad doručení DynamicEnvelopeEncryption, budete muset nezapomeňte propojit klíč obsahu typu EnvelopeEncryption asset. Další informace najdete v tématu: [vytváření klíč obsahu](media-services-rest-create-contentkey.md)).

### <a id="get_delivery_url"></a>Získat adresu URL pro doručení
Získáte adresu URL doručení pro metodu zadaný doručení obsahu klíče vytvořeného v předchozím kroku. Klient používá vrácené adresu URL k vyžádání AES klíč nebo licence PlayReady, aby přehrávání chráněného obsahu.

Zadejte typ adresy URL získat v textu požadavku HTTP. Pokud chráníte svůj obsah pomocí technologie PlayReady, žádosti o adresu URL získání licence Media Services PlayReady pomocí 1 pro keyDeliveryType: {"keyDeliveryType": 1}. Pokud chráníte svůj obsah pomocí šifrování obálky, požadavků adresu URL pro získání klíče zadáním 2 pro keyDeliveryType: {"keyDeliveryType": 2}.

Žádost:

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
Vytvoří následující požadavek HTTP **AssetDeliveryPolicy** nakonfigurovaný pro použití dynamické obálky šifrování (**DynamicEnvelopeEncryption**) k **HLS** protokol (v tomto příkladu jiné protokoly budou při streamování blokovány). 

Informace na hodnotách, které můžete zadat při vytváření AssetDeliveryPolicy najdete v tématu [typy používané při definování AssetDeliveryPolicy](#types) části.   

Žádost:

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


### <a name="link-asset-with-asset-delivery-policy"></a>Odkaz asset zásady doručení assetu
V tématu [odkaz asset zásady doručení assetu](#link_asset_with_asset_delivery_policy)

## <a name="dynamiccommonencryption-asset-delivery-policy"></a>Zásady doručení assetu DynamicCommonEncryption
### <a name="create-content-key-of-the-commonencryption-type-and-link-it-to-the-asset"></a>Vytvořte klíč obsahu typu CommonEncryption a tu propojit na prostředku
Při zadávání zásad doručení DynamicCommonEncryption, budete muset nezapomeňte propojit klíč obsahu typu CommonEncryption asset. Další informace najdete v tématu: [vytváření klíč obsahu](media-services-rest-create-contentkey.md)).

### <a name="get-delivery-url"></a>Získat adresu URL pro doručení
Získáte adresu URL doručení pro metodu doručení PlayReady obsahu klíče vytvořeného v předchozím kroku. Klient používá vrácené adresu URL k vyžádání licence PlayReady, aby přehrávání chráněného obsahu. Další informace najdete v tématu [získat adresu URL doručení](#get_delivery_url).

### <a name="create-asset-delivery-policy"></a>Vytvoření zásady doručení assetu
Vytvoří následující požadavek HTTP **AssetDeliveryPolicy** nakonfigurovaný pro použití běžného dynamického šifrování (**DynamicCommonEncryption**) do **technologie Smooth Streaming**protokolu (v tomto příkladu jiné protokoly budou při streamování blokovány). 

Informace na hodnotách, které můžete zadat při vytváření AssetDeliveryPolicy najdete v tématu [typy používané při definování AssetDeliveryPolicy](#types) části.   

Žádost:

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


Pokud chcete chránit obsah pomocí Widevine DRM, aktualizujte hodnoty AssetDeliveryConfiguration používat WidevineLicenseAcquisitionUrl (což je hodnota 7) a zadejte adresu URL služby doručování licencí. Můžete použít následující partneři AMS doručit licence na Widevine: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/), [castLabs](http://castlabs.com/company/partners/azure/).

Příklad: 

    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":2,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":7,\"Value\":\"https:\\/\\/example.net\/WidevineLicenseAcquisition\/"}]"}

> [!NOTE]
> Při šifrování s technologií Widevine, by pouze možné doručíte pomocí čárka. Nezapomeňte zadat DASH (2) v doručovací protokol assetu.
> 
> 

### <a name="link-asset-with-asset-delivery-policy"></a>Odkaz asset zásady doručení assetu
V tématu [odkaz asset zásady doručení assetu](#link_asset_with_asset_delivery_policy)

## <a id="types"></a>Typy používané při definování AssetDeliveryPolicy

### <a name="assetdeliveryprotocol"></a>AssetDeliveryProtocol

Následující výčet popisuje hodnoty, které lze nastavit pro doručovací protokol assetu.

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

Následující výčet popisuje hodnoty, které můžete zadat pro typ zásad doručení assetu.  

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

Následující výčet popisuje hodnoty, které můžete použít ke konfiguraci metodu doručení obsahu klíče, který se klient.
    
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

Následující výčet popisuje hodnoty, které můžete nastavit, aby konfigurace klíče, které slouží k získání konkrétní konfigurace pro zásady pro doručení assetu.

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
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

