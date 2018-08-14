---
title: Získávání imagí z webu pomocí rozhraní API Bingu pro vyhledávání obrázků | Dokumentace Microsoftu
description: Použití rozhraní API pro vyhledávání obrázků Bingu pro vyhledávání a určit prioritu relevantních obrázků získat z webu.
services: cognitive-services
author: aahill
manager: cgronlun
ms.assetid: AB1B9898-C94A-4B59-91A1-8623C94BA3D4
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: conceptual
ms.date: 8/8/2018
ms.author: aahi
ms.openlocfilehash: c379cc2dfbe53f83e4d79500cd947879407c8d55
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2018
ms.locfileid: "40082600"
---
# <a name="getting-images-from-the-web-with-the-bing-image-search-api"></a>Získávání imagí z webu pomocí rozhraní API Bingu pro vyhledávání obrázků

Pomocí API REST pro vyhledávání obrázků Bingu, můžete získat Image z webu, která se vztahují na uživatele hledaný termín odesláním následujících požadavek GET:

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

## <a name="getting-images-from-a-specific-web-domain"></a>Získání bitové kopie z konkrétní webové domény

Pokud chcete získat obrázky z konkrétní domény, použijte operátor dotazu [site:](http://msdn.microsoft.com/library/ff795613.aspx).

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

## <a name="filtering-images"></a>Filtrování obrázků

 Ve výchozím nastavení rozhraní API pro vyhledávání obrázků vrátí všechny bitové kopie, které jsou relevantní pro dotaz. Pokud chcete filtrovat obrazy, které Bing vrátí (například vrátí pouze obrázky na pozadí transparant nebo specifická velikost), můžete použít tyto parametry dotazu:

* [aspect](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#aspect) – filtrování obrázků podle poměru stran (například standardní nebo širokoúhlé obrázky)
* [color](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#color) – filtrování obrázků podle dominantní barvy nebo černé a bílé
* [freshness](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#freshness) – filtrování obrázků podle stáří (například obrázky zjištěné Bingem během posledního týdne)
* [height](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#height), [width](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#width) – filtrování obrázků podle šířky a výšky
* [imageContent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagecontent) – filtrování obrázků podle obsahu (například obrázky jen s lidským obličejem)
* [imageType](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetype) – filtrování obrázků podle typu (například klipart, animovaný GIF nebo průhledné pozadí)
* [license](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#license) – filtrování obrázků podle typu licence přidružené k příslušnému webu
* [size](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#size) – filtrování obrázků podle velikosti (například malé obrázky do velikosti 200 × 200 pixelů)

Pokud chcete získat obrázky z konkrétní domény, použijte operátor dotazu [site:](http://msdn.microsoft.com/library/ff795613.aspx).

    > [!NOTE]
    > Responses to queries using the `site:` operator may include adult content regardless of the [safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#safesearch) setting. Only use `site:` if you are aware of the content on the domain.

Následující příklad ukazuje, jak získat malé obrázky z webu ContosoSailing.com zjištěné Bingem během posledního týdne.  

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&size=small&freshness=week&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="bing-image-search-response-format"></a>Formát odpovědi Bingu pro vyhledávání obrázků

Obsahuje zprávy s odpovědí ze služby Bing [Imagí](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) odpověď, která obsahuje seznam imagí, které služby Azure cognitive services, jestli se má být relevantní pro dotaz. Každý [Image](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image) objekt v seznamu obsahuje následující informace o imagi: adresa URL, jeho velikost, rozměry, jeho formát kódování, adresu URL na miniaturu obrázku a dimenze na miniaturu.

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

Když zavoláte rozhraní API Bingu pro vyhledávání obrázků, Bing vrátí seznam výsledků. Tento seznam je podmnožinou celkového počtu výsledků, které jsou pro dotaz relevantní. Pole `totalEstimatedMatches` v odpovědi obsahuje odhadovaný počet obrázků, které je možné zobrazit. Podrobnosti o procházení zbývajících obrázků najdete v tématu [Stránkování obrázků](../paging-images.md).

## <a name="next-steps"></a>Další postup

Pokud jste dosud API pro vyhledávání obrázků Bingu před, zkuste [rychlý Start](../quickstarts/csharp.md). Pokud hledáte něco složitějšího, projděte si kurz pro vytvoření [jednostránkovou webovou aplikaci](../tutorial-bing-image-search-single-page-app.md).
