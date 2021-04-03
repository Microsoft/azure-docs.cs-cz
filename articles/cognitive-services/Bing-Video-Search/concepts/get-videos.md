---
title: Hledání videí pomocí rozhraní API Bingu pro vyhledávání videí
titleSuffix: Azure Cognitive Services
description: Vvyhledávání videí Bingu APIfinds a vrátí relevantní videa z webu, nabízí několik funkcí pro inteligentní a cílené načítání videa na webu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 10277efe1f06de3633b2d614e2ee5ec0cc351c76
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "96351925"
---
# <a name="search-for-videos-with-the-bing-video-search-api"></a>Hledání videí pomocí rozhraní API Bingu pro vyhledávání videí

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Rozhraní API Bingu pro vyhledávání videí usnadňuje integraci funkcí vyhledávání zpráv Bingu ve vašich aplikacích. I když rozhraní API primárně najde a vrátí relevantní videa z webu, nabízí několik funkcí pro inteligentní a cílené načítání videí na webu.

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

Pokud chcete získat videa z konkrétní domény, použijte operátor dotazu [site:](/previous-versions/bing/search/ff795613(v=msdn.10)).

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

Odpověď obsahuje objekt [Videos](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) se seznamem videí, které Bing vyhodnotil jako relevantní pro daný dotaz. Každý objekt [Video](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video) v seznamu obsahuje mimo další atributy i adresu URL videa, dobu jeho trvání, rozměry a formát kódování. Objekt videa obsahuje také adresu URL miniatury videa a její rozměry.

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

Můžete zobrazit všechny nebo podmnožinu miniatur videa vrácených rozhraní API Bingu pro vyhledávání videí. Pokud zobrazíte podmnožinu, poskytněte uživateli možnost zobrazit zbývající videa. v rámci [požadavků na použití a zobrazení](../../bing-web-search/use-display-requirements.md)rozhraní API Bingu je nutné zobrazit videa v uvedeném pořadí v odpovědi. Informace o změně velikosti miniatury najdete v tématu [Změna velikosti a oříznutí miniatur](../../bing-web-search/resize-and-crop-thumbnails.md). 

Pokud uživatel najede myší na miniaturu, může pomocí [motionThumbnailUrl](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-motionthumbnailurl) spustit přehrávání miniatury videa. Ujistěte se, že je při zobrazení přiřazená filmová miniatura.

<!-- Removing until the images can be sanitized.
![Motion thumbnail of a video](../bing-web-search/media/cognitive-services-bing-web-api/bing-web-video-motion-thumbnail.PNG)
-->

Při kliknutí na miniaturu jsou k dispozici tři možnosti zobrazení videa:

- Použitím adresy [hostPageUrl](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-hostpageurl) zobrazíte video na webu hostitele (například YouTube)
- Použitím adresy [webSearchUrl](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-websearchurl) zobrazíte video v prohlížeči videa Bingu
- Použitím adresy [embdedHtml](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-embedhtml) můžete vložit video do vlastního prostředí 

Ujistěte se, že při přehrávání k videu přiřadíte atributy creator a publisher.

Podrobné informace o použití [videoId](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-videoid) k získání přehledu o videu naleznete v tématu [Přehledy z videí](../video-insights.md).

## <a name="filtering-videos"></a>Filtrování videí

Ve výchozím nastavení vrací rozhraní API Bingu pro vyhledávání videí všechna videa, která jsou pro dotaz relevantní. Pokud chcete zobrazit pouze videa zdarma nebo videa kratší než pět minut, můžete použít následující parametry dotazu filtru:

- [ceny](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#pricing) &mdash; Filtrovat videa podle ceny (například videa, která jsou zdarma nebo které je třeba zaplatit)
- [řešení](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#resolution) &mdash; Filtrovat videa podle rozlišení (například videí s rozlišením 720p nebo vyšším)
- [videoLength](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videolength) &mdash; Filtrovat videa podle délky videa (například videa, která jsou delší než pět minut)
- [aktuálnost](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#freshness) &mdash; Filtrovat videa podle věku (například videa zjištěná bingem za minulý týden)

Pokud chcete získat videa z konkrétní domény, použijte v řetězci dotazu operátor [site:](/previous-versions/bing/search/ff795613(v=msdn.10)).

> [!NOTE]
> Pokud použijete operátor `site:`, v závislosti na dotazu existuje jistá pravděpodobnost, že odpověď bude obsahovat obsah pro dospělé, a to bez ohledu na nastavení [safeSearch](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#safesearch). Operátor `site:` byste měli používat, pouze pokud znáte obsah příslušného webu a váš scénář podporuje možnost zobrazení obsahu pro dospělé.

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

Pokud Bing dokáže rozšířením dotazu zúžit původní hledání, bude objekt [Videos](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) obsahovat pole `queryExpansions`. Například, pokud byl původní dotaz *čištění okapů*, rozšířené dotazy můžou být: **nástroje na** čištění okapů, čištění okapů **ze země**, **Stroj na** čištění okapů a **snadné** čištění okapů.

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

Pole `queryExpansions` obsahuje seznam objektů [Query](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#query_obj). Pole `text` obsahuje rozšířený dotaz a pole `displayText` obsahuje termín rozšíření. Pomocí polí text a miniatura můžete uživateli zobrazit rozšířené řetězce dotazů pro případ, že ve skutečnosti hledá právě rozšířený řetězec dotazu. Pomocí adresy URL `webSearchUrl` nebo `searchLink` můžete umožnit kliknutí na miniaturu a text. Pomocí hodnoty `webSearchUrl` můžete uživatele přesměrovat na výsledky hledání Bingu. Případně můžete použít hodnotu `searchLink`, pokud máte vlastní stránku výsledků.

## <a name="pivoting-the-query"></a>Rozdělení dotazu na pivoty

Pokud Bing dokáže segmentovat původní vyhledávací dotaz, bude objekt [Videos](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) obsahovat pole `pivotSuggestions`. Pokud byl původní dotaz například *čištění okapů*, může ho Bing segmentovat na *čištění* a *okapů*.

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

Pro každý pivot obsahuje odpověď seznam objektů [Query](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#query_obj), které obsahují navrhované dotazy. Pole `text` obsahuje navrhovaný dotaz a pole `displayText` obsahuje termín, který nahradí pivot v původním dotazu. Například čištění oken.

Pomocí polí `text` a `thumbnail` můžete uživateli zobrazit rozšířené řetězce dotazů pro případ, že ve skutečnosti hledá právě rozšířený řetězec dotazu. Pomocí adresy URL `webSearchUrl` nebo `searchLink` můžete umožnit kliknutí na miniaturu a text. Pomocí hodnoty `webSearchUrl` můžete uživatele přesměrovat na výsledky hledání Bingu. Případně můžete použít hodnotu `searchLink`, pokud máte vlastní stránku výsledků.

## <a name="throttling-requests"></a>Omezování požadavků

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]