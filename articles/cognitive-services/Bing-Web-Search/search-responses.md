---
title: Odpovědi na vyhledávání – rozhraní API webové vyhledávání Bingu
titleSuffix: Azure Cognitive Services
description: Další informace o typech odpovědí a odpovědí z rozhraní API webové vyhledávání Bingu.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: conceptual
ms.date: 8/13/2018
ms.author: erhopf
ms.openlocfilehash: f3a4c8bb024aa5e92365b72b8cc2180cc6f4d6d4
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46123772"
---
# <a name="bing-web-search-api-response-structure-and-answer-types"></a>Typy struktury a odpověď odezvy API vyhledávání na webu Bingu  

Pokud odešlete žádost o vyhledávání Bingu pro vyhledávání na webu, vrátí [ `SearchResponse` ](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse) objektu v textu odpovědi. Objekt obsahuje pole pro každou odpověď, která určuje Bingu je relevantní pro dotaz. Tento příklad ukazuje objektu odpovědi, pokud Bing vrátí všechny odpovědi:

```json
{
    "_type": "SearchResponse",
    "queryContext": {...},
    "webPages": {...},
    "images": {...},
    "relatedSearches": {...},
    "videos": {...},
    "news": {...},
    "spellSuggestion": {...},
    "computation": {...},
    "timeZone": {...},
    "rankingResponse": {...}
}, ...
```

Vyhledávání na webu Bingu obvykle vrátí podmnožinu odpovědi. Například, pokud byl termín dotazu *řízení dinghies*, odpověď může obsahovat `webPages`, `images`, a `rankingResponse`. Pokud jste použili [responseFilter](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#responsefilter) k filtrování webových stránek, budou vždy obsahuje odpovědi `webpages` a `rankingResponse` odpovědi.

## <a name="webpages-answer"></a>Odpověď webové stránky

[Webové stránky](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#webanswer) odpověď obsahuje seznam odkazů na webové stránky, které vyhledávání na webu Bingu určit byly relevantní pro dotaz. Každý [webové stránky](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#webpage) v seznam obsahuje adresu URL zobrazované jméno, adresu url, na stránce, stručný popis obsahu a datum Bingu najít obsah.

```json
{
    "id": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0",
    "name": "Dinghy sailing",
    "url": "https:\/\/www.bing.com\/cr?IG=3A43CA5...",
    "displayUrl": "https:\/\/en.contoso.com\/wiki\/Dinghy_sailing",
    "snippet": "Dinghy sailing is the activity of sailing small boats...",
    "dateLastCrawled": "2017-04-05T16:25:00"
}, ...
```

Použití `name` a `url` vytvoření hypertextového odkazu, který má uživatel na webovou stránku.

<!-- Remove until this can be replaced with a sanitized version.
The following shows an example of how you might display the webpage in a search results page.

![Rendered webpage example](./media/cognitive-services-bing-web-api/bing-rendered-webpage-example.PNG)
-->

## <a name="images-answer"></a>Odpověď imagí

[Imagí](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) odpověď obsahuje seznam imagí, které Bing si mysleli, že byly relevantní pro dotaz. Každý [image](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image) v seznamu obsahuje adresu URL obrázku, jeho velikost, rozměry a jeho formát kódování. Objekt obrázku obsahuje také adresu URL miniatury obrázku a její rozměry.

```json
{
    "name": "Rich Passage Sailing Dinghy",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=3A43CA5CA64...",
    "thumbnailUrl": "https:\/\/tse1.mm.bing.net\/th?id=OIP....",
    "datePublished": "2011-10-29T11:26:00",
    "contentUrl": "http:\/\/upload.contoso.com\/sailing\/...",
    "hostPageUrl": "http:\/\/www.bing.com\/cr?IG=3A43CA5CA6464....",
    "contentSize": "79239 B",
    "encodingFormat": "jpeg",
    "hostPageDisplayUrl": "http:\/\/en.contoso.com\/wiki\/File...",
    "width": 526,
    "height": 688,
    "thumbnail": {
        "width": 229,
        "height": 300
    },
    "insightsSourcesSummary": {
        "shoppingSourcesCount": 0,
        "recipeSourcesCount": 0
    }
}, ...
```

V závislosti na uživatelských zařízeních by obvykle zobrazit pouze podmnožinu miniatury s možností pro uživatele, chcete-li zobrazit zbývající bitové kopie.

<!-- Remove until this can be replaced with a sanitized version.
![List of thumbnail images](./media/cognitive-services-bing-web-api/bing-web-image-thumbnails.PNG)
-->

Miniaturu můžete také zvětšit, když na ni uživatel najedete kurzorem. Pokud obrázek zvětšíte, nezapomeňte mu přiřadit atribut. Například extrahováním hostitele z `hostPageDisplayUrl` a jeho zobrazení pod obrázkem. Informace o změně velikosti miniatury najdete v tématu [Změna velikosti a oříznutí miniatur](./resize-and-crop-thumbnails.md).

<!-- Remove until this can be replaced with a sanitized version.
![Expanded view of thumbnail image](./media/cognitive-services-bing-web-api/bing-web-image-thumbnail-expansion.PNG)
-->

Pokud uživatel klikne na miniaturu, použijte `webSearchUrl` umožní uživateli Bingu stránka výsledků hledání pro Image, která obsahuje koláž imagí.

Podrobnosti o odpovědi image a Image najdete v tématu [API pro vyhledávání obrázků](../bing-image-search/search-the-web.md).

## <a name="related-searches-answer"></a>Související hledání odpovědí

[RelatedSearches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse-relatedsearches) odpověď obsahuje seznam nejoblíbenějších souvisejících dotazů provedené ostatními uživateli. Každý [dotazu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#query_obj) v seznamu obsahuje řetězec dotazu (`text`), řetězec dotazu s zvýraznění shod znaků (`displayText`) a adresu URL (`webSearchUrl`) na stránce výsledků hledání na Bingu pro daný dotaz.

```json
{
    "text": "dinghy racing teams",
    "displayText": "dinghy racing teams",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=96C4CF214A0..."
}, ...
```

Použití `displayText` řetězec dotazu a `webSearchUrl` stránka pro dotaz související s výsledky adresy URL vytvoření hypertextového odkazu, který má uživatel pro vyhledávání Bingu. Můžete také použít `text` řetězce dotazu API vyhledávání na webu dotazu a zobrazit výsledky.

Informace o tom, jak zpracovat značek zvýraznění v `displayText`, naleznete v tématu [přístupů zvýraznění](./hit-highlighting.md).

Následující je příkladem použití souvisejících dotazů v Bing.com.

![Příklad související hledání na Bingu](./media/cognitive-services-bing-web-api/bing-web-rendered-relatedsearches.GIF)

## <a name="videos-answer"></a>Odpověď videa

[Videa](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos) odpověď obsahuje seznam videí, které Bing si mysleli, že byly relevantní pro dotaz. Každý [videa](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video) v seznamu obsahuje adresu URL videa, jeho trvání, rozměry a jeho formát kódování. Video objekt zahrnuje také adresa URL miniatury videa a na miniaturu dimenzí.

```json
{
    "name": "Sailing dinghy",
    "description": "Northwind Traders is a 12 foot gunter rigged...",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=1CAE739681D84...",
    "thumbnailUrl": "https:\/\/tse2.mm.bing.net\/th?id=OVP.wsKiL...",
    "datePublished": "2013-11-06T01:56:28",
    "publisher": [{
        "name": "Fabrikam"
    }],
    "contentUrl": "https:\/\/www.fabrikam.com\/watch?v=MrVBWZpJjX",
    "hostPageUrl": "https:\/\/www.bing.com\/cr?IG=1CAE739681D8400DB...",
    "encodingFormat": "mp4",
    "hostPageDisplayUrl": "https:\/\/www.fabrikam.com\/watch?v=MrBWZpJjXo",
    "width": 1280,
    "height": 720,
    "duration": "PT3M47S",
    "motionThumbnailUrl": "https:\/\/tse2.mm.bing.net\/th?id=OM.oa...",
    "embedHtml": "<iframe width=\"1280\" height=\"720\" src=\"http:\/\/www....><\/iframe>",
    "allowHttpsEmbed": true,
    "viewCount": 19089,
    "thumbnail": {
        "width": 300,
        "height": 168
    },
    "allowMobileEmbed": true,
    "isSuperfresh": false
}, ...
```

V závislosti na uživatelských zařízeních by obvykle zobrazit pouze podmnožinu videa s možností pro uživatele, chcete-li zobrazit zbývající videa. Zobrazí miniatury videa o délce videa, popis (název) a přiřazení (vydavatel).

<!-- Remove until this can be replaced with a sanitized version.
![List of video thumbnails](./media/cognitive-services-bing-web-api/bing-web-video-thumbnails.PNG)
-->

Uživatel najede myší na miniaturu můžete použít `motionThumbnailUrl` přehrávání verze videa s miniatura. Ujistěte se, že atribut na miniaturu pohybu, když ji zobrazíte.

<!-- Remove until this can be replaced with a sanitized version.
![Motion thumbnail of a video](./media/cognitive-services-bing-web-api/bing-web-video-motion-thumbnail.PNG)
-->

Pokud uživatel klikne na miniaturu, jsou následující možnosti pro zobrazení videa:

- Použití `hostPageUrl` pro zobrazení videa na webu hostitele (například YouTube)
- Použití `webSearchUrl` pro zobrazení videa v prohlížeči video Bingu
- Použití `embedHtml` k vložení videa do vlastní prostředí

Podrobnosti o odpovědi videa a videa, najdete v tématu [Video Search API](../bing-video-search/search-the-web.md).

## <a name="news-answer"></a>Zprávy odpovědi

[Zpráv](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#news) odpověď obsahuje seznam články o novinkách, které Bing si mysleli, že byly relevantní pro dotaz. Každý [zpravodajskému článku](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#newsarticle) v seznamu obsahuje název, popis a adresu URL v článku na článek na webu hostitele. Pokud tento článek obsahuje bitovou kopii, obsahuje objekt miniaturu obrázku.

```json
{
    "name": "WC Sailing Qualifies for America Trophy with...",
    "url": "http:\/\/www.bing.com\/cr?IG=3445EEF15DAF4FFFBF7...",
    "image": {
        "contentUrl": "http:\/\/www.contoso.com\/sports\/sail...",
        "thumbnail": {
            "contentUrl": "https:\/\/www.bing.com\/th?id=ON.1...",
            "width": 400,
            "height": 272
        }
    },
    "description": "The WC sailing team qualified for a...",
    "provider": [{
        "_type": "Organization",
        "name": "contoso.com"
    }],
    "datePublished": "2017-04-16T21:56:00"
}, ...
```

V závislosti na uživatelských zařízeních by zobrazit pouze podmnožinu články o novinkách s možností pro uživatele, chcete-li zobrazit zbývající článků. Použití `name` a `url` vytvoření hypertextového odkazu, který přijímá uživateli zpravodajskému článku na webu hostitele. Pokud tento článek obsahuje bitovou kopii, ujistěte se, kliknout, čímž image pomocí `url`. Nezapomeňte použít `provider` kterému budou připsány článku.

<!-- Remove until this can be replaced with a sanitized version.
The following shows an example of how you might display articles in a search results page.

![List of news articles](./media/cognitive-services-bing-web-api/bing-web-news-list.PNG)
-->

Podrobnosti o zpráv odpovědí a články o novinkách najdete v tématu [rozhraní API pro vyhledávání zpráv](../bing-news-search/search-the-web.md).

## <a name="computation-answer"></a>Výpočet odpovědí

Pokud uživatel zadá matematickém výrazu nebo jednotka převodu dotaz, odpověď může obsahovat [výpočtu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#computation) odpovědí. `computation` Odpověď obsahuje normalizovaný výraz a jeho výsledek.

Jednotka převodu dotaz je dotaz, který převede jednu jednotku do jiného. Například *kolik stop 10 měřiče?* nebo *kolik tablespoons 1/4 cupu?*

Zobrazí se následující `computation` odpovědí pro *kolik stop 10 měřiče?*

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "10 meters",
    "value": "32.808399 feet"
}, ...
```

Následující příklad zobrazuje příklady matematické dotazy a jejich odpovídající `computation` odpovědi.

```
Query: (5+3)(10/2)+8
Encoded query: %285%2B3%29%2810%2F2%29%2B8
```

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "((5+3)*(10\/2))+8",
    "value": "48"
}
```

```
Query: sqrt(4^2+8^2)
Encoded query: sqrt%284^2%2B8^2%29
```

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "sqrt((4^2)+(8^2))",
    "value": "8.94427191"
}
```

```
Query: 30 6/8 - 18 8/16
Encoded query: 30%206%2F8%20-%2018%208%2F16
```

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#WolframAlpha",
    "expression": "30 6\/8-18 8\/16",
    "value": "12.25"
}
```

```
Query: 8^2+11^2-2*8*11*cos(37)
Encoded query: 8^2%2B11^2-2*8*11*cos%2837%29
```

```json
"computation": {
        "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
        "expression": "(8^2)+(11^2)-(2*8*11*cos(37))",
        "value": "44.4401502"
}
```

Matematický výraz může obsahovat následující symboly:

|Symbol|Popis|
|------------|-----------------|
|+|Přidání|
|-|Odčítání|
|/|Dělení|
|*|Násobení|
|^|Napájení|
|!|Faktoriál|
|.|Decimal|
|()|Priorita seskupení|
|[]|Funkce|

Matematický výraz může obsahovat následující konstanty:

|Symbol|Popis|
|------------|-----------------|
|Pi|3,14159...|
|Míry|Míry|
|Můžu|IMAGINARY číslo|
|e|e, 2.71828...|
|GoldenRatio|Zlatý poměr 1.61803...|

Matematický výraz může obsahovat následující funkce:

|Symbol|Popis|
|------------|-----------------|
|Sqrt|Druhá odmocnina|
|Sin [x], Cos [x], Tan [x]<br />CSC [x], [x] sekundu Cot [x]|Trigonometrické funkce (s argumenty v radiánech)|
|ArcSin [x], [x] ArcCos ArcTan [x]<br />ArcCsc [x], [x] ArcSec ArcCot [x]|Inverzní trigonometrické funkce (poskytující výsledky v radiánech)|
|Exp [x] E ^ x|Exponenciální funkce|
|Protokol [x]|Přirozený logaritmus|
|SINH [x] Cosh [x], Tanh [x]<br />Csch [x], [x] Sech Coth [x]|Hyperbolické funkce|
|ArcSinh [x], [x] ArcCosh ArcTanh [x]<br />ArcCsch [x], [x] ArcSech ArcCoth [x]|Inverzní hyperbolické funkce|

Matematické výrazy, které obsahují proměnné (pro příklad, 4 x + 6 = 18, kde x je proměnná) nejsou podporovány.

## <a name="timezone-answer"></a>Odpověď časového pásma

Pokud uživatel zadá dotaz času nebo data, odpověď může obsahovat [časové pásmo](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#timezone) odpovědí. Tato odpověď podporuje implicitní nebo explicitní dotazy. Implicitní dotazu, jako například *kdy je to?*, vrátí místního času na základě umístění uživatele. Explicitní dotazu, jako například *dobu je v Praze?*, vrátí místní čas pro Seattle, WA.

`timeZone` Odpověď obsahuje název umístění, aktuální datum a čas UTC v zadaném umístění a posun čas UTC. Pokud je hraniční umístění v rámci více časových pásem, obsahuje odpověď aktuální datum a čas UTC všech časových pásem v rámci hranice. Například protože Florida stavu spadá do dvou časových pásem, odpověď obsahuje místní data a času z obou časových pásem.  

Pokud dotaz požádá o době státě nebo zemi, Bingu určuje primární města v rámci hranice zeměpisné umístění a vrátí jej v `primaryCityTime` pole. Pokud na hranici obsahuje víc časových pásem, zbývající časových pásem jsou vráceny v `otherCityTimes` pole.

Následující příklad ukazuje dotazů, který vrací `timeZone` odpovědí.

```
Query: What time is it?

"timeZone": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
    "primaryCityTime": {
        "location": "Redmond, Washington, United States",
        "time": "2015-10-27T08:38:12.1189231Z",
        "utcOffset": "UTC-7"
    }
}

Query: What time is it in the Pacific time zone?

"timeZone": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
    "primaryCityTime": {
        "location": "Pacific Time Zone",
        "time": "2015-10-23T12:33:19.0728146Z",
        "utcOffset": "UTC-7"
    }
}

Query: Time in Florida?

"timeZone": {
        "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
        "primaryCityTime": {
            "location": "Tallahassee, Florida, United States",
            "time": "2015-10-23T13:04:56.6774389Z",
            "utcOffset": "UTC-4"
        },
        "otherCityTimes": [{
            "location": "Pensacola",
            "time": "2015-10-23T12:04:56.6664294Z",
            "utcOffset": "UTC-5"
        }]
}

Query: What time is it in the U.S.

"timeZone": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
    "primaryCityTime": {
        "location": "Washington, D.C., United States",
        "time": "2015-10-23T15:27:59.8892745Z",
        "utcOffset": "UTC-4"
    },
    "otherCityTimes": [{
        "location": "Honolulu",
        "time": "2015-10-23T09:27:59.8892745Z",
        "utcOffset": "UTC-10"
    },
    {
        "location": "Anchorage",
        "time": "2015-10-23T11:27:59.8892745Z",
        "utcOffset": "UTC-8"
    },
    {
        "location": "Phoenix",
        "time": "2015-10-23T12:27:59.8892745Z",
        "utcOffset": "UTC-7"
    },
    {
        "location": "Los Angeles",
        "time": "2015-10-23T12:27:59.8942788Z",
        "utcOffset": "UTC-7"
    },
    {
        "location": "Denver",
        "time": "2015-10-23T13:27:59.8812681Z",
        "utcOffset": "UTC-6"
    },
    {
        "location": "Chicago",
        "time": "2015-10-23T14:27:59.8892745Z",
        "utcOffset": "UTC-5"
    }]
}
```

## <a name="spellsuggestion-answer"></a>SpellSuggestion odpovědí

Pokud Bingu určí, že uživatel může mít určený k vyhledání něco jiného, obsahuje odpověď [SpellSuggestions](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#spellsuggestions) objektu. Například, pokud uživatel vyhledává *carlos pera*, Bing, může určit, že uživatel pravděpodobně v úmyslu místo toho vyhledat Carlos Pena (na základě posledních hledání objektů *carlos pera*). Následuje příklad odpovědi pravopisu.

```json
"spellSuggestions": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#SpellSuggestions",
    "value": [{
        "text": "carlos pena",
        "displayText": "carlos pena"
    }]
}, ...
```

Následuje ukázka použití návrhy pravopisu Bingu.

![Příklad návrhy pravopisu Bingu](./media/cognitive-services-bing-web-api/bing-web-spellingsuggestion.GIF)  

## <a name="next-steps"></a>Další postup  

* Kontrola [omezování žádostí](throttling-requests.md) dokumentaci.  

## <a name="see-also"></a>Další informace najdete v tématech  

* [Referenční dokumentace rozhraní API webové vyhledávání Bingu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)
