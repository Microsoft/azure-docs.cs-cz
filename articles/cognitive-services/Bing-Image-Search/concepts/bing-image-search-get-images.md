---
title: Získání obrázků z webu – rozhraní API pro vyhledávání obrázků bingu
titleSuffix: Azure Cognitive Services
description: Pomocí rozhraní API pro vyhledávání obrázků Bingu můžete vyhledávat a získat relevantní obrázky z webu.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: AB1B9898-C94A-4B59-91A1-8623C94BA3D4
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 309bbca762149f8804742d9ef02d4c3e8dfcdc6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67542767"
---
# <a name="get-images-from-the-web-with-the-bing-image-search-api"></a>Získání obrázků z webu pomocí rozhraní API pro vyhledávání obrázků Bingem

Při použití rozhraní REST API pro vyhledávání obrázků bingu můžete získat obrázky z webu, které souvisejí s hledaným výrazem, odesláním následující žádosti GET:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Použijte parametr [dotazu q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query) pro hledaný termín kódovaný adresou URL. Pokud například zadáte *plachetní čluny* `sailing+dinghies` , `sailing%20dinghies`nastavte `q` na nebo .

> [!IMPORTANT]
> * Všechny požadavky musí být provedeny ze serveru a nikoli z klienta.
> * Pokud voláte poprvé z vyhledávacích api Bingu, nezahrnujte záhlaví ID klienta. ID klienta zahrňte pouze v případě, že jste dříve volali rozhraní API Bingu, které vrátilo ID klienta pro kombinaci uživatele a zařízení.

## <a name="get-images-from-a-specific-web-domain"></a>Získání obrázků z určité webové domény

Pokud chcete získat obrázky z konkrétní domény, použijte operátor dotazu [site:](https://msdn.microsoft.com/library/ff795613.aspx).

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

> [!NOTE]
> Odpovědi na dotazy pomocí `site:` operátoru mohou obsahovat obsah pouze pro dospělé bez ohledu na nastavení [bezpečného vyhledávání.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#safesearch) Používejte `site:` pouze v případě, že jste si vědomi obsahu v doméně.

## <a name="filter-images"></a>Filtrování obrazů

 Ve výchozím nastavení vrátí rozhraní API pro vyhledávání obrázků všechny obrázky, které jsou relevantní pro dotaz. Pokud chcete filtrovat obrázky, které bing vrací (například chcete-li vrátit pouze obrázky s průhledným pozadím nebo určitou velikostí), použijte následující parametry dotazu:

* [aspekt](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#aspect)– Filtruje obrazy podle poměru stran (například standardní nebo širokoúhlé obrázky).
* [color](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#color)— Filtrujte obrazy dominantní barvou nebo černobíle.
* [svěžest](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#freshness)– Filtruje obrázky podle věku (například obrázky objevené bingem v minulém týdnu).
* [výška](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#height), [šířka](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#width)– Filtrujte obrazy podle šířky a výšky.
* [imageContent](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagecontent)– Filtruje obrázky podle obsahu (například obrázky, které zobrazují pouze obličej osoby).
* [imageTyp](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype)– Filtruje obrázky podle textu (například klipart, animované gify nebo průhledná pozadí).
* [licence](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#license)– Filtrujte obrázky podle typu licence přidružené k webu.
* [velikost](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#size)– Vyfiltrujte obrazy podle velikosti, například malé obrazy až do 200 x 200 pixelů.

Pokud chcete získat obrázky z konkrétní domény, použijte operátor dotazu [site:](https://msdn.microsoft.com/library/ff795613.aspx).

Následující příklad ukazuje, jak získat malé obrázky z ContosoSailing.com, které Bing objevil v minulém týdnu.  

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&size=small&freshness=week&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="bing-image-search-response-format"></a>Formát odpovědi na vyhledávání obrázků bingu

Zpráva odpovědi z Bing obsahuje [obrázky](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) odpověď, která obsahuje seznam obrázků, které Cognitive Services zjištěno, že relevantní pro dotaz. Každý objekt [Obrázku](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) v seznamu obsahuje následující informace o obrázku: url, jeho velikost, rozměry, jeho formát kódování, URL miniatury obrázku a rozměry miniatury.

> [!NOTE]
> * Obrázky musí být zobrazeny v pořadí uvedeném v odpovědi.
> * Vzhledem k tomu, že formáty a parametry adres URL se mohou změnit bez předchozího upozornění, používejte všechny adresy URL tak, jak jsou. Neměli byste přijímat závislosti na formátu adresy URL nebo parametry, pokud není uvedeno jinak.

```json
{
    "name": "Rich Passage Sailing Dinghy",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=73118C8B4E3...",
    "thumbnailUrl": "https:\/\/tse1.mm.bing.net\/th?id=OIP.GNarK7m...",
    "datePublished": "2011-10-29T11:26:00",
    "contentUrl": "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3...",
    "hostPageUrl": "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3687...",
    "contentSize": "79239 B",
    "encodingFormat": "jpeg",
    "hostPageDisplayUrl": "en.contoso.org\/wiki\/File:Rich_Passage...",
    "width": 526,
    "height": 688,
    "thumbnail": {
        "width": 229,
        "height": 300
    },
    "imageInsightsToken": "ccid_GNarK7ma*mid_CCF85447ADA6...",
    "insightsSourcesSummary": {
        "shoppingSourcesCount": 0,
        "recipeSourcesCount": 0
    },
    "imageId": "CCF85447ADA6FFF9E96E7DF0B796F7A86E34593",
    "accentColor": "376094"
},
```

Když zavoláte rozhraní API Bingu pro vyhledávání obrázků, Bing vrátí seznam výsledků. Tento seznam je podmnožinou celkového počtu výsledků, které jsou pro dotaz relevantní. Pole `totalEstimatedMatches` v odpovědi obsahuje odhadovaný počet obrázků, které je možné zobrazit. Podrobnosti o tom, jak procházet ostatními obrázky, naleznete v [tématu Paging Images](../paging-images.md).

## <a name="next-steps"></a>Další kroky

Pokud jste rozhraní API pro vyhledávání obrázků bingu ještě nevyzkoušeli, zkuste [rychlý start](../quickstarts/csharp.md). Pokud hledáte něco složitějšího, zkuste v kurzu vytvořit [jednostránkovou webovou aplikaci](../tutorial-bing-image-search-single-page-app.md).
