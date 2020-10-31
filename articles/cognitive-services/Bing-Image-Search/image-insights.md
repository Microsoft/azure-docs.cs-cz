---
title: Získat přehledy imagí – rozhraní API Bingu pro vyhledávání obrázků
titleSuffix: Azure Cognitive Services
description: Naučte se používat rozhraní API Bingu pro vyhledávání obrázků k získání dalších informací o imagi.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 0BCD936E-D4C0-472D-AE40-F4B2AB6912D5
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: scottwhi
ms.openlocfilehash: cb5da2e3009ca1c50cac05d1bd1f4a8eb55d97d2
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93084285"
---
# <a name="get-image-insights-with-the-bing-image-search-api"></a>Získejte přehledy imagí pomocí rozhraní API Bingu pro vyhledávání obrázků

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](https://aka.ms/cogsvcs/bingmove)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](https://aka.ms/cogsvcs/bingmigration).

> [!IMPORTANT]
> Místo použití koncového bodu/images/Details k získání přehledů imagí byste měli použít [vizuální vyhledávání](../bing-visual-search/overview.md) , protože poskytuje komplexnější přehledy.


Každý obrázek obsahuje token Insights, který můžete použít k získání informací o imagi. Můžete například získat kolekci souvisejících imagí, webových stránek, které obsahují obrázek, nebo seznam obchodníků, u kterých si můžete koupit produkt zobrazený v imagi.  

Pokud chcete získat přehled o obrázku, Zachyťte token [imageInsightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image-imageinsightstoken) obrázku v odpovědi.

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

Dále zavolejte koncový bod podrobností obrázku a nastavte parametr dotazu [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) na token v `imageInsightsToken` .  

Chcete-li určit přehledy, které chcete získat, nastavte `modules` parametr dotazu. Pokud chcete získat všechny přehledy, nastavte `modules` na `All` . Pokud chcete získat jenom přehledy titulků a kolekcí, nastavte `modules` na `Caption%2CCollection` . Úplný seznam možných přehledů najdete v tématu [moduly](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested). Ne všechny přehledy jsou k dispozici pro všechny image. Odpověď zahrnuje všechny informace, které jste požadovali, pokud jsou k dispozici.

Následující příklad vyžádá všechny dostupné přehledy pro předchozí obrázek.

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&insightsToken=mid_D6426898706EC7...&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

## <a name="getting-insights-of-a-known-image"></a>Získání přehledů o známé imagi

Pokud máte adresu URL obrázku, na který chcete získat přehledy, použijte k určení obrázku parametr dotazu [imgUrl](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imgurl) namísto parametru [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) . Nebo, pokud máte soubor s obrázkem, můžete v těle žádosti POST odeslat binární obrázek. Pokud použijete požadavek POST, `Content-Type` musí být záhlaví nastavené na `multipart/data-form` . U obou možností nemůže být velikost obrázku větší než 1 MB.  

Pokud máte adresu URL obrázku, následující příklad ukazuje, jak si vyžádat přehledy o obrázku.

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&imgUrl=https%3A%2F%2Fwww.mydomain.com%2Fimages%2Fsunflower.png&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

## <a name="getting-all-image-insights"></a>Získání přehledů obrázků  

Pokud si chcete vyžádat všechny přehledy o imagi, nastavte parametr dotazu [moduly](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) na `All` . Chcete-li získat související hledání, musí požadavek zahrnovat řetězec dotazu uživatele. Tento příklad ukazuje použití [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) k určení obrázku.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&insightsToken=mid_68364D764J...&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

Objekt nejvyšší úrovně je objekt [ImageInsightsResponse](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse) místo objektu [images](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) .  

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

Funkce rozpoznávání entit identifikuje entity v imagi, aktuálně pouze pro lidi. Pro identifikaci entit v imagi nastavte parametr dotazu [moduly](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) na `RecognizedEntities` .  

> [!NOTE]
> Tento modul nesmíte zadat s žádným jiným modulem. Pokud tento modul zadáte s jinými moduly, odpověď neobsahuje rozpoznané entity.  

Následující příklad ukazuje, jak určit obrázek pomocí parametru [imgUrl](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imgurl) . Nezapomeňte zakódovat parametry dotazu v adrese URL.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=faith+hill&insightsToken=mid_68364D764J...&modules=RecognizedEntities&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```  

Následující příklad ukazuje odpověď na předchozí požadavek. Vzhledem k tomu, že obrázek obsahuje dva lidi, odpověď identifikuje oblast pro každou osobu. V takovém případě byli uživatelé rozpoznáni ve skupinách CelebrityAnnotations a CelebRecognitionAnnotations. Bing vypíše lidi v každé skupině na základě pravděpodobnosti, že odpovídají osobě v původní imagi. Seznam je v sestupném pořadí podle spolehlivosti. Skupina CelebRecognitionAnnotations poskytuje nejvyšší úroveň jistoty, že shoda je správná.  

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

`region`Pole určuje oblast obrázku, kde Bing rozpoznal entitu. Pro lidi oblast představuje plochu osoby.  

Hodnoty obdélníku jsou relativní vzhledem k šířce a výšce původní image a jsou v rozsahu 0,0 až 1,0. Například pokud je obrázek 300x200 a horní roh oblasti je v bodu (10, 20) a dolním pravém horním rohu je bod (290, 150), pak je normalizovaný obdélník:  

-   Vlevo: 10/300 = 0,03333...  
-   Horní: 20/200 = 0,1  
-   Right: 290/300 = 0,9667...  
-   Dolní: 150/200 = 0,75  

V následných voláních Insights můžete použít oblast, kterou Bing vrátí. Například pro získání vizuálně podobných obrázků rozpoznané entity. Další informace najdete v tématu ořezávání obrázků pro použití s vizuálně podobnými moduly a moduly pro rozpoznávání entit. Následující obrázek znázorňuje mapování mezi poli oblasti a parametry dotazu, které byste použili k oříznutí obrázků.  

-   Vlevo se mapuje na [CAL](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cal) .  
-   Horní mapy na [Cat](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cat)  
-   Vpravo se mapuje na [auto](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#car) .  
-   Dolní mapování na soubor [CAB](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cab)  

## <a name="finding-visually-similar-images"></a>Hledání vizuálně podobných obrázků  

Chcete-li najít obrázky, které jsou vizuálně podobné původnímu obrázku, nastavte parametr dotazu [modules](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) na SimilarImages.  

Následující požadavek ukazuje, jak získat vizuálně podobné obrázky. Požadavek používá parametr dotazu [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) k identifikaci původního obrázku. Chcete-li zlepšit relevanci, měli byste zahrnout řetězec dotazu uživatele.  

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

## <a name="cropping-images-to-use-with-visually-similar-and-entity-recognition-modules"></a>Oříznutí obrázků pro použití s vizuálně podobnými moduly a moduly pro rozpoznávání entit  

Chcete-li určit oblast obrázku, kterou Bing používá k určení, zda jsou obrázky vizuálně podobné nebo aby prováděla rozpoznávání entit, použijte parametry dotazu [CAL](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cal), [Cat](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cat), [CAB](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cab)a [auto](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#car) . Ve výchozím nastavení Bing používá celý obrázek.  

Parametry určují horní, levý a dolní roh v pravém horním rohu oblasti, kterou Bing používá pro porovnání. Zadejte hodnoty jako zlomky šířky a výšky původní bitové kopie. Desetinné hodnoty začínají na (0,0, 0,0) nahoře, v levém horním rohu a končí (1,0, 1,0) v pravém dolním rohu. Například chcete-li určit, že horní, levý roh začíná čtvrtletím směrem dolů od začátku a čtvrtletí od levé strany, nastavte `cal` na 0,25 a `cat` 0,25.  

Následující posloupnost volání znázorňuje účinek určení oblasti oříznutí. První volání nezahrnuje oříznutí a Bing rozpoznává dvě lidi po stranách uprostřed obrázku.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?modules=RecognizedEntities&imgurl=https%3A%2F%2Ftse1.mm.bing.net%2Fth%3Fid%3DOIP.M0cbee6fadb43f35b2344e53da7a23ec1o0%26pid%3DApi&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```  

V odpovědi se zobrazí dvě rozpoznané entity.  

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

Druhé volání ořízne obrázek vertikálně uprostřed a Bing rozpoznal jednu osobu na pravé straně obrázku.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?cal=0.5&cat=0.0&car=1.0&cab=1.0&modules=RecognizedEntities&imgurl=https%3A%2F%2Ftse1.mm.bing.net%2Fth%3Fid%3DOIP.M0cbee6fadb43f35b2344e53da7a23ec1o0%26pid%3DApi&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

Odpověď zobrazuje jednu rozpoznanou entitu.  

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

## <a name="finding-visually-similar-products"></a>Hledání vizuálně podobných produktů  

Chcete-li najít obrázky obsahující produkty, které jsou vizuálně podobné produktům, které jsou k dispozici v původní imagi, nastavte parametr dotazu [modules](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) na SimilarProducts.  

Následující požadavek ukazuje, jak získat obrázky vizuálně podobných produktů. Požadavek používá parametr dotazu [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) k identifikaci původní image, která byla vrácena v předchozí žádosti. Chcete-li zlepšit relevanci, měli byste zahrnout řetězec dotazu uživatele.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=anne+klein+dresses&modules=SimilarProducts&insightsToken=ccid_WOeyfoSp*mid_4B0A357&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

Následující příklad ukazuje odpověď na předchozí požadavek. Odpověď obsahuje obrázek podobného produktu a označuje, kolik obchodníků nabízí produkt online, zda jsou k dispozici hodnocení produktu a nejnižší cena se našla (viz `aggregateOffer` pole).  

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

Pokud chcete získat seznam obchodníků, kteří nabízejí produkt online (viz pole [offerCount](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) ), zavolejte rozhraní API znovu a nastavte `modules` na ShoppingSources. Potom nastavte `insightsToken` parametr dotazu na token, který byl nalezen v obrázku souhrnu produktu.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?modules=ShoppingSources&insightsToken=ccid_hb3uRvUk*mid_BF5C252A47F2C765...&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

Následuje odpověď na předchozí požadavek.  

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
