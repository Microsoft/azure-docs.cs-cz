---
title: Struktura odpovědí rozhraní API pro vyhledávání na webu Bingu a typy odpovědí
titleSuffix: Azure Cognitive Services
description: Při odeslání požadavku hledání na webu Bingvrátí `SearchResponse` objekt v těle odpovědi.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 95ebfaef863a1fa05e8a5d3b46fca9659c61f6b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74110616"
---
# <a name="bing-web-search-api-response-structure-and-answer-types"></a>Struktura odpovědí rozhraní API pro vyhledávání na webu Bingu a typy odpovědí  

Při odeslání požadavku hledání na webu Bingvrátí [`SearchResponse`](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#searchresponse) objekt v těle odpovědi. Objekt obsahuje pole pro každou odpověď, která Bing určena byla relevantní pro dotaz. Tento příklad ilustruje objekt odpovědi, pokud Bing vrátil všechny odpovědi:

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

Hledání na webu Bing obvykle vrátí podmnožinu odpovědí. Pokud například termín dotazu byl *plachtění čluny*, `webPages` `images`odpověď `rankingResponse`může zahrnovat , a . Pokud jste k odfiltrování webových stránek nepoužili [responseFilter,](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#responsefilter) odpověď vždy obsahuje odpovědi `webpages` a. `rankingResponse`

[!INCLUDE [cognitive-services-bing-url-note](../../../includes/cognitive-services-bing-url-note.md)]

## <a name="webpages-answer"></a>Odpověď webových stránek

Odpověď [webových stránek](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webanswer) obsahuje seznam odkazů na webové stránky, které bylo určeno pro vyhledávání na webu Bing, které byly relevantní pro dotaz. Každá [webová stránka](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webpage) v seznamu bude obsahovat: název stránky, adresu URL, zobrazovanou adresu URL, krátký popis obsahu a datum, kdy Bing obsah našel.

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

Použijte `name` `url` a vytvořte hypertextový odkaz, který uživatele přenese na webovou stránku.

<!-- Remove until this can be replaced with a sanitized version.
The following shows an example of how you might display the webpage in a search results page.

![Rendered webpage example](./media/cognitive-services-bing-web-api/bing-rendered-webpage-example.PNG)
-->

## <a name="images-answer"></a>Odpověď na obrázky

Odpověď [na obrázky](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) obsahuje seznam obrázků, o kterých si Bing myslel, že jsou relevantní pro dotaz. Každý [obrázek](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) v seznamu obsahuje adresu URL obrázku, jeho velikost, rozměry a jeho formát kódování. Objekt obrázku obsahuje také adresu URL miniatury obrázku a její rozměry.

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

V závislosti na zařízení uživatele obvykle zobrazíte podmnožinu miniatur s možností, aby uživatel [prostránkoval](paging-webpages.md) zbývající obrázky.

<!-- Remove until this can be replaced with a sanitized version.
![List of thumbnail images](./media/cognitive-services-bing-web-api/bing-web-image-thumbnails.PNG)
-->

Miniaturu můžete také zvětšit, když na ni uživatel najedete kurzorem. Pokud obrázek zvětšíte, nezapomeňte mu přiřadit atribut. Například extrahováním hostitele `hostPageDisplayUrl` z a jeho zobrazením pod obrázkem. Informace o změně velikosti miniatury najdete v tématu [Změna velikosti a oříznutí miniatur](./resize-and-crop-thumbnails.md).

<!-- Remove until this can be replaced with a sanitized version.
![Expanded view of thumbnail image](./media/cognitive-services-bing-web-api/bing-web-image-thumbnail-expansion.PNG)
-->

Pokud uživatel klikne na `webSearchUrl` miniaturu, použijte k přepnutí uživatele na stránku s výsledky vyhledávání bingu pro obrázky, která obsahuje koláž obrázků.

Podrobnosti o odpovědi na obrázek a obrázky naleznete v [tématu Image Search API](../bing-image-search/search-the-web.md).

## <a name="related-searches-answer"></a>Související hledání odpověď

[SouvisejícíHledání](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#searchresponse-relatedsearches) odpověď obsahuje seznam nejoblíbenějších souvisejících dotazů provedených jinými uživateli. Každý [dotaz](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query_obj) v seznamu obsahuje`text`řetězec dotazu ( ),`displayText`řetězec dotazu`webSearchUrl`se znaky zvýraznění přístupů ( ) a adresu URL ( ) na stránku s výsledky hledání bingu pro daný dotaz.

```json
{
    "text": "dinghy racing teams",
    "displayText": "dinghy racing teams",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=96C4CF214A0..."
}, ...
```

Pomocí `displayText` řetězce dotazu `webSearchUrl` a adresy URL vytvořte hypertextový odkaz, který uživatele přenese na stránku s výsledky hledání Bingu pro související dotaz. Můžete také použít `text` řetězec dotazu ve vlastním dotazu rozhraní API pro vyhledávání na webu a zobrazit výsledky sami.

Informace o tom, jak zpracovat `displayText`značky zvýraznění v tématu , naleznete v [tématu Zvýraznění přístupů](../bing-web-search/hit-highlighting.md).

Následující ukazuje příklad použití souvisejících dotazů v Bing.com.

![Příklad souvisejícího vyhledávání ve službě Bing](./media/cognitive-services-bing-web-api/bing-web-rendered-relatedsearches.GIF)

## <a name="videos-answer"></a>Odpověď na videa

Odpověď na [videa](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) obsahuje seznam videí, o kterých si Bing myslel, že jsou relevantní pro dotaz. Každé [video](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video) v seznamu obsahuje adresu URL videa, jeho trvání, rozměry a formát kódování. Objekt videa obsahuje také adresu URL miniatury videa a její rozměry.

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

V závislosti na zařízení uživatele obvykle zobrazíte podmnožinu videí s možností, aby uživatel zobrazil zbývající videa. Zobrazte miniaturu videa s délkou, popisem (názvem) a přiřazením (vydavatelem).

<!-- Remove until this can be replaced with a sanitized version.
![List of video thumbnails](./media/cognitive-services-bing-web-api/bing-web-video-thumbnails.PNG)
-->

Jak uživatel najedou nad miniaturou, můžete přehrát `motionThumbnailUrl` miniaturu videa. Ujistěte se, že je při zobrazení přiřazená filmová miniatura.

<!-- Remove until this can be replaced with a sanitized version.
![Motion thumbnail of a video](./media/cognitive-services-bing-web-api/bing-web-video-motion-thumbnail.PNG)
-->

Pokud uživatel klikne na miniaturu, máte následující možnosti pro zobrazení videa:

- Slouží `hostPageUrl` k zobrazení videa na hostitelském webu (například YouTube).
- Použití `webSearchUrl` k zobrazení videa v prohlížeči videa Bing
- Použití `embedHtml` k vložení videa do vlastní zkušenosti

Podrobnosti o odpovědi na video a videích najdete v [tématu Rozhraní API pro vyhledávání videí](../bing-video-search/search-the-web.md).

## <a name="news-answer"></a>Odpověď na novinky

Zpráva [news](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#news) odpověď obsahuje seznam zpravodajských článků, které Bing myslel, že jsou relevantní pro dotaz. Každý [zpravodajský článek](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) v seznamu obsahuje název, popis a adresu URL článku na webu hostitele. Pokud článek obsahuje obrázek, zahrnuje objekt miniaturu obrázku.

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

V závislosti na zařízení uživatele byste zobrazili podmnožinu zpravodajských článků s možností, aby uživatel zobrazil zbývající články. Pomocí `name` a `url` vytvořte hypertextový odkaz, který uživatele přenese na zpravodajský článek na webu hostitele. Pokud článek obsahuje obrázek, aby obraz `url`klikatelný pomocí . Nezapomeňte uvést zdroj článku pomocí `provider`.

<!-- Remove until this can be replaced with a sanitized version.
The following shows an example of how you might display articles in a search results page.

![List of news articles](./media/cognitive-services-bing-web-api/bing-web-news-list.PNG)
-->

Podrobnosti o odpovědích na zprávy a diskusních článcích naleznete v [tématu Rozhraní API pro vyhledávání zpráv](../bing-news-search/search-the-web.md).

## <a name="computation-answer"></a>Odpověď na výpočetní náhon

Pokud uživatel zadá matematický výraz nebo dotaz na převod jednotek, odpověď může obsahovat odpověď [výpočtu.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#computation) Odpověď `computation` obsahuje normalizovaný výraz a jeho výsledek.

Dotaz převodu jednotek je dotaz, který převádí jednu jednotku na jinou. Například, *Kolik stop v 10 metrech?* nebo Kolik *polévkových lžic v 1/4 šálku?*

Následující ukazuje `computation` odpověď na *Kolik stop v 10 metrů?*

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "10 meters",
    "value": "32.808399 feet"
}, ...
```

V následujícím textu jsou uvedeny příklady matematických dotazů a jejich odpovídající `computation` odpovědi.

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
|+|Sčítání|
|-|Odčítání|
|/|Dělení|
|*|Násobení|
|^|Napájení|
|!|Faktoriál|
|.|Desetinné číslo|
|()|Seskupení priorit|
|[]|Funkce|

Matematický výraz může obsahovat následující konstanty:

|Symbol|Popis|
|------------|-----------------|
|Pi|3.14159...|
|Stupeň|Stupeň|
|Mohu|Imaginární číslo|
|e|e, 2.71828...|
|GoldenRatio|Zlatý poměr, 1,61803...|

Matematický výraz může obsahovat následující funkce:

|Symbol|Popis|
|------------|-----------------|
|Seřadit|Odmocnina|
|Hřích[x], Cos[x], Tan[x]<br />Csc[x], Sec[x], Dětská kopačka[x]|Goniometrické funkce (s argumenty v radiánech)|
|ArcSin[x], ArcCos[x], ArcTan[x]<br />ArcCsc[x], ArcSec[x], ArcCot[x]|Inverzní goniometrické funkce (což dává výsledky v radiánech)|
|Exp[x], E^x|Exponenciální funkce|
|Protokol[x]|Přirozený logaritmus|
|Sinh[x], Cosh[x], Tanh[x]<br />Csch[x], Sech[x], Coth[x]|Hyperbolické funkce|
|ArcSinh[x], ArcCosh[x], ArcTanh[x]<br />ArcCsch[x], ArcSech[x], ArcCoth[x]|Inverzní hyperbolické funkce|

Matematické výrazy, které obsahují proměnné (například 4x+6=18, kde x je proměnná), nejsou podporovány.

## <a name="timezone-answer"></a>Odpověď časového pásma

Pokud uživatel zadá dotaz na čas nebo datum, odpověď může obsahovat odpověď [timezone.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#timezone) Tato odpověď podporuje implicitní nebo explicitní dotazy. Implicitní dotaz, například *Jaký čas je?*, vrátí místní čas na základě umístění uživatele. Explicitní dotaz, například *Kolik je v Seattlu?*, vrátí místní čas pro Seattle, WA.

Odpověď `timeZone` poskytuje název umístění, aktuální datum a čas utc v zadaném umístění a posun UTC. Pokud je hranice umístění v rámci více časových pásem, odpověď obsahuje aktuální datum utc a čas všech časových pásem v rámci hranice. Například protože Florida State spadá do dvou časových pásem, odpověď obsahuje místní datum a čas obou časových pásem.  

Pokud dotaz požaduje čas státu nebo země nebo oblasti, Bing určí primární město v rámci zeměpisné hranice `primaryCityTime` umístění a vrátí jej v poli. Pokud hranice obsahuje více časových pásem, zbývající časová pásma jsou vráceny `otherCityTimes` v poli.

Následující ukazuje příklad dotazy, `timeZone` které vracejí odpověď.

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

## <a name="spellsuggestion-answer"></a>Odpověď SpellSuggestion

Pokud Bing zjistí, že uživatel má v úmyslu vyhledat něco jiného, odpověď obsahuje [SpellSuggestions](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#spellsuggestions) objekt. Například pokud uživatel hledá *carlos pero*, Bing může určit, že uživatel pravděpodobně zamýšlel hledat Carlos Pena místo (na základě minulých vyhledávání jinými *carlos pera*). Následující text ukazuje příklad odpovědi na kouzlo.

```json
"spellSuggestions": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#SpellSuggestions",
    "value": [{
        "text": "carlos pena",
        "displayText": "carlos pena"
    }]
}, ...
```

## <a name="response-headers"></a>Hlavičky odpovědi

Odpovědi z rozhraní API pro vyhledávání na webu Bingu mohou obsahovat následující záhlaví:

|||
|-|-|
|`X-MSEdge-ClientID`|Jedinečné ID, které bing přiřadil uživateli|
|`BingAPIs-Market`|Trh, který byl použit ke splnění požadavku|
|`BingAPIs-TraceId`|Položka protokolu na serveru rozhraní API bingu pro tento požadavek (pro podporu)|

Je obzvláště důležité zachovat ID klienta a vrátit jej s následnými požadavky. Když toto provedete, hledání bude používat minulý kontext v pořadí výsledků hledání a také poskytují konzistentní uživatelské prostředí.

Pokud však zavoláte rozhraní API pro vyhledávání na webu Bing z JavaScriptu, mohou vám integrované funkce zabezpečení (CORS) v prohlížeči zabránit v přístupu k hodnotám těchto záhlaví.

Chcete-li získat přístup k záhlavím, můžete vytvořit požadavek rozhraní API pro vyhledávání na webu Bingprostřed proxy serveru CORS. Odpověď z takového proxy serveru má hlavičku `Access-Control-Expose-Headers`, která přidává hlavičky odpovědí na seznam povolených a zpřístupňuje je pro JavaScript.

Je snadné nainstalovat proxy CORS, aby naše [výukové aplikace](tutorial-bing-web-search-single-page-app.md) pro přístup k volitelným záhlavím klienta. Nejdřív [nainstalujte Node.js](https://nodejs.org/en/download/), pokud jste to ještě neudělali. Poté zadejte následující příkaz na příkazovém řádku.

    npm install -g cors-proxy-server

Dále změňte koncový bod rozhraní API pro vyhledávání na webu Bingu v souboru HTML takto:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

Nakonec spusťte proxy server CORS pomocí tohoto příkazu:

    cors-proxy-server

Při používání ukázkové aplikace nechte příkazové okno otevřené. Zavřením okna se zastaví proxy server. V rozbalitelné sekci hlaviček HTTP pod výsledky hledání teď uvidíte hlavičku `X-MSEdge-ClientID` (mimo jiné) a můžete zkontrolovat, jestli je stejná pro každý požadavek.

## <a name="response-headers-in-production"></a>Hlavičky odpovědí v produkčním prostředí

Cors proxy přístup popsaný v předchozí odpovědi je vhodný pro vývoj, testování a učení.

V produkčním prostředí byste měli hostovat skript na straně serveru ve stejné doméně jako webová stránka, která používá rozhraní API pro vyhledávání na webu Bingu. Tento skript by měl provádět volání rozhraní API na žádost z webové stránky JavaScript a předat všechny výsledky, včetně záhlaví, zpět klientovi. Vzhledem k tomu, že dva prostředky (stránka a skript) sdílejí původ, CORS se nepoužívá a speciální záhlaví jsou přístupná javascriptu na webové stránce.

Tento přístup také chrání klíč rozhraní API před vystavením veřejnosti, protože jej potřebuje pouze skript na straně serveru. Skript můžete použít jinou metodu, aby se ujistil, že požadavek je autorizován.

Následující text ukazuje, jak Bing používá návrh pravopisu.

![Příklad návrhu pravopisu bingu](./media/cognitive-services-bing-web-api/bing-web-spellingsuggestion.GIF)  

## <a name="next-steps"></a>Další kroky  

* Zkontrolujte dokumentaci [k omezení požadavku.](throttling-requests.md)  

## <a name="see-also"></a>Viz také  

* [Odkaz na rozhraní API webového vyhledávání Bingu](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)
