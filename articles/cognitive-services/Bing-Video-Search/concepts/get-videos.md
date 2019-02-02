---
title: Odesílání žádostí o hledání do API pro vyhledávání videí Bingu
titlesuffix: Azure Cognitive Services
description: Přečtěte si o odesílání vyhledávacích dotazů do API pro vyhledávání videí Bingu.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: overview
ms.date: 01/31/2019
ms.author: aahi
ms.openlocfilehash: dd085d51f1031bc6fb462292fb6d32f547f820d7
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2019
ms.locfileid: "55569490"
---
# <a name="search-for-videos-with-the-bing-video-search-api"></a>Hledání videí s API pro vyhledávání videí Bingu

API pro vyhledávání videí Bingu umožňuje snadno do svých aplikací integrovat funkce vyhledávání Bingu cognitive zprávy. Rozhraní API primárně najde a vrátí odpovídající videa z webu, poskytuje několik funkcí pro inteligentní a zaměřují se načtení videa na webu.

## <a name="getting-videos"></a>Získávání videí

Pokud chcete z webu získat videa související s hledaným termínem uživatele, odešlete následující požadavek GET:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Všechny požadavky se musí provádět ze serveru.

Pokud voláte některé z rozhraní API Bingu poprvé, nezahrnujte do volání hlavičku ID klienta. ID klienta zahrňte pouze v případě, že jste již dříve volali rozhraní API Bingu a Bing vrátil ID klienta pro příslušnou kombinaci uživatele a zařízení.

Pokud chcete získat videa z konkrétní domény, použijte operátor dotazu [site:](https://msdn.microsoft.com/library/ff795613.aspx).

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

Odpověď obsahuje objekt [Videos](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos) se seznamem videí, které Bing vyhodnotil jako relevantní pro daný dotaz. Každý objekt [Video](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video) v seznamu obsahuje mimo další atributy i adresu URL videa, dobu jeho trvání, rozměry a formát kódování. Objekt videa obsahuje také adresu URL miniatury videa a její rozměry.

```json
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545...",
    "totalEstimatedMatches" : 1000,
    "value" : [
        {
            "name" : "How to sail - What to Wear for Dinghy Sailing",
            "description" : "An informative video on what to wear when...",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7...",
            "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OVP.DYW...",
            "datePublished" : "2014-03-04T11:51:53",
            "publisher" : [
                {
                    "name" : "Fabrikam"
                }
            ],
            "creator" : 
            {
                "name" : "Marcus Appel"
            },
            "contentUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjZ--g",
            "hostPageUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D569...",
            "encodingFormat" : "h264",
            "hostPageDisplayUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjZ--g",
            "width" : 1280,
            "height" : 720,
            "duration" : "PT2M47S",
            "motionThumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?id=OM.Y62...",
            "embedHtml" : "<iframe width=\"1280\" height=\"720\" src=\"https:...><\/iframe>",
            "allowHttpsEmbed" : true,
            "viewCount" : 8743,
            "thumbnail" : 
            {
                "width" : 300,
                "height" : 168
            },
            "videoId" : "6DB795E11A6E3CBAAD636DB795E113CBAAD63",
            "allowMobileEmbed" : true,
            "isSuperfresh" : false
        },
        ...
    ],
    "queryExpansions" : [...],
    "nextOffsetAddCount" : 0,
    "pivotSuggestions" : [...]
}
```

## <a name="video-thumbnails"></a>Miniatury videa

Můžete zobrazit všechny, nebo podmnožinu miniatury videa vrácený Video API Bingu pro vyhledávání. Pokud zobrazíte podmnožinu, poskytněte uživateli možnost zobrazit zbývající videa. jako součást rozhraní API Bingu pro [použít a zobrazit požadavky](../UseAndDisplayRequirements.md), je třeba zobrazit videa v pořadí poskytnutém v odpovědi. Informace o změně velikosti miniatury najdete v tématu [Změna velikosti a oříznutí miniatur](../resize-and-crop-thumbnails.md). 

Pokud uživatel najede myší na miniaturu, může pomocí [motionThumbnailUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video-motionthumbnailurl) spustit přehrávání miniatury videa. Ujistěte se, že je při zobrazení přiřazená filmová miniatura.

<!-- Removing until the images can be sanitized.
![Motion thumbnail of a video](../bing-web-search/media/cognitive-services-bing-web-api/bing-web-video-motion-thumbnail.PNG)
-->

Po kliknutí na miniaturu existují tři možnosti pro zobrazení videa:

- Použitím adresy [hostPageUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video-hostpageurl) zobrazíte video na webu hostitele (například YouTube)
- Použitím adresy [webSearchUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video-websearchurl) zobrazíte video v prohlížeči videa Bingu
- Použitím adresy [embdedHtml](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video-embedhtml) můžete vložit video do vlastního prostředí 

Ujistěte se, že při přehrávání k videu přiřadíte atributy creator a publisher.

Podrobné informace o použití [videoId](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video-videoid) k získání přehledu o videu naleznete v tématu [Přehledy z videí](../video-insights.md).

## <a name="filtering-videos"></a>Filtrování videí

Ve výchozím nastavení vrací rozhraní API Bingu pro vyhledávání videí všechna videa, která jsou pro dotaz relevantní. Pokud chcete zobrazit pouze videa zdarma nebo videa kratší než pět minut, můžete použít následující parametry dotazu filtru:

- [pricing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#pricing) &mdash; filtrování videí podle ceny (například videa, která jsou zdarma nebo placená videa)
- [resolution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#resolution) &mdash; filtrování videí podle rozlišení (například videa s rozlišením 720p nebo vyšším rozlišením)
- [videoLength](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videolength) &mdash; filtrování videí podle jeho délky (například videa, která jsou kratší než pět minut)
- [freshness](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#freshness) &mdash; filtrování videí podle stáří (například videa nalezená Bingem během posledního týdne)

Pokud chcete získat videa z konkrétní domény, použijte v řetězci dotazu operátor [site:](https://msdn.microsoft.com/library/ff795613.aspx).

> [!NOTE]
> Pokud použijete operátor `site:`, v závislosti na dotazu existuje jistá pravděpodobnost, že odpověď bude obsahovat obsah pro dospělé, a to bez ohledu na nastavení [safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#safesearch). Operátor `site:` byste měli používat, pouze pokud znáte obsah příslušného webu a váš scénář podporuje možnost zobrazení obsahu pro dospělé.

Následující příklad ukazuje, jak získat bezplatná videa z webu ContosoSailing.com, která mají rozlišení obrazovky 720p nebo vyšší a Bing je našel během posledního měsíce.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies+site:contososailing.com&pricing=free&freshness=month&resolution=720p&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

## <a name="expanding-the-query"></a>Rozšíření dotazu

Pokud Bing dokáže rozšířením dotazu zúžit původní hledání, bude objekt [Videos](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos) obsahovat pole `queryExpansions`. Například, pokud se dotaz *čištění mezery*, rozšířené dotazy nesmusí být: Ovládací prvek čištění **nástroje**, čištění mezery **od základů**, čištění vazbu **počítače**, a **snadno** čištění ovládací prvek.

Následující příklad ukazuje rozšířené dotazy pro dotaz *čištění okapů*.

```json
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FBC5...",
    "totalEstimatedMatches" : 1000,
    "value" : [...],
    "nextOffsetAddCount" : 4,
    "queryExpansions" : [
        {
            "text" : "Gutter Cleaning Tools",
            "displayText" : "Tools",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FB....",
            "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v5...",
            "thumbnail" : {
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?q=Gutter..."
            }
        },
        ...
    ]
    "pivotSuggestions" : [...],
}
```

Pole `queryExpansions` obsahuje seznam objektů [Query](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#query_obj). Pole `text` obsahuje rozšířený dotaz a pole `displayText` obsahuje termín rozšíření. Pomocí polí text a miniatura můžete uživateli zobrazit rozšířené řetězce dotazů pro případ, že ve skutečnosti hledá právě rozšířený řetězec dotazu. Pomocí adresy URL `webSearchUrl` nebo `searchLink` můžete umožnit kliknutí na miniaturu a text. Pomocí hodnoty `webSearchUrl` můžete uživatele přesměrovat na výsledky hledání Bingu. Případně můžete použít hodnotu `searchLink`, pokud máte vlastní stránku výsledků.

## <a name="pivoting-the-query"></a>Rozdělení dotazu na pivoty

Pokud Bing dokáže segmentovat původní vyhledávací dotaz, bude objekt [Videos](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos) obsahovat pole `pivotSuggestions`. Pokud byl původní dotaz například *čištění okapů*, může ho Bing segmentovat na *čištění* a *okapů*.

Následující příklad ukazuje návrhy pivotů pro dotaz *čištění okapů*.

```json
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FBC...",
    "totalEstimatedMatches" : 1000,
    "value" : [...],
    "nextOffsetAddCount" : 0,
    "queryExpansions" : [...],
    "pivotSuggestions" : [
        {
            "pivot" : "cleaning",
            "suggestions" : [
                {
                    "text" : "Gutter Repair",
                    "displayText" : "Repair",
                    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52...",
                    "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v5\/videos...",
                    "thumbnail" : {
                        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=Gutter..."
                    }
                },
                ...
            ]
        },
        {
            "pivot" : "gutters",
            "suggestions" : [
                {
                    "text" : "Window Cleaning",
                    "displayText" : "Window",
                    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FBC59...",
                    "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v5...",
                    "thumbnail" : {
                        "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?q=Window..."
                    }
                },
                ...
            ]
        }
    ]
}
```

Pro každý pivot obsahuje odpověď seznam objektů [Query](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#query_obj), které obsahují navrhované dotazy. Pole `text` obsahuje navrhovaný dotaz a pole `displayText` obsahuje termín, který nahradí pivot v původním dotazu. Například čištění oken.

Pomocí polí `text` a `thumbnail` můžete uživateli zobrazit rozšířené řetězce dotazů pro případ, že ve skutečnosti hledá právě rozšířený řetězec dotazu. Pomocí adresy URL `webSearchUrl` nebo `searchLink` můžete umožnit kliknutí na miniaturu a text. Pomocí hodnoty `webSearchUrl` můžete uživatele přesměrovat na výsledky hledání Bingu. Případně můžete použít hodnotu `searchLink`, pokud máte vlastní stránku výsledků.

## <a name="throttling-requests"></a>Omezování požadavků

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]
