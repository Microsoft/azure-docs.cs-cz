---
title: Odesílání a pomocí místní firmy hledání rozhraní API Bingu pro dotazy a odpovědi | Dokumentace Microsoftu
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto článku se naučíte odeslat a pomocí vyhledávacích dotazů místní firmy API pro vyhledávání Bingu.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-local-business
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh; v-gedod
ms.openlocfilehash: a86db516e76a91d6e4bec378c47baf6eec13d032
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/02/2018
ms.locfileid: "50959048"
---
# <a name="sending-and-using-bing-local-business-search-api-queries-and-responses"></a>Odesílání a pomocí místní firmy hledání rozhraní API Bingu pro dotazy a odpovědi

Výsledky místního můžete získat z místní firmy hledání rozhraní API Bingu odesílání vyhledávací dotaz pro svůj koncový bod a včetně `Ocp-Apim-Subscription-Key` hlavičky, která je povinný. Spolu s jsou dostupné [záhlaví](local-search-reference.md#headers) a [parametry](local-search-reference.md#query-parameters), vyhledávání můžete přizpůsobit tak, že zadáte [geografické hranice](specify-geographic-search.md) pro oblasti pro hledání a [kategorie](local-search-query-response.md) míst vrátila.

## <a name="creating-a-request"></a>Vytvoření žádosti

Chcete-li odeslat požadavek místní firmy API pro vyhledávání Bingu, přidejte hledaný termín pro `q=` parametr před přidáním do koncového bodu rozhraní API a včetně `Ocp-Apim-Subscription-Key` záhlaví. Příklad:

`https://api.cognitive.microsoft.com/bing/localbusinesses/v7.0/search?q=restaurant+in+Bellevue`

Úplná syntaxe adresy URL je uveden níže. Zobrazit místní firmy hledání rozhraní API Bingu [rychlých startů](quickstarts/local-quickstart.md)a referenční obsah pro [záhlaví](local-search-reference.md#headers) a [parametry](local-search-reference.md#query-parameters) Další informace o odesílání požadavků. 

Informace o kategoriích místní hledání, najdete v části [hledat kategorie pro místní obchodní hledání rozhraní API Bingu](local-categories.md).

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search[?q][&localCategories][&cc][&mkt][&safesearch][&setlang][&count][&first][&localCircularView][&localMapView]
```

## <a name="using-responses"></a>Pomocí odpovědí

Obsahují odpověďmi ve formátu JSON z místní firmy hledání rozhraní API Bingu `SearchResponse` objektu. Rozhraní API vrátí relevantní výsledky vyhledávání v `places` pole. Pokud se nenajdou žádné výsledky, `places` pole nebudou zahrnuty v odpovědi.

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

### <a name="search-result-attributes"></a>Atributy výsledek vyhledávání

Výsledky JSON vrácená rozhraním API obsahovat následující atributy:

* _typ
* Adresa
* entityPresentationInfo
* Geograficky
* id
* jméno
* routeablePoint
* Telefon
* url

Obecné informace o záhlaví, parametry, kódy na trhu, objekty odpovědi, chyby dále, viz [místní API pro vyhledávání Bingu v7](local-search-reference.md) odkaz.

> [!NOTE]
> Jste nebo třetích stran vaším jménem nemusí použít, zachovat, ukládat, ukládat do mezipaměti, sdílet, nebo distribuovat všechna data z místní rozhraní Search API pro účely testování, vývoje, školení, distribuci nebo zpřístupnění službám jiných společností než Microsoft nebo funkce. 


## <a name="example-json-response"></a>Příklad JSON odpovědi

Následující odpověď JSON obsahuje výsledky hledání určenými v dotazu `?q=restaurant+in+Bellevue`.

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


## <a name="next-steps"></a>Další postup
- [Rychlý start místní firmy vyhledávání](quickstarts/local-quickstart.md)
- [Rychlý start místní firmy hledání Java](quickstarts/local-search-java-quickstart.md)
- [Rychlý start místní uzel hledat firmy](quickstarts/local-search-node-quickstart.md)
- [Rychlý start místní firmy hledání Pythonu](quickstarts/local-search-python-quickstart.md)