---
title: Upgrade z rozhraní API pro vyhledávání obrázků Bingu v5 na 7
titleSuffix: Azure Cognitive Services
description: Tento průvodce upgradem popisuje změny mezi verzí 5 a verzí 7 rozhraní API pro vyhledávání obrázků Bingu. V této příručce můžete identifikovat části aplikace, které je třeba aktualizovat, abyste měli používat verzi 7.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 7F78B91F-F13B-40A4-B8A7-770FDB793F0F
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: scottwhi
ms.openlocfilehash: c4c6b95996206cfb38ea3f77b89c3ebe3c2c0026
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "68883491"
---
# <a name="bing-image-search-api-v7-upgrade-guide"></a>Průvodce upgradem rozhraní API pro vyhledávání obrázků bingu v7

Tato příručka pro upgrade identifikuje změny mezi verzí 5 a verzí 7 rozhraní API pro vyhledávání obrázků Bingu. V této příručce můžete identifikovat části aplikace, které je třeba aktualizovat, abyste měli používat verzi 7.

## <a name="breaking-changes"></a>Změny způsobující chyby

### <a name="endpoints"></a>Koncové body

- Číslo verze koncového bodu se změnilo z verze v5 na v7. Například https:\//api.cognitive.microsoft.com/bing/\*\*v7.0**/images/search.

### <a name="error-response-objects-and-error-codes"></a>Objekty odpovědí na chybu a kódy chyb

- Všechny neúspěšné požadavky by `ErrorResponse` nyní měly obsahovat objekt v těle odpovědi.

- Do objektu byla `Error` přidána následující pole.  
  - `subCode`&mdash;Rozdělí kód chyby do samostatných bloků, pokud je to možné
  - `moreDetails`&mdash;Další informace o chybě popsané `message` v poli


- Kódy chyb v5 byly nahrazeny následujícími možnými `code` hodnotami a `subCode` hodnotami.

|kód|Dílčí kód|Popis
|-|-|-
|Chyba serveru|Neočekávaná chyba<br/>Chyba zdroje<br/>Není implementováno|Bing vrátí chybu serveru vždy, když dojde k některé z podmínek podkódu. Odpověď zahrnuje tyto chyby, pokud je stavový kód HTTP 500.
|Neplatný požadavek|ParametrMissing ParametrMissing ParametrMissing ParametrMissing<br/>ParametrInvalidValue<br/>HttpNotAllowed<br/>Blokované|Bing vrátí InvalidRequest vždy, když žádná část požadavku není platná. Například chybí požadovaný parametr nebo hodnota parametru není platná.<br/><br/>Pokud je chyba ParametrMissing nebo ParameterInvalidValue, je stavový kód HTTP 400.<br/><br/>Pokud je chyba HttpNotAllowed, stavový kód HTTP 410.
|RateLimit překročena||Bing vrátí RateLimitExceeded vždy, když překročíte vaše dotazy za sekundu (QPS) nebo dotazy za měsíc (QPM) kvóta.<br/><br/>Bing vrátí stavový kód HTTP 429, pokud jste překročili QPS a 403, pokud jste překročili QPM.
|Neplatná autorizace|AutorizaceChybí<br/>Redundance autorizace|Bing vrátí InvalidAuthorization, když Bing nemůže ověřit volajícího. Například `Ocp-Apim-Subscription-Key` záhlaví chybí nebo klíč předplatného není platný.<br/><br/>K redundanci dochází, pokud zadáte více než jednu metodu ověřování.<br/><br/>Pokud je chyba InvalidAuthorization, stavový kód HTTP je 401.
|Nedostatečná autorizace|Autorizace zakázána.<br/>Platnost oprávnění vypršela.|Bing vrátí InsufficientAuthorization, pokud volající nemá oprávnění k přístupu k prostředku. Tato situace může nastat, pokud byl zakázán klíč předplatného nebo vypršela jeho platnost. <br/><br/>Pokud je chyba InsufficientAuthorization, stavový kód HTTP je 403.

- Následující mapy předchozí kódy chyb na nové kódy. Pokud jste přijali závislost na kódech chyb v5, aktualizujte kód odpovídajícím způsobem.

|Kód verze 5|Kód 7 verze.subCode
|-|-
|RequestParameterMissing|InvalidRequest.ParameterMissing
RequestParameterInvalidValue|Neplatný parametr Invalid.Neplatný parametr
ResourceAccessDenied|Nedostatečná autorizace
ExceededVolume|RateLimit překročena
Limit překročil qpsLimit|RateLimit překročena
Zakázáno|InsufficientAuthorization.Authorization.AuthorizationDisabled
Neočekávaná chyba|Chyba_serveru_nechybě
Chyby zdroje dat|Chyba_serveru.ResourceError
AutorizaceChybí|InvalidAuthorization.AuthorizationMissing
HttpNotAllowed|InvalidRequest.httpNotAllowed
UserAgentMissing|InvalidRequest.ParameterMissing
Není implementováno|ServerError.NotImplemented
Neplatná autorizace|Neplatná autorizace
Metoda Neplatnostauthorizationm|Neplatná autorizace
Metoda multipleAuthorizationMethod|InvalidAuthorization.AuthorizationRedundancy
Platnost tokenu AuthorizationToken|InsufficientAuthorization.AuthorizationVypršela
Nedostatečný rozsah|Nedostatečná autorizace
Blokované|Neplatný požadavek.blokován



### <a name="query-parameters"></a>Parametry dotazu

- Byl přejmenován na parametr dotazu `modulesRequested` na [moduly](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference).  

- Přejmenována na poznámky na tagy. Viz [parametr dotazu modulů](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) na tags.  

- Seznam podporovaných trhů s hodnotou filtru ShoppingSources byl změněn pouze na hodnotu en-US. Viz [imageType](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype).  


### <a name="image-insights-changes"></a>Změny přehledů obrázků

- Přejmenováno `annotations` pole [ImagesInsights](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse) na `imageTags`.  

- Objekt byl `AnnotationModule` přejmenován na [ImageTagsModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetagsmodule).  

- Přejmenoval `Annotation` objekt na [Tag](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#tag)a `confidence` pole odebral.  

- Přejmenováno `insightsSourcesSummary` pole [Image](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) objektu `insightsMetadata`Image na .  

- Objekt byl `InsightsSourcesSummary` přejmenován na [InsightsMetadata](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightsmetadata).  

- Byl `https://api.cognitive.microsoft.com/bing/v7.0/images/details` přidán koncový bod. Tento koncový bod slouží k vyžádání přehledů obrázků namísto koncového bodu /images/search. Viz [Přehledy obrázků](./image-insights.md).

- Následující parametry dotazu jsou nyní `/images/details` platné pouze s koncovým bodem.  

    -   [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken)  
    -   [Moduly](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)  
    -   [imgUrl](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imgurl)  
    -   [Cab](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cab)  
    -   [Cal](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cal)  
    -   [car](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#car)  
    -   [Kočka](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cat)  
    -   [Ct](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#ct)  

- Objekt byl `ImageInsightsResponse` přejmenován na [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsights).  

- Změněny datové typy následujících polí v objektu [ImageInsights.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsights)  

    -   Typ pole byl `relatedCollections` změněn `ImageGallery[]` z [modulu RelatedCollectionsModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#relatedcollectionsmodule).  

    -   Typ pole byl `pagesIncluding` změněn `Image[]` z funkce [ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule).  

    -   Typ pole byl `relatedSearches` změněn `Query[]` z [modulu RelatedSearchesModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#relatedsearchesmodule).  

    -   Typ pole byl `recipes` změněn `Recipe[]` z [modulu RecipesModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#recipesmodule).  

    -   Typ pole byl `visuallySimilarImages` změněn `Image[]` z funkce [ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule).  

    -   Typ pole byl `visuallySimilarProducts` změněn `ProductSummaryImage[]` z funkce [ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule).  

    -   Objekt `ProductSummaryImage` byl odebrán a pole související s produktem přesunuta do objektu [Obrázek.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) Objekt `Image` obsahuje pole související s produktem pouze v případě, že je obrázek zahrnut jako součást vizuálně podobných produktů v odpovědi na přehled obrázku.  

    -   Typ pole byl `recognizedEntityGroups` změněn `RecognizedEntityGroup[]` z [modulu RecognizedEntitiesModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#recognizedentitiesmodule).  

-   Přejmenoval `categoryClassification` pole [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse) `annotations`na a změnil `AnnotationsModule`jeho typ na .  

### <a name="images-answer"></a>Odpověď na obrázky

-   Odstraněna displayShoppingSourcesBadges a displayRecipeSourcesBadges pole z [obrázků](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images).  

-   Přejmenováno `nextOffsetAddCount` pole [Obrázky](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) na `nextOffset`. Změnil se také způsob použití odsazení. Dříve jste nastavili parametr [posunového](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#offset) dotazu na hodnotu `nextOffsetAddCount` plus předchozí hodnotu posunu plus počet obrazů ve výsledku. Nyní můžete `offset` nastavit `nextOffset` hodnotu.  


## <a name="non-breaking-changes"></a>Nenarušující změny

### <a name="query-parameters"></a>Parametry dotazu

- Přidána hodnota filtru Průhledná jako možný [obrázekTyp](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype) filtru. Filtr Průhledná vrátí pouze obrazy s průhledným pozadím.

- Jako možnou hodnotu filtru licence byla přidána hodnota filtru [libovolné licence.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#license) Filtr Any vrátí pouze obrázky, které jsou pod licencí.

- Byly přidány parametry dotazu [maxFileSize](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxfilesize) a [minFileSize.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minfilesize) Tyto filtry slouží k vracení obrazů v rozsahu velikostí souborů.  

- Byly přidány parametry dotazu [maxHeight](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxheight), [minHeight](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minheight), [maxWidth](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxwidth), [minWidth.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minwidth) Tyto filtry slouží k vracení obrazů v rozsahu výšek a šířek.  

### <a name="object-changes"></a>Změny objektů

- Do `description` objektu `lastUpdated` [Offer](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#offer) byla přidána pole a.  

- Pole `name` bylo přidáno do objektu [ImageGallery.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagegallery)  

- Přidáno `similarTerms` do objektu [Obrazy.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) Toto pole obsahuje seznam termínů, které mají podobný význam jako řetězec dotazu uživatele.  
