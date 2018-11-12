---
title: Získat Image z webu – rozhraní API pro vyhledávání obrázků Bingu
titleSuffix: Azure Cognitive Services
description: Pomocí rozhraní API Bingu pro vyhledávání obrázků vyhledat a získat určit prioritu relevantních obrázků z webu.
services: cognitive-services
author: aahill
manager: cgronlun
ms.assetid: AB1B9898-C94A-4B59-91A1-8623C94BA3D4
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: conceptual
ms.date: 8/8/2018
ms.author: aahi
ms.openlocfilehash: 621c2844b6952e5ea1f6b9f7c8bc2ed170fee310
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51246782"
---
# <a name="get-images-from-the-web-with-the-bing-image-search-api"></a>Získat Image z webu pomocí rozhraní API Bingu pro vyhledávání obrázků

Při použití rozhraní API REST vyhledávání obrázků Bingu, můžete získat Image z webu, které souvisejí s hledaný termín odesláním následujících požadavek GET:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

> [!IMPORTANT]
> * Všechny požadavky se musí provádět ze serveru a ne od klienta.
> * Pokud je vaše prvním voláním některé z rozhraní API pro vyhledávání Bingu, neobsahují záhlaví ID klienta. Pokud jste dříve označované jako Bing rozhraní API, které vrátí ID klienta pro uživatele a zařízení kombinaci pouze zahrnovat ID klienta.
> * Bitové kopie musí být zobrazena v pořadí poskytnutém v odpovědi.

## <a name="get-images-from-a-specific-web-domain"></a>Získání bitové kopie z konkrétní webové domény

Pokud chcete získat obrázky z konkrétní domény, použijte operátor dotazu [site:](https://msdn.microsoft.com/library/ff795613.aspx).

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

> [!NOTE]
> Odpovědi na dotazy, které používají `site:` operátor může zahrnovat obsah pro dospělé bez ohledu [bezpečné hledání](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#safesearch) nastavení. Používejte pouze `site:` Pokud si nejste vědomi obsah v doméně.

Následující příklad ukazuje, jak získat malé obrázky z webu ContosoSailing.com zjištěné Bingem během posledního týdne.  

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&size=small&freshness=week&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="filter-images"></a>Filtrovat Image

 Rozhraní API pro vyhledávání obrázků ve výchozím nastavení, vrátí všechny bitové kopie, které jsou relevantní pro dotaz. Pokud chcete filtrovat Image, které Bing vrátí (například vrátit pouze obrázky s průhledným pozadím nebo specifická velikost), použijte následující parametry dotazu:

* [aspekt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#aspect)– filtrovat imagí podle poměru stran (například obrázky standardní nebo široké obrazovky).
* [Barva](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#color)– filtrovat podle dominantní barvy nebo černá a bílá Image.
* [aktuálnost](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#freshness)– filtrovat podle stáří (třeba imagí za poslední týden zjišťování pomocí Bingu) Image.
* [Výška](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#height), [šířka](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#width)– filtrovat Image tak, že šířka a výška.
* [imageContent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagecontent)– filtrovat podle obsahu (například obrázky, které zobrazují pouze pro rozpoznávání tváře osoby) bitové kopie.
* [imageType](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetype)– filtrovat Image podle typu (například klipart, animovaných Gifů nebo průhledné pozadí).
* [licence](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#license)– filtrovat podle typu licence přiřazené k lokalitě Image.
* [velikost](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#size)– filtr imagí podle velikosti, jako je například malé obrázky až 200 x 200 pixelů.

Pokud chcete získat obrázky z konkrétní domény, použijte operátor dotazu [site:](https://msdn.microsoft.com/library/ff795613.aspx).

 > [!NOTE]
 > Odpovědi na dotazy, které používají `site:` operátor může zahrnovat obsah pro dospělé bez ohledu [bezpečné hledání](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#safesearch) nastavení. Používejte pouze `site:` Pokud si nejste vědomi obsah v doméně.

Následující příklad ukazuje, jak získat malé obrázky z ContosoSailing.com, které Bing zjištěno v minulém týdnu.  

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&size=small&freshness=week&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="bing-image-search-response-format"></a>Formát odpovědi Bingu pro vyhledávání obrázků

Obsahuje zprávy s odpovědí ze služby Bing [Imagí](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) odpověď, která obsahuje seznam imagí, které služby Cognitive Services, jestli se má být relevantní pro dotaz. Každý [Image](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image) objekt v seznamu obsahuje následující informace o imagi: adresa URL, jeho velikost, rozměry, jeho formát kódování, adresu URL na miniaturu obrázku a dimenze na miniaturu.

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

Když zavoláte rozhraní API Bingu pro vyhledávání obrázků, Bing vrátí seznam výsledků. Tento seznam je podmnožinou celkového počtu výsledků, které jsou pro dotaz relevantní. Pole `totalEstimatedMatches` v odpovědi obsahuje odhadovaný počet obrázků, které je možné zobrazit. Podrobnosti o tom, jak stránky postupujte podle zbývajících kroků imagí najdete v tématu [stránkování Imagí](../paging-images.md).

## <a name="next-steps"></a>Další postup

Pokud jste dosud API pro vyhledávání obrázků Bingu před, zkuste [rychlý Start](../quickstarts/csharp.md). Pokud hledáte něco složitějšího, projděte si kurz pro vytvoření [jednostránkovou webovou aplikaci](../tutorial-bing-image-search-single-page-app.md).
