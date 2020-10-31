---
title: Získání imagí z webu rozhraní API Bingu pro vyhledávání obrázků
titleSuffix: Azure Cognitive Services
description: K vyhledání a získání relevantních imagí z webu použijte rozhraní API Bingu pro vyhledávání obrázků.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: AB1B9898-C94A-4B59-91A1-8623C94BA3D4
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: b414711f5589a141d59fbe2f14d0a8ae992f5acf
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93084438"
---
# <a name="get-images-from-the-web-with-the-bing-image-search-api"></a>Získat obrázky z webu pomocí rozhraní API Bingu pro vyhledávání obrázků

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](https://aka.ms/cogsvcs/bingmove)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](https://aka.ms/cogsvcs/bingmigration).

Když použijete REST API Vyhledávání obrázků Bingu, můžete získat obrázky z webu, které souvisejí s hledaným termínem, odesláním následující žádosti GET:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Použijte parametr dotazu [q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query) pro hledaný výraz v kódování URL. Pokud například zadáte *plaveb dinghies* , nastavte `q` na `sailing+dinghies` nebo `sailing%20dinghies` .

> [!IMPORTANT]
> * Všechny požadavky musí být provedeny ze serveru, nikoli z klienta.
> * Pokud zavoláte kterékoli rozhraní API pro vyhledávání Bingu, nezahrnete do něj hlavičku ID klienta. ID klienta uveďte jenom v případě, že jste předtím volali rozhraní API Bingu, které vrátilo ID klienta pro kombinaci uživatelů a zařízení.

## <a name="get-images-from-a-specific-web-domain"></a>Získání imagí z konkrétní webové domény

Pokud chcete získat obrázky z konkrétní domény, použijte operátor dotazu [site:](https://msdn.microsoft.com/library/ff795613.aspx).

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

> [!NOTE]
> Odpovědi na dotazy, které používají `site:` operátor, mohou zahrnovat obsah pro dospělé bez ohledu na nastavení [bezpečné hledání](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#safesearch) . Používejte pouze v případě, že víte `site:` o obsahu v doméně.

## <a name="filter-images"></a>Filtrovat obrázky

 Ve výchozím nastavení vrátí rozhraní Vyhledávání obrázků API všechny obrázky, které jsou pro dotaz relevantní. Pokud chcete filtrovat obrázky, které Bing vrátí (například pro vrácení pouze obrázků s průhledným pozadím nebo konkrétní velikostí), použijte následující parametry dotazu:

* [aspekt](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#aspect)– filtruje obrázky podle poměru stran (například standardních nebo širokoúhlých imagí obrazovky).
* [Color](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#color)– filtruje obrázky podle dominantní barvy nebo černou a bílá.
* [aktuálnost](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#freshness)– filtruje image podle stáří (například obrázky zjištěné bingem za minulý týden).
* [Height](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#height)( [Šířka](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#width)) – filtruje obrázky podle šířky a výšky.
* [imageContent](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagecontent)– filtruje obrázky podle obsahu (například obrázky, které zobrazují pouze plochu osoby).
* [ImageType](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype)– filtruje obrázky podle typu (například kliparty, animované soubory GIF nebo průhledné pozadí).
* [licence](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#license)– filtruje image podle typu licence přidružené k lokalitě.
* [Size](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#size)– filtruje obrázky podle velikosti, například malých obrázků až po 200x200 pixelů.

Pokud chcete získat obrázky z konkrétní domény, použijte operátor dotazu [site:](https://msdn.microsoft.com/library/ff795613.aspx).

Následující příklad ukazuje, jak získat malé obrázky z ContosoSailing.com, které Bing zjistil za minulý týden.  

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&size=small&freshness=week&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="bing-image-search-response-format"></a>Formát odpovědi Vyhledávání obrázků Bingu

Zpráva odpovědi z Bingu obsahuje odpověď na [obrázky](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) obsahující seznam imagí, které Cognitive Services určily jako relevantní pro dotaz. Každý objekt [obrázku](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) v seznamu obsahuje následující informace o obrázku: adresu URL, její velikost, její rozměry, formát kódování, adresu URL miniatury obrázku a rozměry miniatury.

> [!NOTE]
> * Obrázky musí být zobrazeny v pořadí, v jakém jsou uvedeny v odpovědi.
> * Vzhledem k tomu, že se formáty a parametry adresy URL mohou změnit bez předchozího upozornění, použijte všechny adresy URL tak, jak jsou. Nemusíte přebírat závislosti na formátu nebo parametrech adresy URL s výjimkou případů uvedených v popsaných.

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

Když zavoláte rozhraní API Bingu pro vyhledávání obrázků, Bing vrátí seznam výsledků. Tento seznam je podmnožinou celkového počtu výsledků, které jsou pro dotaz relevantní. Pole `totalEstimatedMatches` v odpovědi obsahuje odhadovaný počet obrázků, které je možné zobrazit. Podrobnosti o tom, jak stránky procházet zbývajícími obrázky, najdete v tématu [obrázky stránkování](../paging-images.md).

## <a name="next-steps"></a>Další kroky

Pokud jste rozhraní API Bingu pro vyhledávání obrázků ještě nezkoušeli, vyzkoušejte si [rychlý Start](../quickstarts/csharp.md). Pokud hledáte něco složitějšího, zkuste vytvořit [jednostránkovou webovou aplikaci](../tutorial-bing-image-search-single-page-app.md)pomocí kurzu.
