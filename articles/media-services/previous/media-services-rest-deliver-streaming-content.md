---
title: Publikování obsahu Azure Media Services pomocí rest
description: Přečtěte si, jak vytvořit lokátor, který se používá k vytvoření adresy URL streamování. Kód používá rozhraní REST API.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: ff332c30-30c6-4ed1-99d0-5fffd25d4f23
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 787336f00a83d9403e3069754787743b9be6c5b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77050005"
---
# <a name="publish-azure-media-services-content-using-rest"></a>Publikování obsahu Azure Media Services pomocí rest 
> [!div class="op_single_selector"]
> * [.NET](media-services-deliver-streaming-content.md)
> * [Odpočinku](media-services-rest-deliver-streaming-content.md)
> * [Portál](media-services-portal-publish.md)
> 
> 

Sadu MP4 s adaptivní přenosovou rychlostí můžete streamovat vytvořením lokátoru streamování OnDemand a vytvořením adresy URL streamování. Kódování [encoding an asset](media-services-rest-encode-asset.md) asset článek ukazuje, jak kódovat do sady MP4 s adaptivní datový majek. Pokud je váš obsah zašifrován, před vytvořením lokátoru nakonfigurujte zásady doručování datových zdrojů (jak je popsáno v [tomto](media-services-rest-configure-asset-delivery-policy.md) článku). 

Můžete také použít ondemand streaming lokátor k vytvoření adresy URL, které odkazují na SOUBORY MP4, které lze postupně stáhnout.  

Tento článek ukazuje, jak vytvořit lokátor streamování OnDemand, aby bylo možné publikovat datový zdroj a vytvořit adresy URL streamování Plynulý, MPEG DASH a HLS. Ukazuje také, jak vytvořit progresivní stahování adres URL.

[V následující](#types) části jsou uvedeny typy výčtu, jejichž hodnoty se používají ve volání REST.   

> [!NOTE]
> Při přístupu k entitám ve službě Media Services je nutné nastavit konkrétní pole záhlaví a hodnoty v požadavcích HTTP. Další informace naleznete [v tématu Setup for Media Services REST API Development](media-services-rest-how-to-use.md).
> 

## <a name="connect-to-media-services"></a>Připojení ke službě Media Services

Informace o tom, jak se připojit k rozhraní AMS API, najdete [v tématu Přístup k rozhraní API Azure Media Services pomocí ověřování Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

>[!NOTE]
>Po úspěšném připojení https://media.windows.netk programu obdržíte přesměrování 301 určující jiný identifikátor URI služby Media Services. Je nutné provést následná volání nového identifikátoru URI.

## <a name="create-an-ondemand-streaming-locator"></a>Vytvoření lokátoru streamování OnDemand
Chcete-li vytvořit lokátor streamování OnDemand a získat adresy URL, musíte provést následující kroky:

1. Pokud je obsah zašifrován, definujte zásady přístupu.
2. Vytvořte lokátor streamování OnDemand.
3. Pokud máte v plánu streamovat, získejte soubor manifestu streamování (.ism) do datového zdroje. 
   
   Pokud plánujete postupné stahování, získejte názvy souborů MP4 v datovém zdroji. 
4. Vytvořte adresy URL do souboru manifestu nebo souborů MP4. 
5. Lokátor streamování nelze vytvořit pomocí zásad accesspolicy, který zahrnuje oprávnění k zápisu nebo odstranění.

### <a name="create-an-access-policy"></a>Vytvoření zásad přístupu

>[!NOTE]
>Je stanovený limit 1 000 000 různých zásad AMS (třeba zásady lokátoru nebo ContentKeyAuthorizationPolicy). Stejné ID zásad použijte, pokud vždy používáte stejné dny / přístupová oprávnění, například zásady pro lokátory, které mají zůstat na místě po dlouhou dobu (zásady bez nahrávání). Další informace naleznete v [tomto](media-services-dotnet-manage-entities.md#limit-access-policies) článku.

Požadavek:

    POST https://media.windows.net/api/AccessPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19
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

### <a name="create-an-ondemand-streaming-locator"></a>Vytvoření lokátoru streamování OnDemand
Vytvořte lokátor pro zadaný majetek a zásady majetku.

Požadavek:

    POST https://media.windows.net/api/Locators HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19
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

    {"odata.metadata":"https://media.windows.net/api/$metadata#Locators/@Element","Id":"nb:lid:UUID:be245661-2bbd-4fc6-b14f-9cf9a1492e5e","ExpirationDateTime":"2015-03-20T06:34:47.267872+00:00","Type":2,"Path":"https://amstest1.streaming.mediaservices.windows.net/be245661-2bbd-4fc6-b14f-9cf9a1492e5e/","BaseUri":"https://amstest1.streaming.mediaservices.windows.net","ContentAccessComponent":"be245661-2bbd-4fc6-b14f-9cf9a1492e5e","AccessPolicyId":"nb:pid:UUID:1480030d-c481-430a-9687-535c6a5cb272","AssetId":"nb:cid:UUID:cc1e445d-1500-80bd-538e-f1e4b71b465e","StartTime":"2015-02-18T06:34:47.267872+00:00","Name":null}

### <a name="build-streaming-urls"></a>Vytváření adres URL datových proudů
Pomocí hodnoty **Cesta** vrácené po vytvoření lokátoru vytvořte adresy URL Vyhlazení, HLS a MPEG DASH. 

Plynulé streamování: **Cesta** + název souboru manifestu + "/manifest"

Příklad:

    https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest

HLS: **Path** + název souboru manifestu + "/manifest(format=m3u8-aapl)"

Příklad:

    https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=m3u8-aapl)


DASH: **Path** + název souboru manifestu + "/manifest(format=mpd-time-csf)"

Příklad:

    https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=mpd-time-csf)


### <a name="build-progressive-download-urls"></a>Vytváření progresivních adres URL stahování
Pomocí hodnoty **Cesta** vrácené po vytvoření lokátoru vytvořte adresu URL postupného stahování.   

ADRESA URL: Název mp4 souboru prostředků **Cesta** + majetek

Příklad:

    https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

## <a name="enum-types"></a><a id="types"></a>Výčet typy
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

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Viz také
[Media Services operace REST API přehled](media-services-rest-how-to-use.md)

[Konfigurace zásad pro doručování majetku](media-services-rest-configure-asset-delivery-policy.md)

