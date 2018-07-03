---
title: Co je Vyhledávání obrázků Bingu? | Dokumenty Microsoft
description: Zjistěte, jak vyhledávat obrázky na webu pomocí rozhraní API Bingu pro vyhledávání obrázků.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 1446AD8B-A685-4F5F-B4AA-74C8E9A40BE9
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: overview
ms.date: 10/11/2017
ms.author: scottwhi
ms.openlocfilehash: 457707065059b5cb83c9d2b81f5d073cf1c89b84
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "36336515"
---
# <a name="what-is-bing-image-search"></a>Co je Vyhledávání obrázků Bingu?

Rozhraní API Bingu pro vyhledávání obrázků poskytuje podobné prostředí jako [Obrázky Bingu](https://www.bing.com/images), protože umožňuje odeslat vyhledávací dotaz uživatele do Bingu a získat zpět seznam relevantních obrázků.

Pokud vytváříte stránku výsledků hledání pouze s obrázky, na které chcete vyhledávat relevantní obrázky pro vyhledávací dotazy uživatelů, místo obecnějšího [rozhraní API Bingu pro vyhledávání na webu](../bing-web-search/search-the-web.md) volejte toto rozhraní API. Pokud chcete obrázky i další typy obsahu, jako jsou webové stránky, zprávy a videa, volejte rozhraní API Bingu pro vyhledávání na webu.

## <a name="suggesting--using-search-terms"></a>Navrhování a používání hledaných termínů

Pokud nabízíte vyhledávací pole, do kterého může uživatel zadat hledaný termín, můžete hledání vylepšit s využitím [rozhraní API pro automatické návrhy Bingu](../bing-autosuggest/get-suggested-search-terms.md). Toto rozhraní API vrací navrhované řetězce dotazů na základě částečné shody hledaných termínů zadávaných uživatelem.

Jakmile uživatel zadá hledaný termín, před nastavením parametru dotazu [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query) zakódujte termín s použitím kódování URL. Pokud uživatel například zadá *sailing dinghies*, nastavte parametr `q` na hodnotu `sailing+dinghies` nebo `sailing%20dinghies`.

## <a name="getting-images"></a>Získání obrázků

Pokud chcete z webu získat obrázky související s hledaným termínem uživatele, odešlete následující požadavek GET:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Všechny požadavky se musí provádět ze serveru. Z klienta není možné provádět volání.

Pokud voláte některé z rozhraní API Bingu poprvé, nezahrnujte do volání hlavičku ID klienta. ID klienta zahrňte pouze v případě, že jste již dříve volali rozhraní API Bingu a Bing vrátil ID klienta pro příslušnou kombinaci uživatele a zařízení.

Pokud chcete získat obrázky z konkrétní domény, použijte operátor dotazu [site:](http://msdn.microsoft.com/library/ff795613.aspx).

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

Odpověď obsahuje objekt [Images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) se seznamem obrázků, které Bing vyhodnotil jako relevantní pro daný dotaz. Každý objekt [Image](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image) v seznamu obsahuje adresu URL obrázku, jeho velikost, rozměry a formát kódování. Objekt obrázku obsahuje také adresu URL miniatury obrázku a její rozměry.

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

Můžete zobrazit koláž miniatur obrázků nebo jenom podmnožinu miniatur. Pokud zobrazíte podmnožinu, poskytněte uživateli možnost zobrazit zbývající obrázky. Obrázky musíte zobrazit v pořadí uvedeném v odpovědi.

Miniaturu můžete také zvětšit, když na ni uživatel najedete kurzorem. Pokud obrázek zvětšíte, nezapomeňte mu přiřadit atribut. Můžete například extrahovat název hostitele z hodnoty [hostPageDisplayUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-hostpagedisplayurl) a zobrazit ho pod obrázkem. Informace o změně velikosti miniatury najdete v tématu [Změna velikosti a oříznutí miniatur](./resize-and-crop-thumbnails.md).

<!-- Removing image until we can replace it with a sanatized version.
![Expanded view of thumbnail image](../bing-web-search/media/cognitive-services-bing-web-api/bing-web-image-thumbnail-expansion.PNG)
-->

Pokud uživatel na miniaturu klikne, můžete mu s použitím hodnoty [contentUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-contenturl) zobrazit obrázek v plné velikosti. Nezapomeňte obrázku přiřadit atribut.

Pokud je hodnota `shoppingSourcesCount` nebo `recipeSourcesCount` větší než 0, přidejte k miniatuře odznáček, například nákupního košíku, který bude značit, že pro objekt na obrázku je k dispozici možnost nákupu nebo zobrazení receptů.

<!-- this is a sanitized version but we're removing all images for now until everything is sanitized.
![Shopping sources badge](./media/cognitive-services-bing-images-api/bing-images-shopping-source.PNG)
-->

Pokud chcete získat informace o obrázku, například webové stránky, které obrázek obsahují, nebo osoby rozpoznané na obrázku, použijte hodnotu [imageInsightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-imageinsightstoken). Podrobnosti najdete v tématu [Přehledy obrázků](./image-insights.md).

## <a name="filtering-images"></a>Filtrování obrázků

 Ve výchozím nastavení vrací rozhraní API Bingu pro vyhledávání obrázků všechny obrázky, které jsou pro dotaz relevantní. Pokud však chcete pouze obrázky s průhledným pozadím nebo obrázky určité velikost, můžete obrázky vracené Bingem filtrovat pomocí následujících parametrů dotazu.  

- [aspect](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#aspect) – filtrování obrázků podle poměru stran (například standardní nebo širokoúhlé obrázky)
- [color](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#color) – filtrování obrázků podle dominantní barvy nebo černé a bílé
- [freshness](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#freshness) – filtrování obrázků podle stáří (například obrázky zjištěné Bingem během posledního týdne)
- [height](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#height), [width](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#width) – filtrování obrázků podle šířky a výšky
- [imageContent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagecontent) – filtrování obrázků podle obsahu (například obrázky jen s lidským obličejem)
- [imageType](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetype) – filtrování obrázků podle typu (například klipart, animovaný GIF nebo průhledné pozadí)
- [license](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#license) – filtrování obrázků podle typu licence přidružené k příslušnému webu
- [size](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#size) – filtrování obrázků podle velikosti (například malé obrázky do velikosti 200 × 200 pixelů)

Pokud chcete získat obrázky z konkrétní domény, použijte operátor dotazu [site:](http://msdn.microsoft.com/library/ff795613.aspx).

> [!NOTE]
> Pokud použijete operátor `site:`, v závislosti na dotazu existuje jistá pravděpodobnost, že odpověď bude obsahovat obsah pro dospělé, a to bez ohledu na nastavení [safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#safesearch). Operátor `site:` byste měli používat, pouze pokud znáte obsah příslušného webu a váš scénář podporuje možnost zobrazení obsahu pro dospělé.

Následující příklad ukazuje, jak získat malé obrázky z webu ContosoSailing.com zjištěné Bingem během posledního týdne.  

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&size=small&freshness=week&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="pivoting-the-query"></a>Rozdělení dotazu na pivoty

Pokud Bing dokáže segmentovat původní vyhledávací dotaz, bude objekt [Images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) obsahovat pole `pivotSuggestions`. Pokud byl původní dotaz například *Microsoft Surface*, může ho Bing segmentovat na *Microsoft* a *Surface*.  

Následující příklad ukazuje návrhy pivotů pro dotaz *Microsoft Surface*.  

```json
{
    "_type": "Images",
    "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=microsoft%20surface&FORM=OIIARP",
    "totalEstimatedMatches": 1000,
    "value": [...],
    "queryExpansions": [...],
    "pivotSuggestions": [{
        "pivot": "microsoft",
        "suggestions": [{
            "text": "Contoso Surface",
            "displayText": "Contoso",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=OtterBox+Surface&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=Contoso...",
                    "searchLink": "https:\/\/api.cognitive.microsoft.com\/api...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Contoso+Surface..."
            }
        },
        {
            "text": "Adatum Surface",
            "displayText": "Adatum",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Adatum+Surface&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Adatum+Surface&pid=Ap..."
            }
        },
        ...
        ]
    },
    {
        "pivot": "surface",
        "suggestions": [{
            "text": "Microsoft Surface4",
            "displayText": "Surface4",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface...",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft..."
            }
        },
        {
            "text": "Microsoft Tablet",
            "displayText": "Tablet",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Tablet&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Microsoft+Tablet..."
            }
        },
        ...
    ],
    "nextOffsetAddCount": 0
}
```

Uživateli pak můžete zobrazit volitelné termíny dotazu, pokud jsou pro něj zajímavé.

Pole `pivotSuggestions` obsahuje seznam segmentů (pivotů), na které se rozdělil původní dotaz. Pro každý pivot obsahuje odpověď seznam objektů [Query](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query_obj), které obsahují navrhované dotazy. Pole `text` obsahuje navrhovaný dotaz a pole `displayText` obsahuje termín, který nahradí pivot v původním dotazu. Například „Release Date of Surface“ (Datum vydání zařízení Surface).

Pomocí polí `text` a `thumbnail` můžete uživateli zobrazit řetězce dotazů pivotů pro případ, že ve skutečnosti hledá právě řetězec dotazu pivotu. Pomocí adresy URL `webSearchUrl` nebo `searchLink` můžete umožnit kliknutí na miniaturu a text. Pomocí hodnoty `webSearchUrl` můžete uživatele přesměrovat na výsledky hledání Bingu. Případně můžete použít hodnotu `searchLink`, pokud máte vlastní stránku výsledků.

<!-- Need a sanitized version of the image
The following shows an example of the pivot queries.

![Pivot suggestions](./media/cognitive-services-bing-images-api/bing-image-pivotsuggestion.GIF)
-->

## <a name="expanding-the-query"></a>Rozšíření dotazu

Pokud Bing dokáže rozšířením dotazu zúžit původní hledání, bude objekt [Images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) obsahovat pole `queryExpansions`. Pokud byl dotaz například *Microsoft Surface*, rozšířené dotazy můžou být Microsoft Surface **Pro 3**, Microsoft Surface **RT**, Microsoft Surface **Phone** a Microsoft Surface **Hub**.

Následující příklad ukazuje rozšířené dotazy pro dotaz *Microsoft Surface*.

```json
{
    "_type": "Images",
    "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=microsoft%20surface...",
    "totalEstimatedMatches": 1000,
    "value": [...],
    "queryExpansions":  [{
        "text": "Microsoft Surface Pro 3",
        "displayText": "Pro 3",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+Pro+3...",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=Microsoft...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+Pro+3..."
        }
    },
    {
        "text": "Microsoft Surface RT",
        "displayText": "RT",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+RT...",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+RT..."
        }
    },
    {
        "text": "Microsoft Surface Phone",
        "displayText": "Phone",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+Phone",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+Phone..."
        }
    }],
    "pivotSuggestions": [...],
    "nextOffsetAddCount": 0
}
```

Pole `queryExpansions` obsahuje seznam objektů [Query](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query_obj). Pole `text` obsahuje rozšířený dotaz a pole `displayText` obsahuje termín rozšíření. Pomocí polí `text` a `thumbnail` můžete uživateli zobrazit rozšířené řetězce dotazů pro případ, že ve skutečnosti hledá právě rozšířený řetězec dotazu. Pomocí adresy URL `webSearchUrl` nebo `searchLink` můžete umožnit kliknutí na miniaturu a text. Pomocí hodnoty `webSearchUrl` můžete uživatele přesměrovat na výsledky hledání Bingu. Případně můžete použít hodnotu `searchLink`, pokud máte vlastní stránku výsledků.

<!-- Removing until we can replace with a sanitized image.
The following shows an example Bing implementation that uses expanded queries. If the user clicks the Microsoft Surface Pro 3 link, they're taken to the Bing search results page, which shows them images of the Pro 3.

![Query expansion suggestions](./media/cognitive-services-bing-images-api/bing-image-queryexpansion.GIF)
-->

## <a name="throttling-requests"></a>Omezování požadavků

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Další kroky

Pokud chcete rychle začít s vaším prvním požadavkem, projděte si [rychlý start pro jazyk C#](quickstarts/csharp.md).

Seznamte se s referenčními informacemi k [rozhraní API Bingu pro vyhledávání obrázků v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference). Tyto referenční informace obsahují seznam koncových bodů, hlaviček a parametrů dotazů, které můžete použít při odesílání požadavků na výsledky hledání. Obsahují také definice objektů odpovědi.

Pokud chcete zlepšit uživatelské prostředí vyhledávacího pole, přečtěte si o [rozhraní API pro automatické návrhy Bingu](../bing-autosuggest/get-suggested-search-terms.md). Zatímco uživatel zadává termín dotazu, můžete zavoláním tohoto rozhraní API získat relevantní termíny dotazů, které použili jiní uživatelé.

Nezapomeňte si přečíst [požadavky Bingu na zobrazení a použití](./useanddisplayrequirements.md), abyste neporušili žádná pravidla používání výsledků hledání.

Když zavoláte rozhraní API Bingu pro vyhledávání obrázků, Bing vrátí seznam výsledků. Tento seznam je podmnožinou celkového počtu výsledků, které jsou pro dotaz relevantní. Pole `totalEstimatedMatches` v odpovědi obsahuje odhadovaný počet obrázků, které je možné zobrazit. Podrobnosti o procházení zbývajících obrázků najdete v tématu [Stránkování obrázků](./paging-images.md).