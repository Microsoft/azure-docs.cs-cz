---
title: Získejte přehledy obrázků - API pro vyhledávání obrázků Bingu
titleSuffix: Azure Cognitive Services
description: Další informace o použití rozhraní API Bingu pro vyhledávání obrázků, chcete-li získat další informace o obrázku.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 0BCD936E-D4C0-472D-AE40-F4B2AB6912D5
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: b2dfbeaae46be58e94f6c3cfc5bbd97d82382e54
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55876602"
---
# <a name="get-image-insights-with-the-bing-image-search-api"></a>Získání přehledů image pomocí rozhraní API Bingu pro vyhledávání obrázků

> [!IMPORTANT]
> Namísto použití podrobnosti endpoint/imagí a získat přehledy obrázků, byste měli použít [vizuálního vyhledávání](../bing-visual-search/overview.md) vzhledem k tomu, že nabízí komplexnější přehledy.


Každý image obsahuje token insights, který vám pomůže získat informace o imagi. Například můžete získat kolekce imagí souvisejících, webové stránky, které obsahují image, nebo seznam si obchodníci můžou kterého si můžete koupit produktu je znázorněno na obrázku.  

Chcete-li získat přehled o bitovou kopii, zachycení image [imageInsightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-imageinsightstoken) tokenu v odpovědi.

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

V dalším kroku volání Image podrobnosti koncového bodu a nastavit [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken) parametr k tokenu v dotazu `imageInsightsToken`.  

K určení přehledy, které chcete načíst, nastavit `modules` parametr dotazu. Chcete-li získat všechny přehledy, nastavte `modules` k `All`. Chcete-li získat jenom titulek a shromažďování přehledů, nastavte `modules` k `Caption%2CCollection`. Úplný seznam možných insights najdete v tématu [moduly](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modulesrequested). Ne všechny přehledy jsou k dispozici pro všechny bitové kopie. Odpověď obsahuje všechny přehledy, které jste požádali, pokud je k dispozici.

Následující příklad požádá o všechny přehledy k dispozici pro na předchozím obrázku.

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&insightsToken=mid_D6426898706EC7...&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

## <a name="getting-insights-of-a-known-image"></a>Získávají se přehledy známé bitové kopie

Pokud máte adresu URL pro bitovou kopii, kterou chcete získat poznatky o, použijte [imgUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imgurl) parametr místo dotazu [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken) parametr určete bitovou kopii. Nebo, pokud máte soubor bitové kopie, může odesílat binární soubor bitové kopie v těle požadavku POST. Pokud používáte požadavek POST `Content-Type` záhlaví musí být nastaveno na `multipart/data-form`. Obě možnosti velikost bitové kopie nesmí překročit 1 MB.  

Pokud máte adresu URL obrázku, následující příklad ukazuje, jak požádat o poznatky o bitovou kopii.

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&imgUrl=https%3A%2F%2Fwww.mydomain.com%2Fimages%2Fsunflower.png&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

## <a name="getting-all-image-insights"></a>Získání všech přehledy obrázků  

Chcete-li požádat o všechny přehledy bitovou kopii, nastavte [moduly](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modulesrequested) parametr do dotazu `All`. Chcete-li získat související hledání, žádost musí obsahovat řetězec dotazu. Tento příklad ukazuje použití [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken) k zadání image.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&insightsToken=mid_68364D764J...&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

Objekt nejvyšší úrovně je [ImageInsightsResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsightsresponse) místo objektu [Imagí](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) objektu.  

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

## <a name="recognizing-entities-in-an-image"></a>Rozpoznávání entit v obrázku  

Funkce rozpoznávání entit identifikuje entity v bitové kopie, aktuálně jenom lidé. K identifikaci entity v bitovou kopii, nastavte [moduly](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modulesrequested) parametr do dotazu `RecognizedEntities`.  

> [!NOTE]
> Tento modul nelze zadat s ostatní moduly. Pokud zadáte tento modul s ostatními moduly, odpověď neobsahuje rozpoznaný entity.  

Následující ukazuje, jak pomocí určete bitovou kopii [imgUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imgurl) parametru. Pamatovat si adresy URL kódování parametry dotazu.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=faith+hill&insightsToken=mid_68364D764J...&modules=RecognizedEntities&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```  

Následující příklad ukazuje odpověď na předchozí požadavek. Vzhledem k tomu, že bitová kopie obsahuje dva uživatelé, odpověď identifikuje oblast pro každou osobu. V takovém případě lidé nebyly rozpoznány ve skupinách CelebrityAnnotations a CelebRecognitionAnnotations. Bing obsahuje seznam uživatelů v každé skupině na základě pravděpodobnosti, že budou odpovídat osoby v původní bitové kopie. V seznamu je v sestupném pořadí výrazné zvýšení sebedůvěry. Skupina CelebRecognitionAnnotations poskytuje nejvyšší úroveň spolehlivosti, že shoda není správný.  

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

`region` Pole označuje oblast obrázku, kde Bingu rozpoznán entity. U lidí představuje oblast tváře osoby.  

Hodnoty obdélník jsou relativní vzhledem k šířku a výšku původní bitové kopie a jsou v rozmezí 0,0 až 1,0. Například pokud bitová kopie je 300 x 200 a zároveň klauzuli top oblasti, levém rohu se v okamžiku (10, 20) a dolního okraje, pravém rohu v okamžiku (290, 150), pak normalizované obdélníku je:  

-   Left: 10 / 300 = 0.03333...  
-   TOP:  20 / 200 = 0.1  
-   Right: 290 / 300 = 0.9667...  
-   Dole: 150 / 200 = 0.75  

Můžete použít oblast, která vrací Bingu ve voláních další přehledy. Chcete-li například získat vizuálně podobné obrázky rozpoznaný entity. Další informace najdete v tématu oříznutí Image na použití s moduly rozpoznávání entit a vizuálně podobné. Následuje ukázka mapování polí oblast a parametry dotazu, můžete použít k oříznutí obrázků.  

-   Vlevo se mapuje na [cal](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cal)  
-   TOP mapuje [cat](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cat)  
-   Vpravo se mapuje na [Auto](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#car)  
-   Mapuje se na dolní [souboru cab](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cab)  

## <a name="finding-visually-similar-images"></a>Hledání vizuálně podobné obrázky  

Chcete-li najít Image, které jsou vizuálně podobné původní bitové kopie, nastavte [moduly](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modulesrequested) parametr k SimilarImages dotazu.  

Následující požadavek ukazuje, jak získat vizuálně podobné obrázky. Požadavek používá [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken) parametr k identifikaci původní bitové kopie dotazu. Pokud chcete zlepšit relevanci, by měl obsahovat řetězec dotazu.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?insightsToken=mid_68364D764J...&modules=SimilarImages&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```


Následující příklad ukazuje odpověď na předchozí požadavek.  

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

## <a name="cropping-images-to-use-with-visually-similar-and-entity-recognition-modules"></a>Oříznutí obrázků pro použití s vizuálně podobné a moduly rozpoznávání entit  

Chcete-li zadat tuto oblast obrázku, který Bing používá k určení, zda jsou vizuálně podobné obrázky nebo k provádění rozpoznávání entit, použijte [cal](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cal), [cat](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cat), [cab](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cab)a [car](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#car) parametrů dotazu. Ve výchozím nastavení používá Bing celého obrázku.  

Parametry zadejte horní, levého horního a dolního, pravém rohu oblasti, které Bing používá k porovnání. Zadejte hodnoty jako podíly šířky a výšky původní bitové kopie. Desetinné hodnoty začínat (0.0, 0.0) nahoře, levém rohu a končí (1.0; 1,0) v pravém dolním rohu. Například pokud chcete určit, že horním levém horním začne čtvrtletí způsob, jakým dolů z horní části a čtvrtletí způsob na levé straně, nastavte `cal` na 0,25 a `cat` 0,25.  

Následující sekvence volání demonstruje účinek zadání oříznutí oblasti. První volání nezahrnuje oříznutí a Bing rozpozná dva lidé vedle sebe, abyste se uprostřed bitovou kopii.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?modules=RecognizedEntities&imgurl=https%3A%2F%2Ftse1.mm.bing.net%2Fth%3Fid%3DOIP.M0cbee6fadb43f35b2344e53da7a23ec1o0%26pid%3DApi&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```  

Odpověď ukazuje dvě entity, které rozpoznaná.  

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

Druhé volání obrázek ořízne tak, svisle dolů střed a Bing rozpoznán na pravé straně bitové kopie jedné osobě.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?cal=0.5&cat=0.0&car=1.0&cab=1.0&modules=RecognizedEntities&imgurl=https%3A%2F%2Ftse1.mm.bing.net%2Fth%3Fid%3DOIP.M0cbee6fadb43f35b2344e53da7a23ec1o0%26pid%3DApi&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

Odpověď ukazuje rozpoznaný jednu entitu.  

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

Chcete-li najít Image, které obsahují produkty, které jsou vizuálně podobné produkty v původní bitové kopie, nastavte [modueles](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modulesrequested) parametr k SimilarProducts dotazu.  

Následující požadavek ukazuje, jak získat Image vizuálně podobných produktů. Požadavek používá [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken) dotazu parametr k identifikaci původní obrázek, který byl vrácen do předchozí žádosti. Pokud chcete zlepšit relevanci, by měl obsahovat řetězec dotazu.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=anne+klein+dresses&modules=SimilarProducts&insightsToken=ccid_WOeyfoSp*mid_4B0A357&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

Následující příklad ukazuje odpověď na předchozí požadavek. Odpověď obsahuje obrázek podobné produktu a označuje, kolik si obchodníci můžou nabízet produktu online, zda existují hodnocení produktu a najít nejnižší cenu (viz `aggregateOffer` pole).  

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

Chcete-li získat seznam obchodníci, které nabízejí online produktu (naleznete v tématu [offerCount](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) pole), volání rozhraní API znovu a nastavte `modules` k ShoppingSources. Potom nastavte `insightsToken` parametru dotazu na token součástí produktu souhrnného obrázku.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?modules=ShoppingSources&insightsToken=ccid_hb3uRvUk*mid_BF5C252A47F2C765...&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

Tady je odpověď na předchozí požadavek.  

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
