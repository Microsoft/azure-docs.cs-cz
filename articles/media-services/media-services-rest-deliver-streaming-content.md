---
title: Publikovat obsah Azure Media Services pomocí REST
description: Naučte se vytvořit lokátor, který je použit k vytvoření adresy URL streamování. Kód používá rozhraní REST API.
author: Juliako
manager: cfowler
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: ff332c30-30c6-4ed1-99d0-5fffd25d4f23
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: juliako
ms.openlocfilehash: 8937be0e3f18d58d4cdd909de0ca2c455cc46c1f
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
ms.locfileid: "31791830"
---
# <a name="publish-azure-media-services-content-using-rest"></a>Publikovat obsah Azure Media Services pomocí REST
> [!div class="op_single_selector"]
> * [.NET](media-services-deliver-streaming-content.md)
> * [REST](media-services-rest-deliver-streaming-content.md)
> * [Azure Portal](media-services-portal-publish.md)
> 
> 

Můžete datového proudu s adaptivní přenosovou rychlostí sady souborů MP4 vytvořením Lokátor streamování OnDemand a vytvoření adresy URL streamování. [Kódování prostředek](media-services-rest-encode-asset.md) článek ukazuje, jak zakódovat do sady souborů MP4 adaptivní přenosovou rychlostí. Pokud váš obsah je zašifrován, konfigurace zásad doručení assetu (jak je popsáno v [to](media-services-rest-configure-asset-delivery-policy.md) článek) před vytvořením lokátoru. 

Lokátor streamování OnDemand. můžete také použít k vytvoření adresy URL, které odkazují na soubory MP4, které lze progresivně stáhnout.  

Tento článek ukazuje, jak vytvořit lokátor, aby bylo možné publikování asset a vytvoření Smooth, MPEG DASH a adresy URL streamování HLS streamování OnDemand. Také ukazuje aktivní pro vytvoření adres URL progresivního stahování.

[Následující](#types) část ukazuje typy výčtu, jejichž hodnoty se používají ve voláních REST.   

> [!NOTE]
> Při přístupu k entity ve službě Media Services, musíte nastavit specifická pole hlaviček a hodnoty ve své žádosti HTTP. Další informace najdete v tématu [instalační program pro Media Services REST API vývoj](media-services-rest-how-to-use.md).
> 

## <a name="connect-to-media-services"></a>Připojení ke službě Media Services

Informace o tom, jak připojit k rozhraní API pro AMS najdete v tématu [přístup k Azure Media Services API pomocí ověřování Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

>[!NOTE]
>Po úspěšném připojení k https://media.windows.net, zobrazí se 301 přesměrování zadání jiném identifikátoru URI Media Services. Je nutné provést následující volání nový identifikátor URI.

## <a name="create-an-ondemand-streaming-locator"></a>Vytvořit lokátor streamování OnDemand.
Pokud chcete vytvořit lokátor streamování OnDemand a získání adres URL, musíte udělat následující:

1. Pokud je obsah šifrovat, definujte zásady přístupu.
2. Vytvořte Lokátor streamování OnDemand.
3. Pokud máte v plánu k vysílání datového proudu, získáte streamování souboru manifestu (.ism) v prostředku. 
   
   Pokud budete chtít progresivně stahovat, získáte názvy soubory MP4 v prostředku. 
4. Sestavení adresy URL k souboru manifestu nebo soubory MP4. 
5. Nelze vytvořit lokátor streamování pomocí AccessPolicy, která zahrnuje zápisu nebo odstraňte oprávnění.

### <a name="create-an-access-policy"></a>Vytvoření zásad přístupu

>[!NOTE]
>Je stanovený limit 1 000 000 různých zásad AMS (třeba zásady lokátoru nebo ContentKeyAuthorizationPolicy). Stejné ID zásady použijte, pokud vždy používají stejné dny / přístupová oprávnění, například zásady pro lokátory, které jsou určeny k zůstat na místě po dlouhou dobu (bez odeslání zásady). Další informace najdete v [tomto](media-services-dotnet-manage-entities.md#limit-access-policies) článku.

Žádost:

    POST https://media.windows.net/api/AccessPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: 6bcfd511-a561-448d-a022-a319a89ecffa
    Host: media.windows.net
    Content-Length: 68

    {"Name":"access policy","DurationInMinutes":43200.0,"Permissions":1}

Odpověď:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 311
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https:/media.windows.net/api/AccessPolicies('nb%3Apid%3AUUID%3A69c80d98-7830-407f-a9af-e25f4b0d3e5f')
    Server: Microsoft-IIS/8.5
    request-id: a877528a-bdb4-4414-9862-273f8e64f882
    x-ms-request-id: a877528a-bdb4-4414-9862-273f8e64f882
    x-ms-client-request-id: 6bcfd511-a561-448d-a022-a319a89ecffa
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Wed, 18 Feb 2015 06:52:09 GMT

    {"odata.metadata":"https://media.windows.net/api/$metadata#AccessPolicies/@Element","Id":"nb:pid:UUID:69c80d98-7830-407f-a9af-e25f4b0d3e5f","Created":"2015-02-18T06:52:09.8862191Z","LastModified":"2015-02-18T06:52:09.8862191Z","Name":"access policy","DurationInMinutes":43200.0,"Permissions":1}

### <a name="create-an-ondemand-streaming-locator"></a>Vytvořit lokátor streamování OnDemand.
Vytvořte Lokátor pro daný prostředek a zásady assetu.

Žádost:

    POST https://media.windows.net/api/Locators HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: ac159492-9a0c-40c3-aacc-551b1b4c5f62
    Host: media.windows.net
    Content-Length: 181

    {"AccessPolicyId":"nb:pid:UUID:1480030d-c481-430a-9687-535c6a5cb272","AssetId":"nb:cid:UUID:cc1e445d-1500-80bd-538e-f1e4b71b465e","StartTime":"2015-02-18T06:34:47.267872Z","Type":2}

Odpověď:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 637
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://media.windows.net/api/Locators('nb%3Alid%3AUUID%3Abe245661-2bbd-4fc6-b14f-9cf9a1492e5e')
    Server: Microsoft-IIS/8.5
    request-id: 5bd5864a-0afd-44c0-a67a-4044a2c9043b
    x-ms-request-id: 5bd5864a-0afd-44c0-a67a-4044a2c9043b
    x-ms-client-request-id: ac159492-9a0c-40c3-aacc-551b1b4c5f62
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Wed, 18 Feb 2015 06:58:37 GMT

    {"odata.metadata":"https://media.windows.net/api/$metadata#Locators/@Element","Id":"nb:lid:UUID:be245661-2bbd-4fc6-b14f-9cf9a1492e5e","ExpirationDateTime":"2015-03-20T06:34:47.267872+00:00","Type":2,"Path":"http://amstest1.streaming.mediaservices.windows.net/be245661-2bbd-4fc6-b14f-9cf9a1492e5e/","BaseUri":"http://amstest1.streaming.mediaservices.windows.net","ContentAccessComponent":"be245661-2bbd-4fc6-b14f-9cf9a1492e5e","AccessPolicyId":"nb:pid:UUID:1480030d-c481-430a-9687-535c6a5cb272","AssetId":"nb:cid:UUID:cc1e445d-1500-80bd-538e-f1e4b71b465e","StartTime":"2015-02-18T06:34:47.267872+00:00","Name":null}

### <a name="build-streaming-urls"></a>Vytvoření adresy URL pro streamování
Použití **cesta** vrácena hodnota po vytvoření lokátoru vytvářet Smooth, HLS a MPEG DASH adresy URL. 

Technologie Smooth Streaming: **cesta** + název souboru manifestu + "/ manifest"

Příklad:

    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest

HLS: **cesta** + název souboru manifestu + "/ manifest(format=m3u8-aapl)"

Příklad:

    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=m3u8-aapl)


DASH: **cesta** + název souboru manifestu + "/ manifest(format=mpd-time-csf)"

Příklad:

    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=mpd-time-csf)


### <a name="build-progressive-download-urls"></a>Vytvoření adres URL progresivního stahování
Použití **cesta** vrácena hodnota po vytvoření lokátoru sestavit adresu URL progresivního stahování.   

Adresa URL: **cesta** + asset soubor mp4 název

Příklad:

    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

## <a id="types"></a>Typy výčtu
    [Flags]
    public enum AccessPermissions
    {
        None = 0,
        Read = 1,
        Write = 2,
        Delete = 4,
        List = 8,
    }

    public enum LocatorType
    {
        None = 0,
        Sas = 1,
        OnDemandOrigin = 2,
    }

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Další informace najdete v tématech
[Přehled rozhraní REST API operations Media Services](media-services-rest-how-to-use.md)

[Konfigurace zásad doručení assetu](media-services-rest-configure-asset-delivery-policy.md)

