---
title: Konfigurace zásad doručení assetu pomocí Media Services REST API | Microsoft Docs
description: V tomto tématu se dozvíte, jak nakonfigurovat různé zásady doručování assetů pomocí Media Services REST API.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: cfowler
editor: ''
ms.assetid: 5cb9d32a-e68b-4585-aa82-58dded0691d0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 65a7b9479dae1826f478ca5e038ef5feb1caefb6
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "103017320"
---
# <a name="configuring-asset-delivery-policies"></a>Konfigurace zásad doručení assetu

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

[!INCLUDE [media-services-selector-asset-delivery-policy](../../../includes/media-services-selector-asset-delivery-policy.md)]

Pokud plánujete doručování dynamicky šifrovaných prostředků, jeden z kroků v pracovním postupu pro doručování obsahu Media Services konfiguruje zásady doručování pro prostředky. Zásada doručení assetu oznamuje Media Services, jak chcete, aby se Asset doručil: do kterého by měl být váš Asset dynamicky zabalen (například MPEG POMLČKa, HLS, Smooth Streaming nebo vše), ať už chcete, nebo ne, chcete-li prostředek dynamicky zašifrovat a jak (obálky nebo běžné šifrování).

Toto téma popisuje, proč a jak vytvořit a nakonfigurovat zásady doručení assetu.

> [!NOTE]
> Po vytvoření účtu AMS se do vašeho účtu přidá **výchozí** koncový bod streamování ve stavu **Zastaveno**. Pokud chcete spustit streamování vašeho obsahu a využít výhod dynamického balení a dynamického šifrování, musí koncový bod streamování, ze kterého chcete streamovat obsah, být ve stavu **Spuštěno**. 
>
> Aby bylo možné použít dynamické balení a dynamické šifrování, vaše Asset musí obsahovat sadu adaptivních přenosů rychlostmi nebo adaptivní přenosové rychlosti Smooth Streaming souborů.

U stejného prostředku můžete použít jiné zásady. Můžete například použít šifrování PlayReady pro Smooth Streaming a šifrování obálek AES na MPEG POMLČKy a HLS. Veškeré protokoly, které nejsou v zásadách doručení definovány (například když přidáte jedinou zásadu, která jako protokol určuje pouze HLS), budou při streamování blokovány. Výjimkou je, pokud nemáte definovány vůbec žádné zásady doručení assetu. Pak budou všechny protokoly povolené v nešifrované podobě.

Pokud chcete doručovat šifrovaný prostředek úložiště, musíte nakonfigurovat zásady doručování prostředků. Předtím, než bude možné Asset streamovat, server streamování odstraní šifrování úložiště a streamuje obsah pomocí zadaných zásad doručování. Pokud třeba chcete zajistit, aby se Asset zašifroval pomocí šifrovacího klíče standard AES (Advanced Encryption Standard) (AES), nastavte typ zásady na **DynamicEnvelopeEncryption**. Pokud chcete odstranit šifrování úložiště a streamovat prostředek v nejasném případě, nastavte typ zásady na **NoDynamicEncryption**. Příklady, které ukazují, jak nakonfigurovat tyto typy zásad, následují.

V závislosti na tom, jak nakonfigurujete zásady doručení assetu, byste mohli dynamicky zabalit, dynamicky šifrovat a streamovat následující protokoly streamování: Smooth Streaming, HLS a MPEG SPOJOVNÍKy.

Následující seznam obsahuje formáty, které používáte ke streamování hladce, HLS, POMLČKy.

Smooth Streaming:

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

HLS

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG DASH

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)


Pokyny k publikování assetu a vytvoření adresy URL streamování najdete v článku o [vytvoření adresy URL streamování](media-services-deliver-streaming-content.md).

## <a name="considerations"></a>Požadavky
* Nelze odstranit AssetDeliveryPolicy přidružené k assetu, zatímco pro daný prostředek existuje Lokátor OnDemand (streaming). Doporučujeme odstranit zásadu z assetu předtím, než zásadu odstraníte.
* Lokátor streamování nejde vytvořit na prostředku šifrovaném úložiště, pokud není nastavená žádná zásada pro doručení prostředků.  Pokud prostředek není šifrovaný jako zašifrovaný, systém vám umožní vytvořit Lokátor a streamovat prostředek ve formě bez zásad doručení assetu.
* K jednomu prostředku můžete mít k dispozici více zásad doručení prostředků, ale můžete zadat jenom jeden způsob, jak zpracovat daný AssetDeliveryProtocol.  To znamená, že se pokusíte propojit dvě zásady doručování, které určují protokol AssetDeliveryProtocol. SmoothStreaming, který bude mít za následek chybu, protože systém neví, který z nich chcete použít, když klient provede požadavek Smooth Streaming.
* Pokud máte Asset s existujícím lokátorem služby streamování, nemůžete propojit nové zásady s Assetem, odkázat stávající zásady z assetu nebo aktualizovat zásady doručování přidružené k assetu.  Nejdřív musíte odebrat Lokátor streamování, upravit zásady a pak znovu vytvořit Lokátor streamování.  Stejné locatorId můžete použít při opětovném vytvoření lokátoru streamování, ale měli byste zajistit, aby nedošlo k potížím pro klienty, protože obsah může být uložený v mezipaměti od původu nebo přes CDN pro příjem dat.

> [!NOTE]
> 
> Při přístupu k entitám v Media Services musíte nastavit konkrétní pole a hodnoty hlaviček v požadavcích HTTP. Další informace najdete v tématu [instalace Media Services REST APIm vývoji](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Připojení ke službě Media Services

Informace o tom, jak se připojit k rozhraní API AMS, najdete v tématu [přístup k rozhraní Azure Media Services API pomocí ověřování Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="clear-asset-delivery-policy"></a>Vymazat zásady doručení assetu
### <a name="create-asset-delivery-policy"></a><a id="create_asset_delivery_policy"></a>Vytvořit zásady doručení assetu
Následující požadavek HTTP vytvoří zásadu doručení assetu, která určuje, že se nemá používat dynamické šifrování, a pokud chcete datový proud doručit v některém z následujících protokolů: MPEG POMLČKy, HLS a Smooth Streaming protokoly. 

Informace o tom, jaké hodnoty můžete zadat při vytváření AssetDeliveryPolicy, najdete v tématu [typy používané při definování oddílu AssetDeliveryPolicy](#types) .   

Požadavek:

```console
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
```

Odpověď:

```output
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
```

### <a name="link-asset-with-asset-delivery-policy"></a><a id="link_asset_with_asset_delivery_policy"></a>Propojit prostředek se zásadami doručení assetu
Následující požadavek HTTP propojí zadaný prostředek k zásadě doručení assetu.

Požadavek:

```console
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
```

Odpověď:

```output
HTTP/1.1 204 No Content
```


## <a name="dynamicenvelopeencryption-asset-delivery-policy"></a>Zásady doručování prostředků DynamicEnvelopeEncryption
### <a name="create-content-key-of-the-envelopeencryption-type-and-link-it-to-the-asset"></a>Vytvořit klíč obsahu pro typ EnvelopeEncryption a propojit ho s Assetem
Když zadáváte zásady doručování DynamicEnvelopeEncryption, musíte se ujistit, že propojíte svůj Asset s klíčem obsahu typu EnvelopeEncryption. Další informace najdete v tématu: [vytvoření klíče obsahu](media-services-rest-create-contentkey.md).

### <a name="get-delivery-url"></a><a id="get_delivery_url"></a>Získat adresu URL doručení
Získejte adresu URL doručení pro zadanou metodu doručení klíče obsahu vytvořeného v předchozím kroku. Klient použije vrácenou adresu URL k vyžádání klíče AES nebo licence PlayReady, aby mohl přehrát chráněný obsah.

Zadejte typ adresy URL, která má být získána v těle požadavku HTTP. Pokud chráníte obsah pomocí PlayReady, požádejte o Media Services adresu URL pro získání licence PlayReady, a to pomocí 1 pro keyDeliveryType: {"keyDeliveryType": 1}. Pokud chráníte obsah pomocí šifrování obálky, požádejte o adresu URL pro získání klíče zadáním 2 pro keyDeliveryType: {"keyDeliveryType": 2}.

Požadavek:

```console
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
```

Odpověď:

```output
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
```


### <a name="create-asset-delivery-policy"></a>Vytvořit zásady doručení assetu
Následující požadavek HTTP vytvoří **AssetDeliveryPolicy** , který je nakonfigurován na použití dynamického šifrování obálky (**DynamicEnvelopeEncryption**), do protokolu **HLS** (v tomto příkladu budou z datového proudu blokovány další protokoly). 

Informace o tom, jaké hodnoty můžete zadat při vytváření AssetDeliveryPolicy, najdete v tématu [typy používané při definování oddílu AssetDeliveryPolicy](#types) .   

Požadavek:

```console
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
```


Odpověď:

```output
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
```


### <a name="link-asset-with-asset-delivery-policy"></a>Propojit prostředek se zásadami doručení assetu
Podívejte [se na téma propojení assetu se zásadami doručení assetu](#link_asset_with_asset_delivery_policy)

## <a name="dynamiccommonencryption-asset-delivery-policy"></a>Zásady doručování prostředků DynamicCommonEncryption
### <a name="create-content-key-of-the-commonencryption-type-and-link-it-to-the-asset"></a>Vytvořit klíč obsahu pro typ CommonEncryption a propojit ho s Assetem
Když zadáváte zásady doručování DynamicCommonEncryption, musíte se ujistit, že propojíte svůj Asset s klíčem obsahu typu CommonEncryption. Další informace najdete v tématu: [vytvoření klíče obsahu](media-services-rest-create-contentkey.md).

### <a name="get-delivery-url"></a>Získat adresu URL doručení
Získejte adresu URL doručení pro metodu doručení PlayReady klíče obsahu vytvořeného v předchozím kroku. Klient použije vrácenou adresu URL k vyžádání licence PlayReady za účelem přehrání chráněného obsahu. Další informace najdete v tématu [získání adresy URL pro doručení](#get_delivery_url).

### <a name="create-asset-delivery-policy"></a>Vytvořit zásady doručení assetu
Následující požadavek HTTP vytvoří **AssetDeliveryPolicy** , který je nakonfigurovaný na použití dynamického běžného šifrování (**DynamicCommonEncryption**) na protokol **Smooth Streaming** (v tomto příkladu se zablokují i jiné protokoly). 

Informace o tom, jaké hodnoty můžete zadat při vytváření AssetDeliveryPolicy, najdete v tématu [typy používané při definování oddílu AssetDeliveryPolicy](#types) .   

Požadavek:

```console
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
```


Pokud chcete chránit svůj obsah pomocí Widevine DRM, aktualizujte hodnoty AssetDeliveryConfiguration na použití WidevineLicenseAcquisitionUrl (která má hodnotu 7) a zadejte adresu URL služby doručování licencí. Následující partneři AMS vám pomůžou s poskytováním licencí Widevine: [Axinom](https://www.axinom.com), [EZDRM](https://ezdrm.com/), [castLabs](https://castlabs.com/company/partners/azure/).

Například: 

```console
{"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":2,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":7,\"Value\":\"https:\\/\\/example.net\/WidevineLicenseAcquisition\/"}]"}
```

> [!NOTE]
> Při šifrování pomocí Widevine byste mohli doručovat jenom POMLČKy. Ujistěte se, že v protokolu pro doručení assetu zadáte POMLČKu (2).
> 
> 

### <a name="link-asset-with-asset-delivery-policy"></a>Propojit prostředek se zásadami doručení assetu
Podívejte [se na téma propojení assetu se zásadami doručení assetu](#link_asset_with_asset_delivery_policy)

## <a name="types-used-when-defining-assetdeliverypolicy"></a><a id="types"></a>Typy používané při definování AssetDeliveryPolicy

### <a name="assetdeliveryprotocol"></a>AssetDeliveryProtocol

Následující výčet popisuje hodnoty, které můžete nastavit pro protokol doručení assetu.

```csharp
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
```

### <a name="assetdeliverypolicytype"></a>AssetDeliveryPolicyType

Následující výčet popisuje hodnoty, které můžete nastavit pro typ zásad doručení assetu.  

```csharp
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
```

### <a name="contentkeydeliverytype"></a>ContentKeyDeliveryType

Následující výčet popisuje hodnoty, které můžete použít ke konfiguraci metody doručení klíče obsahu klientovi.
    
```csharp
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
```


### <a name="assetdeliverypolicyconfigurationkey"></a>AssetDeliveryPolicyConfigurationKey

Následující výčet popisuje hodnoty, které můžete nastavit ke konfiguraci klíčů používaných k získání konkrétní konfigurace pro zásady doručení assetu.

```csharp
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
```

## <a name="additional-notes"></a>Další poznámky

* Widevine je služba od společnosti Google Inc. v souladu s podmínkami služby a zásadami ochrany osobních údajů Google, Inc.

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

