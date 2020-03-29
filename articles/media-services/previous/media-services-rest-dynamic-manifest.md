---
title: Vytváření filtrů pomocí rozhraní REST API služby Azure Media Services | Dokumenty společnosti Microsoft
description: Toto téma popisuje, jak vytvořit filtry, aby je váš klient mohl použít k vysílání datového proudu určitých částí datového proudu. Media Services vytváří dynamické manifesty k dosažení tohoto selektivního streamování.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: f7d23daf-7cd2-49c7-a195-ab902912ab3c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.reviewr: cenkdin
ms.openlocfilehash: b778ad8c59cf51f92584cd3590f7d99244f37b2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76774960"
---
# <a name="creating-filters-with-azure-media-services-rest-api"></a>Vytváření filtrů pomocí rozhraní REST služby Azure Media Services 
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-dynamic-manifest.md)
> * [Odpočinku](media-services-rest-dynamic-manifest.md)
> 
> 

Počínaje verzí 2.17 umožňuje služba Media Services definovat filtry pro vaše datové zdroje. Tyto filtry jsou pravidla na straně serveru, která zákazníkům umožňují zvolit si například: přehrávání pouze části videa (namísto přehrávání celého videa) nebo určení pouze podmnožinu zvukových a obrazových interpretací, které zařízení zákazníka zvládne (namísto všechny interpretace, které jsou přidruženy k prostředku). Toto filtrování vašich datových zdrojů je archivováno prostřednictvím **dynamického manifestu,** který je vytvořen na základě požadavku zákazníka na streamování videa na základě určených filtrů.

Podrobnější informace týkající se filtrů a dynamického manifestu naleznete [v tématu Přehled dynamických manifestů](media-services-dynamic-manifest-overview.md).

Tento článek ukazuje, jak pomocí rest API vytvořit, aktualizovat a odstranit filtry. 

## <a name="types-used-to-create-filters"></a>Typy používané k vytváření filtrů
Při vytváření filtrů se používají následující typy:  

* [Filtr](https://docs.microsoft.com/rest/api/media/operations/filter)
* [AssetFilter](https://docs.microsoft.com/rest/api/media/operations/assetfilter)
* [PresentationTimeRange](https://docs.microsoft.com/rest/api/media/operations/presentationtimerange)
* [FilterTrackSelect a FilterTrackPropertyCondition](https://docs.microsoft.com/rest/api/media/operations/filtertrackselect)

> [!NOTE]
> 
> Při přístupu k entitám ve službě Media Services je nutné nastavit konkrétní pole záhlaví a hodnoty v požadavcích HTTP. Další informace naleznete [v tématu Setup for Media Services REST API Development](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Připojení ke službě Media Services

Informace o tom, jak se připojit k rozhraní AMS API, najdete [v tématu Přístup k rozhraní API Azure Media Services pomocí ověřování Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="create-filters"></a>Vytváření filtrů
### <a name="create-global-filters"></a>Vytvořit globální filtry
Chcete-li vytvořit globální filtr, použijte následující požadavky HTTP:  

#### <a name="http-request"></a>Požadavek HTTP
Hlavičky požadavku

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

Text požadavku 

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




#### <a name="http-response"></a>Odpověď HTTP
    HTTP/1.1 201 Created 

### <a name="create-local-assetfilters"></a>Vytvořit místní assetfilters
Chcete-li vytvořit místní filtr AssetFilter, použijte následující požadavky HTTP:  

#### <a name="http-request"></a>Požadavek HTTP
Hlavičky požadavku

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

Text požadavku 

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

#### <a name="http-response"></a>Odpověď HTTP
    HTTP/1.1 201 Created 
    . . . 

## <a name="list-filters"></a>Filtry seznamů
### <a name="get-all-global-filters-in-the-ams-account"></a>Získání všech globálních **filtrů**v účtu AMS
Chcete-li vypsat filtry, použijte následující požadavky HTTP: 

#### <a name="http-request"></a>Požadavek HTTP
    GET https://media.windows.net/API/Filters HTTP/1.1 
    DataServiceVersion:3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19 
    Host: media.windows.net 

### <a name="get-assetfilters-associated-with-an-asset"></a>Získání **assetfilteru**s přidruženým k datovému zdroji
#### <a name="http-request"></a>Požadavek HTTP
    GET https://media.windows.net/API/Assets('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592')/AssetFilters HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
    Host: media.windows.net 

### <a name="get-an-assetfilter-based-on-its-id"></a>Získání **filtru AssetFilter** na základě jeho ID
#### <a name="http-request"></a>Požadavek HTTP
    GET https://media.windows.net/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__TestFilter') HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19 
    x-ms-client-request-id: 00000000


## <a name="update-filters"></a>Aktualizovat filtry
Pomocí funkce PATCH, PUT nebo MERGE aktualizujte filtr s novými hodnotami vlastností.  Další informace o těchto operacích naleznete v tématu [PATCH, PUT, MERGE](https://msdn.microsoft.com/library/dd541276.aspx).

Pokud aktualizujete filtr, může trvat až dvě minuty, než koncový bod streamování aktualizuje pravidla. Pokud byl obsah obsluhován pomocí tohoto filtru (a ukládán do mezipaměti proxy a cdn), může aktualizace tohoto filtru způsobit selhání přehrávače. Po aktualizaci filtru vymažte mezipaměť. Pokud tato možnost není možná, zvažte použití jiného filtru.  

### <a name="update-global-filters"></a>Aktualizovat globální filtry
Chcete-li aktualizovat globální filtr, použijte následující požadavky HTTP: 

#### <a name="http-request"></a>Požadavek HTTP
Hlavičky požadavku: 

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

Text požadavku: 

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

### <a name="update-local-assetfilters"></a>Aktualizace místních filtrů majetku
Chcete-li aktualizovat místní filtr, použijte následující požadavky HTTP: 

#### <a name="http-request"></a>Požadavek HTTP
Hlavičky požadavku: 

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

Text požadavku: 

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


## <a name="delete-filters"></a>Odstranit filtry
### <a name="delete-global-filters"></a>Odstranit globální filtry
Chcete-li odstranit globální filtr, použijte následující požadavky HTTP:

#### <a name="http-request"></a>Požadavek HTTP
    DELETE https://media.windows.net/api/Filters('GlobalFilter') HTTP/1.1 
    DataServiceVersion:3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN>  
    x-ms-version: 2.19 
    Host: media.windows.net 


### <a name="delete-local-assetfilters"></a>Odstranit místní assetfilters
Chcete-li odstranit místní filtr AssetFilter, použijte následující požadavky HTTP:

#### <a name="http-request"></a>Požadavek HTTP
    DELETE https://media.windows.net/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__LocalFilter') HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19 
    Host: media.windows.net 

## <a name="build-streaming-urls-that-use-filters"></a>Vytváření streamovaných adres URL s použitím filtrů
Informace o tom, jak publikovat a doručovat datové zdroje, najdete v [tématu Doručování obsahu zákazníkům Přehled](media-services-deliver-content-overview.md).

Následující příklady ukazují, jak přidat filtry do datových adres URL.

**MPEG DASH** 

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf, filter=MyFilter)

**Apple HTTP Živé vysílání (HLS) V4**

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl, filter=MyFilter)

**Apple HTTP Živé vysílání (HLS) V3**

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3, filter=MyFilter)

**Technologie Smooth Streaming**

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyFilter)

    
## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Viz také
[Přehled dynamických manifestů](media-services-dynamic-manifest-overview.md)

