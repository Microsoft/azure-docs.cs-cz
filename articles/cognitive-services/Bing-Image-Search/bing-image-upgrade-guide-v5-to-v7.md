---
title: Upgrade z rozhraní API Bingu pro vyhledávání obrázků V5 na v7
titleSuffix: Azure Cognitive Services
description: Tato příručka pro upgrade popisuje změny mezi verzí 5 a 7 rozhraní API Bingu pro vyhledávání obrázků. Tento průvodce vám pomůže identifikovat části aplikace, které potřebujete aktualizovat, aby používaly verzi 7.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 7F78B91F-F13B-40A4-B8A7-770FDB793F0F
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: scottwhi
ms.openlocfilehash: 0e8a3ec38a79edb52031d6c18596038ab4c6a8af
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2020
ms.locfileid: "94592137"
---
# <a name="bing-image-search-api-v7-upgrade-guide"></a>Průvodce upgradem rozhraní API Bingu pro vyhledávání obrázků v7

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](https://aka.ms/cogsvcs/bingmove)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](https://aka.ms/cogsvcs/bingmigration).

Tento průvodce upgradem identifikuje změny mezi verzemi 5 a verze 7 rozhraní API Bingu pro vyhledávání obrázků. Tento průvodce vám pomůže identifikovat části aplikace, které potřebujete aktualizovat, aby používaly verzi 7.

## <a name="breaking-changes"></a>Změny způsobující chyby

### <a name="endpoints"></a>Koncové body

- Číslo verze koncového bodu bylo změněno z verze 5 na v7. Například https: \/ /API.Cognitive.Microsoft.com/Bing/ \* \* v 7.0 * */images/Search.

### <a name="error-response-objects-and-error-codes"></a>Objekty a chybové kódy pro odpověď na chybu

- Všechny neúspěšné žádosti by nyní měly obsahovat `ErrorResponse` objekt v těle odpovědi.

- Do objektu byla přidána následující pole `Error` .  
  - `subCode`&mdash;Rozdělí kód chyby do diskrétních kontejnerů, pokud je to možné.
  - `moreDetails`&mdash;Další informace o chybě popsané v `message` poli


- Kódy chyb 5 nahradily následujícími možnými `code` hodnotami a `subCode` .

|Kód|Podřízeného kódu|Description
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|Bing vrátí ServerError vždy, když dojde ke kterékoli z podmínek dílčího kódu. Odpověď zahrnuje tyto chyby, pokud je stavový kód HTTP 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Blokované|Bing vrátí InvalidRequest, pokud jakákoli část požadavku není platná. Například povinný parametr chybí nebo hodnota parametru není platná.<br/><br/>Pokud se jedná o chybu ParameterMissing nebo ParameterInvalidValue, kód stavu HTTP je 400.<br/><br/>Pokud je chyba HttpNotAllowed, kód stavu HTTP 410.
|RateLimitExceeded||Bing vrátí RateLimitExceeded vždy, když překročíte kvótu dotazů za sekundu (QPS) nebo dotazů za měsíc (QPM).<br/><br/>Bing vrátí stavový kód HTTP 429, pokud jste překročili QPS a 403, pokud jste překročili QPM.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing vrátí InvalidAuthorization, když Bing nemůže ověřit volajícího. `Ocp-Apim-Subscription-Key`Hlavička například chybí nebo klíč předplatného není platný.<br/><br/>Redundance probíhá, pokud zadáte více než jednu metodu ověřování.<br/><br/>Pokud je chyba InvalidAuthorization, kód stavu HTTP je 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Bing vrátí InsufficientAuthorization, pokud volající nemá oprávnění pro přístup k prostředku. Tato situace může nastat, pokud byl klíč předplatného zakázán nebo vypršela jeho platnost. <br/><br/>Pokud je chyba InsufficientAuthorization, kód stavu HTTP je 403.

- Následující kód namapuje předchozí chybové kódy na nové kódy. Pokud jste se seznámili s kódy chyb V5, aktualizujte odpovídající kód.

|Kód verze 5|Kód verze 7. Subcode
|-|-
|RequestParameterMissing|InvalidRequest.ParameterMissing
RequestParameterInvalidValue|InvalidRequest.ParameterInvalidValue
ResourceAccessDenied|InsufficientAuthorization
ExceededVolume|RateLimitExceeded
ExceededQpsLimit|RateLimitExceeded
Zakázáno|InsufficientAuthorization.AuthorizationDisabled
UnexpectedError|ServerError. UnexpectedError
DataSourceErrors|ServerError. ResourceError
AuthorizationMissing|InvalidAuthorization.AuthorizationMissing
HttpNotAllowed|InvalidRequest.HttpNotAllowed
UserAgentMissing|InvalidRequest.ParameterMissing
NotImplemented|ServerError. NotImplemented
InvalidAuthorization|InvalidAuthorization
InvalidAuthorizationMethod|InvalidAuthorization
MultipleAuthorizationMethod|InvalidAuthorization.AuthorizationRedundancy
ExpiredAuthorizationToken|InsufficientAuthorization.AuthorizationExpired
InsufficientScope|InsufficientAuthorization
Blokované|InvalidRequest. Block



### <a name="query-parameters"></a>Parametry dotazů

- `modulesRequested`Parametr dotazu byl přejmenován na [moduly](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference).  

- Poznámky byly přejmenovány na značky. Podívejte se na téma parametr dotazu [modules](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) na značky.  

- Změnil se seznam podporovaných trhů hodnoty filtru ShoppingSources jenom na en-US. Viz [ImageType](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype).  


### <a name="image-insights-changes"></a>Změny v přehledech imagí

- Pole ImagesInsights bylo přejmenováno `annotations` [ImagesInsights](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse) na `imageTags` .  

- Objekt byl přejmenován `AnnotationModule` na [ImageTagsModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetagsmodule).  

- Přejmenuje `Annotation` objekt na [tag](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#tag)a odebralo se `confidence` pole.  

- `insightsSourcesSummary`Pole objektu [Image](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) bylo přejmenováno na `insightsMetadata` .  

- Objekt byl přejmenován `InsightsSourcesSummary` na [InsightsMetadata](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightsmetadata).  

- Byl přidán `https://api.cognitive.microsoft.com/bing/v7.0/images/details` koncový bod. Tento koncový bod použijte k vyžádání přehledů imagí místo koncového bodu/images/Search. Viz [přehledy imagí](./image-insights.md).

- Následující parametry dotazu jsou nyní platné pouze s `/images/details` koncovým bodem.  

    -   [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken)  
    -   [Aktualizuj](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)  
    -   [imgUrl](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imgurl)  
    -   [souborů](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cab)  
    -   [klientských](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cal)  
    -   [car](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#car)  
    -   [Cat](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cat)  
    -   [CT](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#ct)  

- Objekt byl přejmenován `ImageInsightsResponse` na [ImageInsights](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsights).  

- Změna datových typů následujících polí v objektu [ImageInsights](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsights) .  

    -   Změnili jste typ `relatedCollections` pole z `ImageGallery[]` na [RelatedCollectionsModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#relatedcollectionsmodule).  

    -   Změnili jste typ `pagesIncluding` pole z `Image[]` na [ImagesModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule).  

    -   Změnili jste typ `relatedSearches` pole z `Query[]` na [RelatedSearchesModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#relatedsearchesmodule).  

    -   Změnili jste typ `recipes` pole z `Recipe[]` na [RecipesModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#recipesmodule).  

    -   Změnili jste typ `visuallySimilarImages` pole z `Image[]` na [ImagesModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule).  

    -   Změnili jste typ `visuallySimilarProducts` pole z `ProductSummaryImage[]` na [ImagesModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule).  

    -   Odebrali jste `ProductSummaryImage` objekt a přesunuli pole související s produktem do objektu [obrázku](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) . `Image`Objekt zahrnuje pole související s produktem pouze v případě, že je obrázek zahrnut jako součást vizuálně podobných produktů v odpovědi na obrázek Insight.  

    -   Změnili jste typ `recognizedEntityGroups` pole z `RecognizedEntityGroup[]` na [RecognizedEntitiesModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#recognizedentitiesmodule).  

-   Přejmenovalo `categoryClassification` pole [ImageInsights](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse) na `annotations` a změnil jeho typ na `AnnotationsModule` .  

### <a name="images-answer"></a>Odpovědi na obrázky

-   Z [obrázků](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images)byla odebrána pole DisplayShoppingSourcesBadges a displayRecipeSourcesBadges.  

-   Pole obrázků bylo přejmenováno `nextOffsetAddCount` [Images](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) na `nextOffset` . Způsob, jakým používáte posun, se také změnil. Dříve jste nastavili parametr [posunutí](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#offset) dotazu na `nextOffsetAddCount` hodnotu plus hodnotu předchozí posunutí a počet obrázků ve výsledku. Nyní nastavíte `offset` `nextOffset` hodnotu.  


## <a name="non-breaking-changes"></a>Neprůlomové změny

### <a name="query-parameters"></a>Parametry dotazů

- Přidat transparentní jako možnou hodnotu filtru [ImageType](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype) Transparentní filtr vrátí pouze obrázky s průhledným pozadím.

- Přidání jakékoli jako možné hodnoty filtru [licence](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#license) . Libovolný filtr vrátí pouze bitové kopie, které jsou v licenci.

- Přidání parametrů dotazu [maxFileSize](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxfilesize) a [minFileSize](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minfilesize) Pomocí těchto filtrů můžete vracet obrázky v rámci rozsahu velikostí souborů.  

- Přidání parametrů dotazu [maxHeight](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxheight), [minHeight](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minheight), [MaxWidth](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxwidth), [minWidth](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minwidth) Pomocí těchto filtrů můžete vracet obrázky v rámci rozsahu výšek a šířek.  

### <a name="object-changes"></a>Změny objektu

- Do `description` `lastUpdated` objektu [nabídky](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#offer) byly přidány pole a.  

- Pole se přidalo `name` do objektu [ImageGallery](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagegallery) .  

- Přidáno `similarTerms` do objektu [images](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) . Toto pole obsahuje seznam termínů, které jsou ve smyslu řetězce dotazu uživatele podobné.