---
title: Posílání a používání dotazů a odpovědí rozhraní API – vyhledávání v místních firmách Bingu
titleSuffix: Azure Cognitive Services
description: V tomto článku se dozvíte, jak odesílat a používat vyhledávací dotazy pomocí rozhraní API Bingu pro vyhledávání v místním obchodu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: rosh
ms.openlocfilehash: 70a33774ac82312660d887fb86f7e2a482c30a0c
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96487163"
---
# <a name="sending-and-using-bing-local-business-search-api-queries-and-responses"></a>Odesílání a používání dotazů a odpovědí v rozhraní API Bingu pro vyhledávání místních obchodních hledání

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Místní výsledky můžete získat z rozhraní API pro místní vyhledávání Bingu, a to tak, že odešlete vyhledávací dotaz na jeho koncový bod a zahrnete do něj `Ocp-Apim-Subscription-Key` hlavičku, která je povinná. Spolu s dostupnými [hlavičkami](local-search-reference.md#headers) a [parametry](local-search-reference.md#query-parameters)můžete vyhledávat pomocí [geografických hranic](specify-geographic-search.md) pro oblast, která má být prohledána, a [kategorií](local-search-query-response.md) vrácených míst.

## <a name="creating-a-request"></a>Vytvoření žádosti

Pokud chcete poslat požadavek do rozhraní API služby Bing pro vyhledávání v místním obchodu, `q=` před přidáním ho do koncového bodu rozhraní API přidejte hledaný termín a včetně `Ocp-Apim-Subscription-Key` hlavičky. Příklad:

`https://api.cognitive.microsoft.com/bing/localbusinesses/v7.0/search?q=restaurant+in+Bellevue`

Úplná syntaxe adresy URL požadavku je uvedena níže. Další informace o odesílání požadavků najdete v tématu [rychlý Start](quickstarts/local-quickstart.md)k rozhraní API Bingu pro vyhledávání místních obchodních funkcí a referenční obsah pro [záhlaví](local-search-reference.md#headers) a [parametry](local-search-reference.md#query-parameters) . 

Informace o místních kategoriích vyhledávání najdete v tématu [kategorie hledání pro rozhraní API Bingu pro vyhledávání místních obchodních aplikací](local-categories.md).

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search[?q][&localCategories][&cc][&mkt][&safesearch][&setlang][&count][&first][&localCircularView][&localMapView]
```

## <a name="using-responses"></a>Použití odpovědí

Odpovědi JSON z rozhraní API služby Bing pro vyhledávání v místním obchodu obsahují `SearchResponse` objekt. Rozhraní API vrátí relevantní výsledky hledání v `places` poli. Pokud nejsou nalezeny žádné výsledky, `places` pole nebude zahrnuto do odpovědi.

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

Výsledky JSON vracené rozhraním API obsahují tyto atributy:

* _type
* adresa
* entityPresentationInfo
* geograficky
* id
* name
* routeablePoint
* Link
* url

Obecné informace o hlavičkách, parametrech, kódech trhu, objektech odpovědí, chybách atd. najdete v referenčních informacích k [rozhraní API Bingu pro vyhledávání v7](local-search-reference.md) .

> [!NOTE]
> Vy nebo třetí strana vaším jménem nesmíte k testování, vývoji, školení, distribuci nebo zpřístupnění jakékoli služby nebo funkce od Microsoftu používat, uchovávat, ukládat, ukládat do mezipaměti, sdílet ani distribuovat žádná data z rozhraní API místního vyhledávání. 


## <a name="example-json-response"></a>Příklad odpovědi JSON

Následující odpověď JSON obsahuje výsledky hledání zadané dotazem `?q=restaurant+in+Bellevue` .

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
- [Rychlý start hledání místních obchodních obchodů](quickstarts/local-quickstart.md)
- [Rychlý Start pro místní vyhledávání v jazyce Java](quickstarts/local-search-java-quickstart.md)
- [Rychlý Start uzlu místního hledání firmy](quickstarts/local-search-node-quickstart.md)
- [Rychlý Start pro místní vyhledávání v Pythonu](quickstarts/local-search-python-quickstart.md)