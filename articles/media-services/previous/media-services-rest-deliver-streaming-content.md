---
title: Publikování obsahu Azure Media Services pomocí REST
description: Naučte se, jak vytvořit lokátor, který se používá k vytvoření adresy URL streamování. Kód používá REST API.
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
ms.openlocfilehash: 438a816ca28b9414557e64591d9ece2dbb173370
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89256867"
---
# <a name="publish-azure-media-services-content-using-rest"></a>Publikování obsahu Azure Media Services pomocí REST

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-deliver-streaming-content.md)
> * [REST](media-services-rest-deliver-streaming-content.md)
> * [Azure Portal](media-services-portal-publish.md)
> 
> 

Můžete vytvořit datový proud sady MP4 s adaptivní přenosovou rychlostí vytvořením lokátoru pro streamování OnDemand a vytvořením adresy URL streamování. Článek [kódování prostředku](media-services-rest-encode-asset.md) ukazuje, jak se zakódovat do sady souborů MP4 s adaptivní přenosovou rychlostí. Pokud je váš obsah zašifrovaný, nakonfigurujte zásady doručení assetu (jak je popsáno v [tomto](media-services-rest-configure-asset-delivery-policy.md) článku) ještě před vytvořením lokátoru. 

Lokátor streamování OnDemand můžete použít také k sestavení adres URL, které odkazují na soubory MP4, které se dají postupně stahovat.  

V tomto článku se dozvíte, jak vytvořit Lokátor streamování OnDemand, abyste mohli publikovat svůj Asset a vytvářet hladké adresy URL a HLS streamování MPEG. Také ukazuje, jak vytvářet progresivní adresy URL pro stahování.

V [následující](#types) části jsou uvedeny typy výčtu, jejichž hodnoty jsou použity v voláních REST.   

> [!NOTE]
> Při přístupu k entitám v Media Services musíte nastavit konkrétní pole a hodnoty hlaviček v požadavcích HTTP. Další informace najdete v tématu [instalace Media Services REST APIm vývoji](media-services-rest-how-to-use.md).
> 

## <a name="connect-to-media-services"></a>Připojení ke službě Media Services

Informace o tom, jak se připojit k rozhraní API AMS, najdete v tématu [přístup k rozhraní Azure Media Services API pomocí ověřování Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

>[!NOTE]
>Po úspěšném připojení k vám https://media.windows.net obdržíte přesměrování 301 s určením jiného identifikátoru URI Media Services. Je nutné provést následná volání nového identifikátoru URI.

## <a name="create-an-ondemand-streaming-locator"></a>Vytvoření lokátoru pro streamování OnDemand
Chcete-li vytvořit Lokátor streamování OnDemand a získat adresy URL, je nutné provést následující akce:

1. Pokud je obsah zašifrovaný, definujte zásady přístupu.
2. Vytvořte Lokátor streamování OnDemand.
3. Pokud máte v úmyslu streamovat, Získejte v assetu soubor manifestu pro streamování (. ISM). 
   
   Pokud se chystáte se postupně stahovat, Získejte názvy souborů MP4 v assetu. 
4. Vytvoří adresy URL pro soubor manifestu nebo soubory MP4. 
5. Nemůžete vytvořit Lokátor streamování pomocí AccessPolicy, který zahrnuje oprávnění zapisovat nebo odstranit.

### <a name="create-an-access-policy"></a>Vytvoření zásady přístupu

>[!NOTE]
>Je stanovený limit 1 000 000 různých zásad AMS (třeba zásady lokátoru nebo ContentKeyAuthorizationPolicy). Použijte stejné ID zásad, pokud vždycky používáte stejné dny nebo přístupová oprávnění, například zásady pro Lokátory, které mají zůstat v platnosti po dlouhou dobu (zásady bez nahrávání). Další informace najdete v [tomto](media-services-dotnet-manage-entities.md#limit-access-policies) článku.

Požadavek:

```console
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
```

Odpověď:

```console
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
```

### <a name="create-an-ondemand-streaming-locator"></a>Vytvoření lokátoru pro streamování OnDemand
Vytvoří Lokátor pro zadané zásady assetu a assetu.

Požadavek:

```console
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
```
Odpověď:

```console
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
```

### <a name="build-streaming-urls"></a>Vytváření adres URL streamování
Použijte hodnotu **cesty** vrácenou po vytvoření lokátoru a vytvořte tak vyhlazené adresy URL, HLS a MPEG pomlčky. 

Smooth Streaming: **cesta** + název souboru manifestu + "/manifest"

Příklad:

`https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest`

HLS: **cesta** + název souboru manifestu + "/manifest (Format = M3U8-AAPL)"

Příklad:

`https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=m3u8-aapl)`


POMLČKa: **cesta** + název souboru manifestu + "/manifest (Format = MPD-Time-CSF)"

Příklad:

`https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=mpd-time-csf)`


### <a name="build-progressive-download-urls"></a>Sestavit progresivní adresy URL pro stahování
Pro sestavení adresy URL postupného stahování použijte hodnotu **cesty** vrácenou po vytvoření lokátoru.   

Adresa URL: **cesta** + název souboru MP4 prostředku

Příklad:

`https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4`

## <a name="enum-types"></a><a id="types"></a>Výčtové typy

```console
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
```

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Viz také
[Přehled Media Servicesch operací REST API](media-services-rest-how-to-use.md)

[Konfigurace zásad doručení assetu](media-services-rest-configure-asset-delivery-policy.md)

