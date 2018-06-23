---
title: Přehledné image | Microsoft Docs
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Ukazuje, jak používat rozhraní API služby Bing Image Search získat další informace o bitovou kopii.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 0BCD936E-D4C0-472D-AE40-F4B2AB6912D5
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: f651d9f773f475e633aed698e134aa6a7c07393b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343434"
---
# <a name="get-insights-about-an-image"></a>Získat informace o obrázku

> [!IMPORTANT]
> Místo použití nebo bitové kopie nebo podrobnosti koncového bodu získáte přehledy bitové kopie, měli byste použít [Visual vyhledávání](../bing-visual-search/overview.md) vzhledem k tomu, že poskytuje komplexní přehled.


Každý image obsahuje token statistiky, který vám pomůže získat informace o bitové kopii. Například můžete získat kolekci související Image, webové stránky, které zahrnují bitovou kopii nebo seznam obchodníků níž můžete zakoupit produktu znázorněno na obrázku.  
  
Pokud chcete získat přehled o bitovou kopii, zachycení image [imageInsightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-imageinsightstoken) tokenu v odpovědi. 

```json
"value" : [{
        . . .
        "name":"sailing dinghy.jpg",
        "imageInsightsToken" : "mid_D6426898706EC7..."
        "insightsSourcesSummary" : {
            "shoppingSourcesCount" : 9,
            "recipeSourcesCount" : 0
        },
        . . .
}],
```

V dalším kroku volání koncový bod podrobnosti bitové kopie a nastavení [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken) parametr k tokenu v dotazu `imageInsightsToken`.  

Chcete-li zadat přehledy, které chcete získat, nastavte `modules` parametr dotazu. Chcete-li přehledné, nastavte `modules` k `All`. Chcete-li získat pouze přehledy titulku a kolekce, nastavte `modules` k `Caption%2CCollection`. Úplný seznam možných statistiky, najdete v části [moduly](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modulesrequested). Ne všechny statistiky jsou k dispozici pro všechny Image. Odpověď obsahuje všechny přehledy, které jste požádali, pokud je k dispozici.

Následující příklad požádá o všechny dostupné statistiky pro předchozí bitovou kopii.

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&insightsToken=mid_D6426898706EC7...&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
``` 

## <a name="getting-insights-of-a-known-image"></a>Analyzovat známé bitové kopie

Pokud máte adresu URL pro bitovou kopii, kterou chcete získat informace o, použijte [imgUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imgurl) parametr místo dotazu [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken) parametru určete bitovou kopii. Nebo, pokud je soubor bitové kopie, může odesílat binárního souboru bitové kopie v textu požadavku POST. Pokud používáte požadavek POST `Content-Type` záhlaví musí být nastavena na `multipart/data-form`. U obou možností nesmí překročit velikost obrázku 1 MB.  
  
Pokud máte adresu URL do bitové kopie, následující příklad ukazuje, jak požádat o Statistika bitové kopie.

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&imgUrl=https%3A%2F%2Fwww.mydomain.com%2Fimages%2Fsunflower.png&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
``` 
  
## <a name="getting-all-image-insights"></a>Analyzovat bitové kopie  

Chcete-li požádat o všechny statistiky bitové kopie, nastavte [moduly](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modulesrequested) parametr k dotazu `All`. Chcete-li získat související hledání, žádost musí obsahovat řetězec dotazu uživatele. Tento příklad ukazuje, pomocí [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken) bitovou kopii.  
  
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&insightsToken=mid_68364D764J...&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

Objekt nejvyšší úrovně je [ImageInsightsResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsightsresponse) objektu místo [bitové kopie](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) objektu.  
  
```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "bcid_3297E6A54E4787A5F51C49D9BA342B9A*ccid_Fe2Hx...",
    "bestRepresentativeQuery" : {
        "text" : "Sailing Dinghy",
        "displayText" : "Sailing Dinghy",
        "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=Sailing+Dinghy...",
    },
    "pagesIncluding" : {
        "value" : [
            {
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=...",
                "name" : "Powerboating Dublin, Dinghy Sailing Courses...",
                "thumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OIP....",
                "datePublished" : "2017-01-20T00:41:00.0000000Z",
                "contentUrl" : "http:\/\/www.contoso.ie\/content...",
                "hostPageUrl" : "http:\/\/www.contoso.ie\/powerboating...",
                "contentSize" : "59063 B",
                "encodingFormat" : "jpeg",
                "hostPageDisplayUrl" : "www.contoso.ie\/powerboating...",
                "width" : 800,
                "height" : 600,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 225
                },
                "imageInsightsToken" : "ccid_pHjQIA0x*mid_17F61B1316A39C92214...",
                "imageId" : "17F61B1316A39C922143FFDE9DFB5B0FB41171",
                "accentColor" : "0997C2"
            },
            . . .
        ]
    },
    "relatedSearches" : {
        "value" : [
            {
                "text" : "Sailing Fun",
                "displayText" : "Sailing Fun",
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=Sailing...",
                "thumbnail" : {
                    "url" : "https:\/\/tse1.mm.bing.net\/th?q=Sailing+Fun..."
                }
            },
            . . .
        ]
    },
    "visuallySimilarImages" : {
        "value" : [
            {
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=...",
                "name" : "Weekend On the Water",
                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?id=OIP...",
                "datePublished" : "2010-09-05T12:00:00.0000000Z",
                "contentUrl" : "http:\/\/1.bp.contoso.com\/_dc_6...",
                "hostPageUrl" : "http:\/\/contoso.com\/2010...",
                "contentSize" : "203806 B",
                "encodingFormat" : "jpeg",
                "hostPageDisplayUrl" : "contoso.com\/2010...",
                "width" : 1600,
                "height" : 1249,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 234
                },
                "imageInsightsToken" : "ccid_Jg1Kwuc4*mid_5B7DA43976D3A422...",
                "imageId" : "5B7DA43976D3A422BA679A3AB019BB52C08DBC",
                "accentColor" : "0B2543"
            },
            . . .
        ]
    },
    "imageTags" : {
        "value" : [
            {
                "name" : "sail boat"
            },
            . . .
        ]
    }
}
```

## <a name="recognizing-entities-in-an-image"></a>Rozpozná entity v obrázku  

Funkce rozpoznávání entita identifikuje entity v bitové kopii, aktuálně jenom lidé. Chcete-li identifikovat entity v bitovou kopii, nastavte [moduly](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modulesrequested) parametr k dotazu `RecognizedEntities`.  

> [!NOTE]
> Tento modul nelze zadat s ostatní moduly. Pokud zadáte tento modul jiné moduly, odpověď neobsahuje rozpoznaný entity.  
  
Následující ukazuje, jak určit bitovou kopii pomocí [imgUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imgurl) parametr. Mějte na paměti, na adresu URL kódování parametry dotazu.  
  
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=faith+hill&insightsToken=mid_68364D764J...&modules=RecognizedEntities&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```  
  
Následující obrázek znázorňuje odpověď na předchozí požadavek. Vzhledem k tomu, že bitová kopie obsahuje dvou osob, odpověď identifikuje oblast pro každou osobu. V tomto případě byly ve skupině CelebrityAnnotations a CelebRecognitionAnnotations rozpoznány osoby. Bing obsahuje seznam uživatelů v každé skupině podle pravděpodobnost, že budou odpovídat osoby v původní bitové kopie. V seznamu je v sestupném pořadí spolehlivosti. Skupina CelebRecognitionAnnotations poskytuje nejvyšší úroveň spolehlivosti správnost shody.  
  
```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "ccid_ldi5nX38*mid_29470780DE0E6F969...",
    "recognizedEntityGroups" : {
        "value" : [
            {
                "recognizedEntityRegions" : [...],
                "name" : "CelebRecognitionAnnotations"
            },
            {
                "recognizedEntityRegions" : [...],
                "name" : "CelebrityAnnotations"
            }
        ]
    }
}
```
  
`region` Pole identifikuje oblasti obrázku kde Bing rozpoznána entity. Pro osoby představuje oblast obličeje.  
  
Hodnoty rámeček jsou relativní vzhledem k šířce a výšce původní bitové kopie a jsou v rozsahu 0,0 až 1,0. Například pokud bitovou kopii je 300 x 200 a oblasti top, rohu je v bodě (10, 20) a dolní pravém rohu je v bodě (290, 150), pak normalizovaný rámeček je:  
  
-   Doleva: 10 / 300 = 0.03333...  
-   Horní: 20 / 200 = 0,1  
-   Práva: 290 / 300 = 0.9667...  
-   Dolní: 150 / 200 = 0,75  
  
Můžete použít oblast, která vrátí Bing v následných Statistika volání. Chcete-li například získat vizuálně podobné obrázky rozpoznaný entity. Další informace najdete v tématu [oříznutí obrázků pro použití s vizuálně podobné a moduly rozpoznávání Entity](#croppingimages). Následující ukazuje mapování mezi pole oblast a parametry dotazu, že byste použili k oříznutí obrázků.  
  
-   Se mapuje na levé straně [kalendáře](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cal)  
-   TOP mapuje [cat](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cat)  
-   Mapuje vpravo [car](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#car)  
-   Dolů mapuje [souboru cab](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cab)  

## <a name="finding-visually-similar-images"></a>Hledání vizuálně podobné bitové kopie  

Chcete-li najít bitové kopie, které jsou vizuálně podobná původní bitové kopie, nastavte [moduly](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modulesrequested) parametr k SimilarImages dotazu.  
  
Následující požadavek ukazuje, jak získat vizuálně podobné bitové kopie. V požadavku použita [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken) dotaz k identifikaci původní bitové kopie. Pokud chcete zlepšit relevance, by měla obsahovat řetězec dotazu uživatele.  
  
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?insightsToken=mid_68364D764J...&modules=SimilarImages&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

  
Následující obrázek znázorňuje odpověď na předchozí požadavek.  
  
```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "ccid_ldi5nX38*mid_29470780DE0E6F969...",
    "visuallySimilarImages" : {
        "value" : [
            {
                "name" : "typical Hawaiian Sunset! :) | Scenes of Hawaii",
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=detailv2...",
                "thumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OIP.Mda2a86...",
                 . . .
            }
        ]
    }
```
  
## <a name="cropping-images-to-use-with-visually-similar-and-entity-recognition-modules"></a>Oříznutí obrázků pro použití s vizuálně podobné a moduly rozpoznávání entity  

Chcete-li určit oblasti bitové kopie Bingu používá pro určení, zda jsou obrázky vizuálně podobné nebo provedení rozpoznávání entity, použijte [cal](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cal), [cat](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cat), [souboru cab](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cab)a [car](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#car) parametrů dotazu. Ve výchozím nastavení používá Bing celého obrázku.  
  
Parametry zadejte horního, levém horním a dolním, pravém rohu oblasti, kterou používá Bing pro porovnání. Zadejte hodnoty jako zlomků šířky a výšky původní bitové kopie. Začínat desetinné číslo (0,0, 0,0) v horní, rohu a končit (1.0, 1.0) v pravém horním rohu. Například k určení, že top, levém dolním začne čtvrtletí způsob, jakým dolů z horní části a čtvrtletí způsobem z levé strany, nastavte `cal` k 0,25 a `cat` 0,25.  
  
K následujícímu pořadí volání ukazuje účinek zadání oblasti oříznutí. První volání nezahrnuje oříznutí a Bing rozpozná dvou osob stálého vedle sebe uprostřed bitovou kopii.  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?modules=RecognizedEntities&imgurl=https%3A%2F%2Ftse1.mm.bing.net%2Fth%3Fid%3DOIP.M0cbee6fadb43f35b2344e53da7a23ec1o0%26pid%3DApi&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```  

Odpověď obsahuje dvě rozpoznaný entity.  
  
```json 
{  
    "_type" : "ImageInsights",  
    "recognizedEntityGroups" : {
        "value": [  
            . . .  
            {  
                "recognizedEntityRegions" : [{  
                    "region" : {  
                        "left" : 0.5066667,  
                        "top" : 0.1955556,  
                        "right" : 0.75,  
                        "bottom" : 0.52  
                    },  
                    "matchingEntities" : [{  
                        "entity" : {  
                            "_type" : "Person",  
                            "name" : "Charlene Whitney",  
                            . . .  
                        },  
                        "matchConfidence" : 0.9961388  
                    }]  
                },  
                {  
                    "region" : {  
                        "left" : 0.25,  
                        "top" : 0.2488889,  
                        "right" : 0.4466667,  
                        "bottom" : 0.5111111  
                    },  
                    "matchingEntities" : [{  
                        "entity" : {  
                            "_type" : "Person",  
                            "name" : "Marcus Appel",  
                            . . .  
                        },  
                        "matchConfidence" : 0.9961388  
                    }]  
                }],  
            "name" : "CelebRecognitionAnnotations"
        }]
    }  
}  
```  
  
Druhé volání ořízne obrázek ve svislém směru dolů středu a Bing rozpoznány jedna osoba na pravé straně bitové kopie.  
  
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?cal=0.5&cat=0.0&car=1.0&cab=1.0&modules=RecognizedEntities&imgurl=https%3A%2F%2Ftse1.mm.bing.net%2Fth%3Fid%3DOIP.M0cbee6fadb43f35b2344e53da7a23ec1o0%26pid%3DApi&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

Odpověď obsahuje jednu entitu platný.  
  
```json  
{  
    "_type" : "ImageInsights",  
    "recognizedEntityGroups" : {
        "value" : [  
            . . .  
            {  
                "recognizedEntityRegions" : [{  
                    "region" : {  
                        "left" : 0.5066667,  
                        "top" : 0.1955556,  
                        "right" : 0.75,  
                        "bottom" : 0.52  
                    },  
                    "matchingEntities" : [{  
                        "entity" : {  
                            "_type" : "Person",  
                            "name" : "Charlene Whitney",  
                            . . .  
                        },  
                        "matchConfidence" : 0.9961388  
                    }]  
                }],  
                "name" : "CelebRecognitionAnnotations"  
            }
        ]
    }
}  
```  
  
## <a name="finding-visually-similar-products"></a>Hledání vizuálně podobné produkty  

Nastavením bitové kopie, které obsahují produkty, které jsou podobné vizuálně produkty najít v původní bitové kopie najdete [modueles](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modulesrequested) parametr k SimilarProducts dotazu.  
  
Následující požadavek ukazuje, jak získat bitové kopie vizuálně podobných produktů. V požadavku použita [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken) dotaz k identifikaci původní bitové kopie, kterou vrátil v předchozí požadavek. Pokud chcete zlepšit relevance, by měla obsahovat řetězec dotazu uživatele.  
  
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=anne+klein+dresses&modules=SimilarProducts&insightsToken=ccid_WOeyfoSp*mid_4B0A357&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```
  
Následující obrázek znázorňuje odpověď na předchozí požadavek. Odpověď obsahuje obrázek podobné produktu a určuje, kolik obchodníků nabízejí produktu online, jestli existují hodnocení produktu a najít nejnižší cenu (najdete v článku `aggregateOffer` pole).  
  
```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "ccid_ldi5nX38*mid_29470780DE0E6F969...",
    "visuallySimilarProducts" : {
        "value" : [
            {
                "name" : "Sequin One-Shoulder Twist-Drape Dress",  
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=de...",  
                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?id=OIP.M85bdee...",  
                . . .
            },
            . . .
        ]
    }
}
```
  
Získat seznam obchodníků, které nabízejí produktu online (najdete v článku [offerCount](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#offer-offercount) pole), volání rozhraní API znovu a nastavte `modules` k ShoppingSources. Potom nastavte `insightsToken` parametr dotazu na token nalezen v souhrnu bitovou kopii produktu.  
  
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?modules=ShoppingSources&insightsToken=ccid_hb3uRvUk*mid_BF5C252A47F2C765...&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

Toto je odpověď na předchozí požadavek.  
  
```json  
{  
    "_type" : "ImageInsights",  
    "shoppingSources" : {  
        "offers" : [{  
            "url" : "http:\/\/www.contoso.com\/dp\/B00O...",  
            "seller" : {  
                "name" : "Contoso",  
                "image" : {  
                    "url" : "https:\/\/tse3.mm.bing.net\/th?id=A10d50fe..."  
                }  
            },  
            "price" : 126.87,  
            "priceCurrency" : "USD",  
            "availability" : "InStock"  
        },  
        {  
            "url" : "http:\/\/www.adatum.com\/product\/heritage...\/",  
            "seller" : {  
                "name" : "fabrikam.com"  
            },  
            "price" : 495,  
            "priceCurrency" : "USD",  
            "availability" : "InStock"  
        }]  
    }  
}  
```
