---
title: Odesílání a používání dotazů a odpovědí rozhraní API – Vyhledávání místních obchodních společností Bing
titleSuffix: Azure Cognitive Services
description: V tomto článku se dozvíte, jak odesílat a používat vyhledávací dotazy pomocí rozhraní API pro vyhledávání místních společností Bingu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: rosh
ms.openlocfilehash: 25bcdb89002fec4f9b67b091996d7bf80bcf21c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74326723"
---
# <a name="sending-and-using-bing-local-business-search-api-queries-and-responses"></a>Odesílání a používání dotazů a odpovědí rozhraní API pro vyhledávání v místní chodu Bingu

Místní výsledky z rozhraní API pro vyhledávání místních společností Bingu můžete `Ocp-Apim-Subscription-Key` získat odesláním vyhledávacího dotazu do jeho koncového bodu včetně záhlaví, které je povinné. Spolu s [dostupnými záhlavími](local-search-reference.md#headers) a [parametry](local-search-reference.md#query-parameters)lze vyhledávání přizpůsobit zadáním [zeměpisných hranic](specify-geographic-search.md) oblasti, která má být prohledána, a [kategorií](local-search-query-response.md) vrácených míst.

## <a name="creating-a-request"></a>Vytvoření žádosti

Chcete-li odeslat požadavek do rozhraní API pro vyhledávání `q=` v místní firmě Bingu, přidejte k parametru hledaný termín před jeho přidáním do koncového bodu rozhraní API a včetně `Ocp-Apim-Subscription-Key` záhlaví. Například:

`https://api.cognitive.microsoft.com/bing/localbusinesses/v7.0/search?q=restaurant+in+Bellevue`

Úplná syntaxe adresy URL požadavku je uvedena níže. Další informace o odesílání požadavků naleznete v [rychlých startech](quickstarts/local-quickstart.md)rozhraní API pro vyhledávání v místním podniku Bing u [tématu](local-search-reference.md#headers) a v referenčním obsahu záhlaví a [parametrů.](local-search-reference.md#query-parameters) 

Informace o místních kategoriích vyhledávání naleznete [v tématu Hledání kategorií rozhraní API pro vyhledávání v místní firmě Bingu](local-categories.md).

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search[?q][&localCategories][&cc][&mkt][&safesearch][&setlang][&count][&first][&localCircularView][&localMapView]
```

## <a name="using-responses"></a>Použití odpovědí

Odpovědi JSON z rozhraní API pro `SearchResponse` vyhledávání místních společností Bingobsahují objekt. Rozhraní API vrátí příslušné výsledky `places` vyhledávání v poli. Pokud nejsou nalezeny `places` žádné výsledky, pole nebude zahrnuto do odpovědi.

[!INCLUDE [cognitive-services-bing-url-note](../../../includes/cognitive-services-bing-url-note.md)]

```
{
   "_type": "SearchResponse",
   "queryContext": {
      "originalQuery": "restaurant in Bellevue"
   },
   "places": {
      "totalEstimatedMatches": 10,
. . . 
```

### <a name="search-result-attributes"></a>Atributy výsledků hledání

Výsledky JSON vrácené rozhraním API zahrnují následující atributy:

* _type
* adresa
* entityPresentationInfo
* Geo
* id
* jméno
* routeablePoint
* Telefonní
* url

Obecné informace o záhlavích, parametrech, kódech trhu, objektech odpovědí, chybách atd., naleznete v odkazu rozhraní [API pro místní vyhledávání Bingu v7.](local-search-reference.md)

> [!NOTE]
> Vy nebo třetí strana vaším jménem nesmíte používat, uchovávat, ukládat, ukládat, ukládat, sdílet ani distribuovat žádná data z místního rozhraní API pro vyhledávání za účelem testování, vývoje, školení, distribuce nebo zpřístupnění jakékoli služby nebo funkce jiné společnosti než Microsoft. 


## <a name="example-json-response"></a>Příklad odpovědi JSON

Následující odpověď JSON obsahuje výsledky hledání `?q=restaurant+in+Bellevue`určené dotazem .

```json
Vary: Accept-Encoding
BingAPIs-TraceId: 5376FFEB65294E24BB9F91AD70545826
BingAPIs-SessionId: 06ED7CEC80F746AA892EDAAC97CB0CB4
X-MSEdge-ClientID: 112C391E72C0624204153594738C63DE
X-MSAPI-UserState: aeab
BingAPIs-Market: en-US
X-Search-ResponseInfo: InternalResponseTime=659,MSDatacenter=CO4
X-MSEdge-Ref: Ref A: 5376FFEB65294E24BB9F91AD70545826 Ref B: BY3EDGE0306 Ref C: 2018-10-16T16:26:15Z
apim-request-id: fe54f585-7c54-4bf5-8b92-b9bede2b710a
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
x-content-type-options: nosniff
Cache-Control: max-age=0, private
Date: Tue, 16 Oct 2018 16:26:15 GMT
P3P: CP="NON UNI COM NAV STA LOC CURa DEVa PSAa PSDa OUR IND"
Content-Length: 978
Content-Type: application/json; charset=utf-8
Expires: Tue, 16 Oct 2018 16:25:15 GMT

{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "restaurant Bellevue"
  },
  "places": {
    "totalEstimatedMatches": 50,
    "value": [{
      "_type": "LocalBusiness",
      "id": "https:\/\/cognitivegblppe.azure-api.net\/api\/v7\/#Places.0",
      "name": "Facing East Taiwanese Restaurant",
      "url": "http:\/\/litadesign.wix.com\/facingeastrestaurant",
      "entityPresentationInfo": {
        "entityScenario": "ListItem",
        "entityTypeHints": ["Place", "LocalBusiness", "Restaurant"]
      },
      "geo": {
        "latitude": 47.6199188232422,
        "longitude": -122.202796936035
      },
      "routablePoint": {
        "latitude": 47.6199188232422,
        "longitude": -122.201713562012
      },
      "address": {
        "streetAddress": "1075 Bellevue Way NE Ste B2",
        "addressLocality": "Bellevue",
        "addressRegion": "WA",
        "postalCode": "98004",
        "addressCountry": "US",
        "neighborhood": "Bellevue",
        "text": "1075 Bellevue Way NE Ste B2, Bellevue, WA 98004"
      },
      "telephone": "(425) 688-2986"
    }],
    "searchAction": {
      "location": [{
        "name": "Bellevue, Washington"
      }],
      "query": "restaurant"
    }
  }
}
 
```

## <a name="throttling-requests"></a>Omezování požadavků

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]


## <a name="next-steps"></a>Další kroky
- [Rychlý start hledání místní firmy](quickstarts/local-quickstart.md)
- [Rychlý start java vyhledávání místních společností](quickstarts/local-search-java-quickstart.md)
- [Rychlý start uzlu místního obchodního hledání](quickstarts/local-search-node-quickstart.md)
- [Rychlý start pythonu pro místní obchodní vyhledávání](quickstarts/local-search-python-quickstart.md)
