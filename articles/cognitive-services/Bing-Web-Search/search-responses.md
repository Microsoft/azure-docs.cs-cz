---
title: rozhraní API Bingu pro vyhledávání na webu struktury odpovědí a typů odpovědí
titleSuffix: Azure Cognitive Services
description: Když odešlete Vyhledávání na webu Bingu požadavek hledání, vrátí `SearchResponse` objekt v těle odpovědi.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 2cea88c2e20c9e96c5ad5504815886b2cc771e44
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100554"
---
# <a name="bing-web-search-api-response-structure-and-answer-types"></a>rozhraní API Bingu pro vyhledávání na webu struktury odpovědí a typů odpovědí  

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](https://aka.ms/cogsvcs/bingmove)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](https://aka.ms/cogsvcs/bingmigration).

Když odešlete Vyhledávání na webu Bingu požadavek hledání, vrátí [`SearchResponse`](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#searchresponse) objekt v těle odpovědi. Objekt obsahuje pole pro každou odpověď, kterou Bing určil pro dotazování. Tento příklad ukazuje objekt odpovědi, pokud Bing vrátil všechny odpovědi:

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

Vyhledávání na webu Bingu obvykle vrací podmnožinu odpovědí. Pokud se například termín dotazu *dodinghies* , může odpověď zahrnovat `webPages` , `images` a `rankingResponse` . Pokud jste nepoužívali [responseFilter](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#responsefilter) k odfiltrování webových stránek, odpověď vždy obsahuje `webpages` odpovědi a `rankingResponse` .

[!INCLUDE [cognitive-services-bing-url-note](../../../includes/cognitive-services-bing-url-note.md)]

## <a name="webpages-answer"></a>Odpověď webových stránek

Odpověď na [webové stránky](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webanswer) obsahuje seznam odkazů na webové stránky, které vyhledávání na webu Bingu určené pro dotaz relevantní. Každá [Webová stránka](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webpage) v seznamu bude obsahovat: název stránky, adresu URL, ZOBRAZOVANOU adresu URL, krátký popis obsahu a datum, kde Bing nalezl obsah.

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

Pomocí `name` a `url` můžete vytvořit hypertextový odkaz, který převezme uživatele na webovou stránku.

<!-- Remove until this can be replaced with a sanitized version.
The following shows an example of how you might display the webpage in a search results page.

![Rendered webpage example](./media/cognitive-services-bing-web-api/bing-rendered-webpage-example.PNG)
-->

## <a name="images-answer"></a>Odpovědi na obrázky

Odpověď na [obrázky](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) obsahuje seznam imagí, které Bing myslel jako relevantní pro dotaz. Každý [Obrázek](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) v seznamu obsahuje adresu URL obrázku, jeho velikost, rozměry a formát kódování. Objekt obrázku obsahuje také adresu URL miniatury obrázku a její rozměry.

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

V závislosti na zařízení uživatele by se obvykle zobrazila podmnožina miniatur s možností stránky pro uživatele [prostřednictvím](paging-webpages.md) zbývajících imagí.

<!-- Remove until this can be replaced with a sanitized version.
![List of thumbnail images](./media/cognitive-services-bing-web-api/bing-web-image-thumbnails.PNG)
-->

Miniaturu můžete také zvětšit, když na ni uživatel najedete kurzorem. Pokud obrázek zvětšíte, nezapomeňte mu přiřadit atribut. Například extrakcí hostitele z `hostPageDisplayUrl` a jeho zobrazení pod obrázkem. Informace o změně velikosti miniatury najdete v tématu [Změna velikosti a oříznutí miniatur](./resize-and-crop-thumbnails.md).

<!-- Remove until this can be replaced with a sanitized version.
![Expanded view of thumbnail image](./media/cognitive-services-bing-web-api/bing-web-image-thumbnail-expansion.PNG)
-->

Pokud uživatel klikne na miniaturu, použijte `webSearchUrl` k převzetí uživatele na stránku výsledků hledání Bingu pro obrázky, které obsahují směs imagí.

Podrobnosti o odpovědích a obrázcích imagí najdete v tématu [vyhledávání obrázků API](../bing-image-search/search-the-web.md).

## <a name="related-searches-answer"></a>Odpověď související s vyhledáváním

[RelatedSearches](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#searchresponse-relatedsearches) odpověď obsahuje seznam nejoblíbenějších souvisejících dotazů, které udělali jiní uživatelé. Každý [dotaz](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query_obj) v seznamu obsahuje řetězec dotazu ( `text` ), řetězec dotazu se znaky zvýrazňování `displayText` hledaného znaku () a adresu URL ( `webSearchUrl` ) na stránku výsledků hledání ve službě Bing pro daný dotaz.

```json
{
    "text": "dinghy racing teams",
    "displayText": "dinghy racing teams",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=96C4CF214A0..."
}, ...
```

Použijte `displayText` řetězec dotazu a `webSearchUrl` adresu URL k vytvoření hypertextového odkazu, který uživateli přiřadí na stránku výsledků hledání Bingu pro související dotaz. Můžete také použít `text` řetězec dotazu ve vlastním vyhledávání na webu dotaz rozhraní API a zobrazit výsledky sami.

Informace o tom, jak zpracovávat značky zvýraznění v nástroji `displayText` , naleznete v tématu [zvýrazňování přístupů](../bing-web-search/hit-highlighting.md).

V následujícím příkladu vidíte příklad použití souvisejících dotazů v Bing.com.

![Příklad souvisejících hledání v Bingu](./media/cognitive-services-bing-web-api/bing-web-rendered-relatedsearches.GIF)

## <a name="videos-answer"></a>Odpověď na videa

Odpověď na [videa](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) obsahuje seznam videí, o kterých bylo pro dotaz relevantní Bing. Každé [video](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video) v seznamu obsahuje adresu URL videa, jeho trvání, jeho rozměry a formát kódování. Objekt videa obsahuje také adresu URL miniatury videa a její rozměry.

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

V závislosti na zařízení uživatele byste obvykle zobrazili podmnožinu videí s možností, jak může uživatel zobrazit zbývající videa. Zobrazí se miniatura videa s délkou, popisem (názvem) a autorem (vydavatel).

<!-- Remove until this can be replaced with a sanitized version.
![List of video thumbnails](./media/cognitive-services-bing-web-api/bing-web-video-thumbnails.PNG)
-->

Když uživatel najede myší na miniaturu, můžete použít `motionThumbnailUrl` k přehrání miniatury verze videa. Ujistěte se, že je při zobrazení přiřazená filmová miniatura.

<!-- Remove until this can be replaced with a sanitized version.
![Motion thumbnail of a video](./media/cognitive-services-bing-web-api/bing-web-video-motion-thumbnail.PNG)
-->

Pokud uživatel klikne na miniaturu, máte následující možnosti pro zobrazení videa:

- Použijte `hostPageUrl` k zobrazení videa na webu hostitele (například YouTube).
- Slouží `webSearchUrl` k zobrazení videa v prohlížeči videí Bingu.
- Použití `embedHtml` pro vložení videa ve vlastním prostředí

Podrobnosti o odpovědích a videích videa najdete v tématu [vyhledávání videí API](../bing-video-search/search-the-web.md).

## <a name="news-answer"></a>Odpověď na zprávy

Odpověď na [zprávy](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#news) obsahuje seznam článků s novinkami, které Bing myslelo jako relevantní pro daný dotaz. Každý [zpravodajský článek](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) v seznamu obsahuje název, popis a adresu URL článku na webu hostitele. Pokud článek obsahuje obrázek, zahrnuje objekt miniaturu obrázku.

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

V závislosti na zařízení uživatele by se vám zobrazila podmnožina novinek s možností, jak si uživatel zobrazit zbývající články. Pomocí `name` a `url` vytvořte hypertextový odkaz, který uživatele přenese na zpravodajský článek na webu hostitele. Pokud článek obsahuje obrázek, vytvářený obrázek je na něj použit `url` . Nezapomeňte uvést zdroj článku pomocí `provider`.

<!-- Remove until this can be replaced with a sanitized version.
The following shows an example of how you might display articles in a search results page.

![List of news articles](./media/cognitive-services-bing-web-api/bing-web-news-list.PNG)
-->

Podrobnosti o odpovědích a novinkách zpráv najdete v tématu [vyhledávání zpráv API](../bing-news-search/search-the-web.md).

## <a name="computation-answer"></a>Odpověď výpočtu

Pokud uživatel zadá matematický výraz nebo dotaz na převod jednotek, odpověď může obsahovat odpověď [výpočtu](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#computation) . `computation`Odpověď obsahuje normalizovaný výraz a jeho výsledek.

Dotaz na převod jednotek je dotaz, který převede jednu jednotku na jinou. Například *kolik metrů v 10 metrech?* nebo *kolik Tablespoons ve 1/4* .

Následující příklad zobrazuje `computation` odpověď na počet *metrů v 10 metrech?*

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "10 meters",
    "value": "32.808399 feet"
}, ...
```

Níže jsou uvedeny příklady matematických dotazů a jejich odpovídajících `computation` odpovědí.

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
|/|Oddělení|
|*|Násobení|
|^|Napájení|
|!|Faktoriál|
|.|Decimal|
|()|Seskupování priorit|
|[]|Function|

Matematický výraz může obsahovat následující konstanty:

|Symbol|Description|
|------------|-----------------|
|Pi|3,14159...|
|Chýlení|Chýlení|
|Mohu|Imaginární číslo|
|e|e, 2,71828...|
|GoldenRatio|Zlatý poměr, 1,61803...|

Matematický výraz může obsahovat následující funkce:

|Symbol|Description|
|------------|-----------------|
|Seřadit|Druhá odmocnina|
|Sin [x]; cos [x]; Tan [x]<br />CSc [x], SEK [x], COT [x]|Trigonometrické funkce (s argumenty v radiánech)|
|ArcSin [x]; ArcCos [x]; ArcTan [x]<br />ArcCsc [x]; ArcSec [x]; ArcCot [x]|Inverzní trigonometrické funkce (poskytující výsledky v radiánech)|
|EXP [x], E ^ x|Exponenciální funkce|
|Protokol [x]|Přirozený logaritmus|
|Sinh – [x]; COSH – [x]; tanh – [x]<br />Csch [x]; sech [x]; coth [x]|Hyperbolický funkce|
|ArcSinh [x]; ArcCosh [x]; ArcTanh [x]<br />ArcCsch [x]; ArcSech [x]; ArcCoth [x]|Inverzní hyperbolický funkce|

Matematické výrazy, které obsahují proměnné (například 4x + 6 = 18, kde x je proměnná), nejsou podporovány.

## <a name="timezone-answer"></a>Odpověď časového pásma

Pokud uživatel zadá dotaz na čas nebo datum, odpověď může obsahovat odpověď [časového pásma](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#timezone) . Tato odpověď podporuje implicitní nebo explicitní dotazy. Implicitní dotaz, jako je například *čas?* , vrátí místní čas na základě umístění uživatele. Explicitní dotaz, jako je například *čas v Seattlu?* , vrátí místní čas pro Seattle, WA.

`timeZone`Odpověď poskytuje název umístění, aktuální datum a čas UTC v zadaném umístění a posun UTC. Pokud se hranice umístění nachází v několika časových pásmech, odpověď obsahuje aktuální datum a čas UTC všech časových pásem v rámci hranice. Například vzhledem k tomu, že stav Florida spadá do dvou časových pásem, odpověď obsahuje místní datum a čas obou časových pásem.  

Pokud dotaz požádá o čas stavu nebo země nebo oblasti, Bing určí primární město v rámci geografické hranice umístění a vrátí jej v `primaryCityTime` poli. Pokud hranice obsahuje více časových pásem, zbývající časová pásma se vrátí do `otherCityTimes` pole.

Následující příklad ukazuje příklady dotazů, které vracejí `timeZone` odpověď.

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

## <a name="spellsuggestion-answer"></a>SpellSuggestion odpověď

Pokud Bing určí, že uživatel může chtít hledat něco jiného, odpověď zahrnuje objekt [SpellSuggestions](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#spellsuggestions) . Pokud uživatel například vyhledá *Carlos pero* , Bing může určit, že uživatel pravděpodobně hledal místo toho, aby Carlos Pena (na základě předchozích hledání ostatních *carlosho pera* ). Následuje ukázka reakce na kontrolu pravopisu.

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

Odpovědi z rozhraní API Bingu pro vyhledávání na webu mohou obsahovat následující hlavičky:

| Záhlaví | Description |
|-|-|
|`X-MSEdge-ClientID`|Jedinečné ID, které Bingu přiřadilo uživateli|
|`BingAPIs-Market`|Trh, který se použil ke splnění žádosti|
|`BingAPIs-TraceId`|Položka protokolu serveru rozhraní API Bingu pro tuto žádost (pro podporu)|

Je obzvláště důležité zachovat ID klienta a vrátit ho s následnými požadavky. Když to uděláte, bude hledání používat minulý kontext v rámci řazení výsledků hledání a zároveň poskytuje konzistentní uživatelské prostředí.

Když však zavoláte rozhraní API Bingu pro vyhledávání na webu z JavaScriptu, můžou vám integrované funkce zabezpečení (CORS) v prohlížeči bránit v přístupu k hodnotám těchto hlaviček.

Pokud chcete získat přístup k hlavičkám, můžete žádost o rozhraní API Bingu pro vyhledávání na webu vytvořit prostřednictvím serveru proxy CORS. Odpověď z takového serveru proxy má `Access-Control-Expose-Headers` záhlaví, které filtruje hlavičky odpovědí a zpřístupňuje je pro JavaScript.

Je snadné nainstalovat proxy CORS, aby mohla naše [aplikace](tutorial-bing-web-search-single-page-app.md) získat přístup k volitelným hlavičkám klienta. Nejdřív [nainstalujte Node.js](https://nodejs.org/en/download/), pokud jste to ještě neudělali. Pak na příkazovém řádku zadejte následující příkaz.

```console
npm install -g cors-proxy-server
```

Dále změňte koncový bod rozhraní API Bingu pro vyhledávání na webu v souboru HTML na: \
`http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search`

Nakonec spusťte proxy server CORS pomocí tohoto příkazu:

```console
cors-proxy-server
```

Při používání ukázkové aplikace nechte příkazové okno otevřené. Zavřením okna se zastaví proxy server. V rozbalitelné sekci hlaviček HTTP pod výsledky hledání teď uvidíte hlavičku `X-MSEdge-ClientID` (mimo jiné) a můžete zkontrolovat, jestli je stejná pro každý požadavek.

## <a name="response-headers-in-production"></a>Hlavičky odpovědí v produkčním prostředí

Přístup k proxy CORS, který je popsaný v předchozí odpovědi, je vhodný pro vývoj, testování a učení.

V produkčním prostředí byste měli hostovat skript na straně serveru ve stejné doméně jako webová stránka, která používá rozhraní API Bingu pro vyhledávání na webu. Tento skript by měl volat rozhraní API na vyžádání z webové stránky JavaScript a předat všem výsledkům, včetně hlaviček, zpátky do klienta. Vzhledem k tomu, že dva prostředky (stránka a skript) sdílí počátek, CORS se nepoužije a speciální hlavičky jsou přístupné pro JavaScript na webové stránce.

Tento přístup také chrání klíč rozhraní API před expozicí veřejnosti, protože ho potřebuje jenom skript na straně serveru. Skript může použít jinou metodu k ověření, zda je žádost autorizována.

Následující příklad ukazuje, jak Bing používá návrh pravopisu.

![Příklad návrhu pro kontrolu pravopisu Bingu](./media/cognitive-services-bing-web-api/bing-web-spellingsuggestion.GIF)  

## <a name="next-steps"></a>Další kroky  

* Projděte si dokumentaci [omezení žádostí](throttling-requests.md) .  

## <a name="see-also"></a>Viz také  

* [Odkaz na rozhraní API Bingu pro vyhledávání na webu](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)
