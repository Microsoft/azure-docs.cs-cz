---
title: Upgrade Bing bitové kopie v5 vyhledávání rozhraní API pro v7 | Microsoft Docs
description: Identifikuje části aplikace, které je potřeba aktualizovat na použití verze 7.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 7F78B91F-F13B-40A4-B8A7-770FDB793F0F
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: b4d785eafe9ced6fb12e2dac3282dfd286849eb6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342513"
---
# <a name="image-search-api-upgrade-guide"></a>Průvodce upgradem rozhraní API služby Search bitové kopie

Tento průvodce upgradem identifikuje změny mezi 5 a verze 7 rozhraní API pro vyhledávání bitové kopie Bingu. Pomocí Tento průvodce vám pomůže identifikovat části aplikace, které je potřeba aktualizovat na použití verze 7.

## <a name="breaking-changes"></a>Změny způsobující chyby

### <a name="endpoints"></a>Koncové body

- Číslo verze pro koncový bod změněn z v5 na v7. Například https://api.cognitive.microsoft.com/bing/\ * \*v7.0**/images/search.

### <a name="error-response-objects-and-error-codes"></a>Chyba odpovědi objekty a kódy chyb

- Teď by měla obsahovat všechny neúspěšné žádosti `ErrorResponse` objektu v textu odpovědi.

- Přidat následující pole do `Error` objektu.  
  - `subCode`&mdash;Oddíly. kód chyby do diskrétní sad, pokud je to možné
  - `moreDetails`&mdash;Další informace o této chybě podrobněji popsaná `message` pole
   

- Kódy chyb v5 nahradí následující možné `code` a `subCode` hodnoty.

|Kód|Dílčí|Popis
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>Neimplementováno|Bing vrátí ServerError vždy, když nastane některá z podmínek dílčí kódu. Odpověď obsahuje tyto chyby, pokud je stavový kód protokolu HTTP 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Blokováno|Bing vrátí InvalidRequest vždy, když libovolná součást žádosti není platný. Například chybí povinný parametr nebo hodnota parametru není platný.<br/><br/>Pokud je chyba ParameterMissing nebo ParameterInvalidValue, je stavový kód HTTP 400.<br/><br/>Pokud je chyba HttpNotAllowed, stavový kód protokolu HTTP 410.
|RateLimitExceeded||Bing vrátí RateLimitExceeded vždy, když překročí dotazů za sekundu (QPS) nebo dotazů za měsíc (QPM) kvóty.<br/><br/>Bing vrátí stavový kód HTTP 429, pokud se překročí QPS a 403 překračování QPM.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing vrátí InvalidAuthorization, pokud Bing nemůže ověřit volající. Například `Ocp-Apim-Subscription-Key` chybí hlavička nebo klíč předplatného není platný.<br/><br/>Pokud zadáte více než jednu metodu ověřování, dojde k redundance.<br/><br/>Pokud je chyba InvalidAuthorization, je stavový kód HTTP 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Bing vrátí InsufficientAuthorization, pokud má volající nemá oprávnění pro přístup k prostředku. Tato situace může nastat, pokud klíč předplatného je zakázané nebo vypršela platnost. <br/><br/>Pokud je chyba InsufficientAuthorization, je stavový kód HTTP 403.

- Následující mapuje předchozí kódy chyb nové kódy. Pokud jste prováděné závislost na kódy chyb v5, aktualizujte kód odpovídajícím způsobem.

|Verze 5 kódu|Verze 7 code.subCode
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

- Přejmenován `modulesRequested` parametr k dotazu [moduly](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modules).  
  
- Přejmenovat poznámky na základě značek. V tématu [moduly](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modules) parametr na základě značek dotazu.  

- Seznam podporovaných trhů hodnota filtru ShoppingSources změnit na pouze en US. V tématu [imageType](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetype).  
 

### <a name="image-insights-changes"></a>Změny Statistika bitové kopie
  
- Přejmenován `annotations` pole z [ImagesInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsightsresponse) k `imageTags`.  
  
- Přejmenován `AnnotationModule` do objektu [ImageTagsModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetagsmodule).  
  
- Přejmenován `Annotation` do objektu [značka](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#tag)a odebrat `confidence` pole.  
  
- Přejmenován `insightsSourcesSummary` pole z [Image](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image) do objektu `insightsMetadata`.  
  
- Přejmenován `InsightsSourcesSummary` do objektu [InsightsMetadata](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightsmetadata).  
  
- Přidat `https://api.cognitive.microsoft.com/bing/v7.0/images/details` koncový bod. Místo endpoint hledání nebo bitové kopie nebo použijte tento koncový bod na žádosti o statistiku bitové kopie. V tématu [bitové kopie Insights](./image-insights.md). 
  
- Následující parametry dotazu jsou nyní je platný pouze `/images/details` koncový bod.  
  
    -   [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken)  
    -   [moduly](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modules)  
    -   [imgUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imgurl)  
    -   [soubor CAB](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cab)  
    -   [kalendáře](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cal)  
    -   [Auto](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#car)  
    -   [CAT](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cat)  
    -   [Berte](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#ct)  
  
- Přejmenován `ImageInsightsResponse` do objektu [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsights).  
  
- Změnit datové typy následující pole v [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsights) objektu.  
  
    -   Změnit typ `relatedCollections` pole z `ImageGallery[]` k [RelatedCollectionsModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#relatedcollectionsmodule).  
  
    -   Změnit typ `pagesIncluding` pole z `Image[]` k [ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagesmodule).  
  
    -   Změnit typ `relatedSearches` pole z `Query[]` k [RelatedSearchesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#relatedsearchesmodule).  
  
    -   Změnit typ `recipes` pole z `Recipe[]` k [RecipesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#recipesmodule).  
  
    -   Změnit typ `visuallySimilarImages` pole z `Image[]` k [ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagesmodule).  
  
    -   Změnit typ `visuallySimilarProducts` pole z `ProductSummaryImage[]` k [ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagesmodule).  
  
    -   Odebrat `ProductSummaryImage` objektu a přesunout do pole týkající se produktu [Image](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image) objektu. `Image` Objekt zahrnuje i pole týkající se produktu jenom v případě, že bitová kopie je součástí vizuálně podobné produkty v odpovědi přehledy bitové kopie.  
  
    -   Změnit typ `recognizedEntityGroups` pole z `RecognizedEntityGroup[]` k [RecognizedEntitiesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#recognizedentitiesmodule).  
  
-   Přejmenován `categoryClassification` pole z [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsightsresponse) k `annotations`a změnit jeho typ `AnnotationsModule`.  

### <a name="images-answer"></a>Bitové kopie odpovědí

-   Odebere pole displayShoppingSourcesBadges a displayRecipeSourcesBadges z [bitové kopie](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images).  
  
-   Přejmenován `nextOffsetAddCount` pole z [bitové kopie](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) k `nextOffset`. Změnil také způsobu, jakým používáte posun. Dříve jste nastavili [posun](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#offset) parametr k dotazu `nextOffsetAddCount` hodnotu a předchozí hodnotu posunutí plus počet bitových kopií ve výsledku. Nyní nastavte `offset` k `nextOffset` hodnotu.  
    
  
## <a name="non-breaking-changes"></a>Bez nejnovější změny

### <a name="query-parameters"></a>Parametry dotazu
  
- Přidat průhledný jako možného [imageType](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetype) hodnota filtru. Transparentní filtr vrací pouze obrázky s průhledným pozadím.

- Přidat všechny jako možného [licence](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#license) hodnota filtru. Libovolný filtr vrací pouze bitové kopie, které jsou v rámci licence.
  
- Přidat [maxFileSize](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#maxfilesize) a [minFileSize](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#minfilesize) parametrů dotazu. Tyto filtry lze použijte k vrácení bitové kopie do rozsahu velikosti souborů.  
  
- Přidat [maxHeight](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#maxheight), [minHeight](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#minheight), [maxWidth](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#maxwidth), [hodnota minWidth](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#minwidth) parametrů dotazu. Tyto filtry lze použijte k vrácení bitové kopie do rozsahu výšky a šířky.  

### <a name="object-changes"></a>Změny objektu
  
- Přidat `description` a `lastUpdated` polí k [nabízejí](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#offer) objektu.  
  
- Přidat `name` do [ImageGallery](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagegallery) objektu.  
  
- Přidat `similarTerms` k [bitové kopie](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) objektu. Toto pole obsahuje seznam termínů, které se podobají význam řetězcem dotazu uživatele.  
