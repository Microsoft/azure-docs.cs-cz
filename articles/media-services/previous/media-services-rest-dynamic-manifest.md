---
title: Vytváření filtrů pomocí Azure Media Services REST API | Microsoft Docs
description: V tomto tématu se dozvíte, jak vytvořit filtry, aby je klient mohl používat ke streamování konkrétních oddílů datového proudu. Media Services REST API vytvoří dynamické manifesty pro dosažení tohoto selektivního streamování.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: f7d23daf-7cd2-49c7-a195-ab902912ab3c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.reviewr: cenkdin
ms.openlocfilehash: b9286260d0fead11c7f55eff213c8ad0241a8cba
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "103008786"
---
# <a name="creating-filters-with-azure-media-services-rest-api"></a>Vytváření filtrů pomocí Azure Media Services REST API

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-dynamic-manifest.md)
> * [REST](media-services-rest-dynamic-manifest.md)
> 
> 

Počínaje verzí 2,17 Media Services umožňuje definovat filtry pro vaše prostředky. Tyto filtry jsou pravidla na straně serveru, která zákazníkům umožňují vybrat, co dělají: přehrávání pouze části videa (místo přehrávání celého videa) nebo určení pouze podmnožiny zvukových a video verzí, které může zařízení zákazníka zpracovat (místo všech verzí přidružených k assetu). Toto filtrování prostředků je archivováno prostřednictvím **dynamického manifestu** s, které jsou vytvořeny na základě požadavku zákazníka o streamování videa na základě zadaného filtru (ů).

Podrobnější informace týkající se filtrů a dynamického manifestu naleznete v tématu [Přehled dynamických manifestů](media-services-dynamic-manifest-overview.md).

Tento článek popisuje, jak pomocí rozhraní REST API vytvářet, aktualizovat a odstraňovat filtry. 

## <a name="types-used-to-create-filters"></a>Typy použité k vytváření filtrů
Při vytváření filtrů se používají tyto typy:  

* [Filtr](/rest/api/media/operations/filter)
* [AssetFilter](/rest/api/media/operations/assetfilter)
* [PresentationTimeRange](/rest/api/media/operations/presentationtimerange)
* [FilterTrackSelect a FilterTrackPropertyCondition](/rest/api/media/operations/filtertrackselect)

> [!NOTE]
> 
> Při přístupu k entitám v Media Services musíte nastavit konkrétní pole a hodnoty hlaviček v požadavcích HTTP. Další informace najdete v tématu [instalace Media Services REST APIm vývoji](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Připojení ke službě Media Services

Informace o tom, jak se připojit k rozhraní API AMS, najdete v tématu [přístup k rozhraní Azure Media Services API pomocí ověřování Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="create-filters"></a>Vytváření filtrů
### <a name="create-global-filters"></a>Vytvořit globální filtry
Pokud chcete vytvořit globální filtr, použijte následující požadavky HTTP:  

#### <a name="http-request"></a>Požadavek HTTP
Hlavičky požadavku

```console
POST https://media.windows.net/API/Filters HTTP/1.1 
DataServiceVersion:3.0 
MaxDataServiceVersion: 3.0 
Content-Type: application/json 
Accept: application/json 
Accept-Charset: UTF-8 
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19 
x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
Host:media.windows.net 
```

Text požadavku 

```console
{  
   "Name":"GlobalFilter",
   "PresentationTimeRange":{  
      "StartTimestamp":"0",
      "EndTimestamp":"9223372036854775807",
      "PresentationWindowDuration":"12000000000",
      "LiveBackoffDuration":"0",
      "Timescale":"10000000"
   },
   "Tracks":[  
      {  
         "PropertyConditions":
              [  
            {  
               "Property":"Type",
               "Value":"audio",
               "Operator":"Equal"
            },
            {  
               "Property":"Bitrate",
               "Value":"0-2147483647",
               "Operator":"Equal"
            }
         ]
      }
   ]
}
```



#### <a name="http-response"></a>Odpověď HTTP

```console
HTTP/1.1 201 Created 
```

### <a name="create-local-assetfilters"></a>Vytvořit místní AssetFilters
Pokud chcete vytvořit místní AssetFilter, použijte následující požadavky HTTP:  

#### <a name="http-request"></a>Požadavek HTTP
Hlavičky požadavku

```console
POST https://media.windows.net/API/AssetFilters HTTP/1.1 
DataServiceVersion: 3.0 
MaxDataServiceVersion: 3.0 
Content-Type: application/json 
Accept: application/json 
Accept-Charset: UTF-8 
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19 
x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
Host: media.windows.net  
```

Text požadavku 

```console
{   
   "Name":"AssetFilter", 
   "ParentAssetId":"nb:cid:UUID:536e555d-1500-80c3-92dc-f1e4fdc6c592", 
   "PresentationTimeRange":{   
      "StartTimestamp":"0", 
      "EndTimestamp":"9223372036854775807", 
      "PresentationWindowDuration":"12000000000", 
      "LiveBackoffDuration":"0", 
      "Timescale":"10000000" 
   }, 
   "Tracks":[   
      {   
         "PropertyConditions": 
              [   
            {   
               "Property":"Type", 
               "Value":"audio", 
               "Operator":"Equal" 
            }, 
            {   
               "Property":"Bitrate", 
               "Value":"0-2147483647", 
               "Operator":"Equal" 
            } 
         ] 
      } 
   ] 
} 
```

#### <a name="http-response"></a>Odpověď HTTP

```console
HTTP/1.1 201 Created 
. . . 
```

## <a name="list-filters"></a>Seznam filtrů
### <a name="get-all-global-filters-in-the-ams-account"></a>Načíst všechny globální **filtry** s v účtu AMS
Chcete-li zobrazit seznam filtrů, použijte následující požadavky HTTP: 

#### <a name="http-request"></a>Požadavek HTTP

```console
GET https://media.windows.net/API/Filters HTTP/1.1 
DataServiceVersion:3.0 
MaxDataServiceVersion: 3.0 
Accept: application/json 
Accept-Charset: UTF-8 
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19 
Host: media.windows.net 
```

### <a name="get-assetfilters-associated-with-an-asset"></a>Získat **AssetFilter** s přidruženou k assetu
#### <a name="http-request"></a>Požadavek HTTP

```console
GET https://media.windows.net/API/Assets('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592')/AssetFilters HTTP/1.1 
DataServiceVersion: 3.0 
MaxDataServiceVersion: 3.0 
Accept: application/json 
Accept-Charset: UTF-8 
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19 
x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
Host: media.windows.net 
```

### <a name="get-an-assetfilter-based-on-its-id"></a>Získat **AssetFilter** na základě jeho ID
#### <a name="http-request"></a>Požadavek HTTP

```console
GET https://media.windows.net/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__TestFilter') HTTP/1.1 
DataServiceVersion: 3.0 
MaxDataServiceVersion: 3.0 
Accept: application/json 
Accept-Charset: UTF-8 
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19 
x-ms-client-request-id: 00000000
```

## <a name="update-filters"></a>Aktualizovat filtry
Pomocí PATCH, PUT nebo MERGE můžete aktualizovat filtr o nové hodnoty vlastností.  Další informace o těchto operacích naleznete v tématu [patch, PUT, Merge](/openspecs/windows_protocols/ms-odata/59d5abd3-7b12-490a-a0e2-9d9324b91893).

Pokud filtr aktualizujete, může trvat až dvě minuty, než koncový bod streamování pravidel aktualizuje pravidla. Pokud byl obsah obsluhován pomocí tohoto filtru (a ukládá do mezipaměti proxy a mezipaměti CDN), může aktualizace tohoto filtru způsobit selhání přehrávače. Po aktualizaci filtru vymažte mezipaměť. Pokud tato možnost není možná, zvažte použití jiného filtru.  

### <a name="update-global-filters"></a>Aktualizovat globální filtry
Pokud chcete aktualizovat globální filtr, použijte následující požadavky HTTP: 

#### <a name="http-request"></a>Požadavek HTTP
Hlavičky žádosti: 

```console
MERGE https://media.windows.net/API/Filters('filterName') HTTP/1.1 
DataServiceVersion:3.0 
MaxDataServiceVersion: 3.0 
Content-Type: application/json 
Accept: application/json 
Accept-Charset: UTF-8 
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19 
x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
Host: media.windows.net 
Content-Length: 384
```

Text požadavku: 

```console
{ 
   "Tracks":[   
      {   
         "PropertyConditions": 
         [   
            {   
               "Property":"Type", 
               "Value":"audio", 
               "Operator":"Equal" 
            }, 
            {   
               "Property":"Bitrate", 
               "Value":"0-2147483647", 
               "Operator":"Equal" 
            } 
         ] 
      } 
   ] 
} 
```

### <a name="update-local-assetfilters"></a>Aktualizovat místní AssetFilters
Pokud chcete aktualizovat místní filtr, použijte následující požadavky HTTP: 

#### <a name="http-request"></a>Požadavek HTTP
Hlavičky žádosti: 

```console
MERGE https://media.windows.net/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__TestFilter')  HTTP/1.1 
DataServiceVersion: 3.0 
MaxDataServiceVersion: 3.0 
Content-Type: application/json 
Accept: application/json 
Accept-Charset: UTF-8 
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19 
x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
Host: media.windows.net 
```

Text požadavku: 

```console
{ 
   "Tracks":[   
      {   
         "PropertyConditions": 
         [   
            {   
               "Property":"Type", 
               "Value":"audio", 
               "Operator":"Equal" 
            }, 
            {   
               "Property":"Bitrate", 
               "Value":"0-2147483647", 
               "Operator":"Equal" 
            } 
         ] 
      } 
   ] 
} 
```

## <a name="delete-filters"></a>Odstranit filtry
### <a name="delete-global-filters"></a>Odstranit globální filtry
Pokud chcete odstranit globální filtr, použijte následující požadavky HTTP:

#### <a name="http-request"></a>Požadavek HTTP

```console
DELETE https://media.windows.net/api/Filters('GlobalFilter') HTTP/1.1 
DataServiceVersion:3.0 
MaxDataServiceVersion: 3.0 
Accept: application/json 
Accept-Charset: UTF-8 
Authorization: Bearer <ENCODED JWT TOKEN>  
x-ms-version: 2.19 
Host: media.windows.net 
```

### <a name="delete-local-assetfilters"></a>Odstranit místní AssetFilters
Pokud chcete odstranit místní AssetFilter, použijte následující požadavky HTTP:

#### <a name="http-request"></a>Požadavek HTTP

```console
DELETE https://media.windows.net/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__LocalFilter') HTTP/1.1 
DataServiceVersion: 3.0 
MaxDataServiceVersion: 3.0 
Accept: application/json 
Accept-Charset: UTF-8 
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19 
Host: media.windows.net 
```

## <a name="build-streaming-urls-that-use-filters"></a>Vytváření streamování adres URL, které používají filtry
Informace o tom, jak publikovat a doručovat vaše assety, najdete v tématu [doručování obsahu zákazníkům – přehled](media-services-deliver-content-overview.md).

Následující příklady ukazují, jak přidat filtry na adresy URL streamování.

**MPEG DASH** 

`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf, filter=MyFilter)`

**Apple HTTP Live Streaming (HLS) v4**

`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl, filter=MyFilter)`

**Apple HTTP Live Streaming (HLS) V3**

`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3, filter=MyFilter)`

**Technologie Smooth Streaming**

`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyFilter)`

    
## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Viz také
[Přehled dynamických manifestů](media-services-dynamic-manifest-overview.md)
