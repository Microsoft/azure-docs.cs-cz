---
title: Upgrade z Bingu v5 rozhraní API pro vyhledávání na v7 obrázku
titleSuffix: Azure Cognitive Services
description: Tento průvodce upgradem popisuje změny mezi verzí 5 a verze 7 rozhraní API Bingu pro vyhledávání obrázků. Tento průvodce vám pomůže identifikovat části aplikace, které je potřeba aktualizovat na použití verze 7.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 7F78B91F-F13B-40A4-B8A7-770FDB793F0F
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 4cd77dc6572ed100a7bef34829ed17579e3b5491
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55876778"
---
# <a name="bing-image-search-api-upgrade-guide"></a>Průvodce upgradem API pro vyhledávání obrázků Bingu

Tento průvodce upgradem identifikuje změny mezi verzí 5 a verze 7 rozhraní API Bingu pro vyhledávání obrázků. Tento průvodce vám pomůže identifikovat části aplikace, které je potřeba aktualizovat na použití verze 7.

## <a name="breaking-changes"></a>Změny způsobující chyby

### <a name="endpoints"></a>Koncové body

- Číslo verze koncový bod se změní z v5 na v7. Například https://api.cognitive.microsoft.com/bing/\ * \*v7.0**/images/search.

### <a name="error-response-objects-and-error-codes"></a>Objekty odpovědi chyby a chybové kódy

- Všechny neúspěšné žádosti by teď měl obsahovat `ErrorResponse` objektu v textu odpovědi.

- Přidat následující pole `Error` objektu.  
  - `subCode`&mdash;Oddíly kód chyby: do samostatných sad, tj. Pokud je to možné
  - `moreDetails`&mdash;Další informace o chybě popsaných v `message` pole


- Nahradí chybové kódy v5 následujícího `code` a `subCode` hodnoty.

|Kód|Podřízeného|Popis
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>Neimplementováno|Bing vrátí ServerError pokaždé, když dojde k některé z podmínek dílčí kód. Odpověď obsahuje tyto chyby, pokud je stavový kód HTTP 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Blokováno|Bing vrátí InvalidRequest pokaždé, když libovolnou část žádosti není platný. Například povinný parametr chybí nebo není platná hodnota parametru.<br/><br/>Pokud je chyba ParameterMissing nebo ParameterInvalidValue, je stavový kód HTTP 400.<br/><br/>Pokud je chyba HttpNotAllowed, stavový kód HTTP 410.
|RateLimitExceeded||Bing vrátí RateLimitExceeded pokaždé, když překročíte dotazů za sekundu (QPS) nebo dotazů za měsíc (QPM) kvóty.<br/><br/>Bing vrátí stavový kód HTTP 429, pokud se překročí QPS a 403 překročení QPM.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing vrátí InvalidAuthorization při Bingu se nemůže ověřit volající. Například `Ocp-Apim-Subscription-Key` záhlaví chybí nebo není platný klíč předplatného.<br/><br/>Redundance nastane, pokud zadáte více než jednu metodu ověřování.<br/><br/>Pokud je chyba InvalidAuthorization, je stavový kód HTTP 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Bing vrátí InsufficientAuthorization volající nemá oprávnění k přístupu k prostředku. Tato situace může nastat, pokud klíč předplatného se zakázalo, nebo vypršela platnost. <br/><br/>Pokud je chyba InsufficientAuthorization, je stavový kód HTTP 403.

- Následující mapuje předchozích kódů chyb nové kódy. Pokud jste pořídili závislost na kódy chyb v5, aktualizujte svůj kód odpovídajícím způsobem.

|Kód verze 5|Verze 7 code.subCode
|-|-
|RequestParameterMissing|InvalidRequest.ParameterMissing
RequestParameterInvalidValue|InvalidRequest.ParameterInvalidValue
ResourceAccessDenied|InsufficientAuthorization
ExceededVolume|RateLimitExceeded
ExceededQpsLimit|RateLimitExceeded
Zakázáno|InsufficientAuthorization.AuthorizationDisabled
UnexpectedError|ServerError.UnexpectedError
DataSourceErrors|ServerError.ResourceError
AuthorizationMissing|InvalidAuthorization.AuthorizationMissing
HttpNotAllowed|InvalidRequest.HttpNotAllowed
UserAgentMissing|InvalidRequest.ParameterMissing
Neimplementováno|ServerError.NotImplemented
InvalidAuthorization|InvalidAuthorization
InvalidAuthorizationMethod|InvalidAuthorization
MultipleAuthorizationMethod|InvalidAuthorization.AuthorizationRedundancy
ExpiredAuthorizationToken|InsufficientAuthorization.AuthorizationExpired
InsufficientScope|InsufficientAuthorization
Blokováno|InvalidRequest.Blocked



### <a name="query-parameters"></a>Parametry dotazu

- Přejmenovat `modulesRequested` parametr do dotazu [moduly](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference).  

- Přejmenovat poznámky se značkami. Zobrazit [moduly](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) parametr se značkami dotazu.  

- Seznam podporované trhy hodnota filtru ShoppingSources změnit na pouze en US. Zobrazit [imageType](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetype).  


### <a name="image-insights-changes"></a>Změny pro insights bitové kopie

- Přejmenovat `annotations` pole [ImagesInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsightsresponse) k `imageTags`.  

- Přejmenovat `AnnotationModule` objektu [ImageTagsModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetagsmodule).  

- Přejmenovat `Annotation` do objektu [značky](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#tag)a odeberou `confidence` pole.  

- Přejmenovat `insightsSourcesSummary` pole [Image](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image) objektu `insightsMetadata`.  

- Přejmenovat `InsightsSourcesSummary` objektu [InsightsMetadata](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightsmetadata).  

- Přidá `https://api.cognitive.microsoft.com/bing/v7.0/images/details` koncového bodu. Použijte tento koncový bod pro přehledy obrázků žádosti namísto hledání endpoint/imagí /. Zobrazit [obrázku Insights](./image-insights.md).

- Tyto parametry dotazu jsou nyní platné jen spolu s `/images/details` koncového bodu.  

    -   [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken)  
    -   [Moduly](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)  
    -   [imgUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imgurl)  
    -   [cab](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cab)  
    -   [licence CAL](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cal)  
    -   [Auto](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#car)  
    -   [cat](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cat)  
    -   [ct](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#ct)  

- Přejmenovat `ImageInsightsResponse` objektu [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsights).  

- Změnila datové typy z těchto polí v [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsights) objektu.  

    -   Změnit typ `relatedCollections` pole z `ImageGallery[]` k [RelatedCollectionsModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#relatedcollectionsmodule).  

    -   Změnit typ `pagesIncluding` pole z `Image[]` k [ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagesmodule).  

    -   Změnit typ `relatedSearches` pole z `Query[]` k [RelatedSearchesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#relatedsearchesmodule).  

    -   Změnit typ `recipes` pole z `Recipe[]` k [RecipesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#recipesmodule).  

    -   Změnit typ `visuallySimilarImages` pole z `Image[]` k [ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagesmodule).  

    -   Změnit typ `visuallySimilarProducts` pole z `ProductSummaryImage[]` k [ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagesmodule).  

    -   Odebrat `ProductSummaryImage` objektu a pole týkající se produktu do přesunout [Image](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image) objektu. `Image` Objekt zahrnuje pole týkající se produktu pouze v případě, že na obrázku je součástí vizuálně podobných produktů v přehledu odpovědi bitové kopie.  

    -   Změnit typ `recognizedEntityGroups` pole z `RecognizedEntityGroup[]` k [RecognizedEntitiesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#recognizedentitiesmodule).  

-   Přejmenovat `categoryClassification` pole [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsightsresponse) k `annotations`a změnit jeho typ `AnnotationsModule`.  

### <a name="images-answer"></a>Odpověď imagí

-   Odebrat pole displayShoppingSourcesBadges a displayRecipeSourcesBadges z [image](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images).  

-   Přejmenovat `nextOffsetAddCount` pole [image](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) k `nextOffset`. Způsob, jak použít posun byl také změněn. Dříve jste nastavili [posun](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#offset) parametr k dotazu `nextOffsetAddCount` hodnota plus předchozí hodnotu posunu plus počet imagí ve výsledku. Nyní nastavte `offset` k `nextOffset` hodnotu.  


## <a name="non-breaking-changes"></a>Non nejnovější změny

### <a name="query-parameters"></a>Parametry dotazu

- Přidat Transparent jako možný výskyt [imageType](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetype) hodnota filtru. Transparentní filtr vrátí pouze obrázky s průhledným pozadím.

- Přidat nějaké jako možný výskyt [licence](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#license) hodnota filtru. Libovolný filtr vrátí pouze bitové kopie, které jsou v rámci licence.

- Přidá [maxFileSize](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#maxfilesize) a [minFileSize](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#minfilesize) parametrů dotazu. Tyto filtry použijte k vrácení obrázků v rámci škála velikostí souborů.  

- Přidá [maxHeight](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#maxheight), [minHeight](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#minheight), [maxWidth](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#maxwidth), [hodnota minWidth](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#minwidth) parametrů dotazu. Tyto filtry použijte k vrácení bitové kopie do rozsahu výšky a šířky.  

### <a name="object-changes"></a>Změny objektu

- Přidá `description` a `lastUpdated` polím [nabízejí](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#offer) objektu.  

- Přidá `name` pole [ImageGallery](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagegallery) objektu.  

- Přidání `similarTerms` k [Imagí](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) objektu. Toto pole obsahuje seznam termínů, které jsou podobné jako u význam, aby řetězce dotazu.  
